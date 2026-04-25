---
name: 代码查看器设计
description: HTML网页代码查看器，支持语法高亮、代码折叠、主题切换等功能
type: project
---

# 代码查看器设计文档

## 项目概述

开发一个单HTML网页代码查看器，用户粘贴代码后自动识别语言并展示，提供便捷的代码阅读体验。

## 功能需求

### 核心功能

| 功能 | 描述 | 实现方式 |
|------|------|----------|
| 代码粘贴 | 用户粘贴代码到编辑器 | Monaco Editor 输入区域 |
| 语言选择 | 用户选择或自动检测编程语言 | 语言下拉选择 + 启发式检测（可选） |
| 语法高亮 | 根据语言显示彩色代码 | Monaco Editor 语法高亮 |
| 行号显示 | 左侧显示代码行号 | Monaco Editor 内置 |
| 代码折叠 | 折叠函数、类、代码块 | Monaco Editor 内置折叠功能 |
| 主题切换 | 深色/浅色主题切换 | 滑动开关 + Monaco 主题API |
| 字体调整 | 字体和字号调整 | 下拉选择 + 滑动调节 |
| 一键复制 | 复制全部代码 | Clipboard API |
| 高亮配色 | 多种预设配色主题 | Monaco defineTheme API |

### 支持的语言

Monaco Editor 内置支持 80+ 种语言，主要包括：
- JavaScript, TypeScript, Python, Java, C/C++, C#
- Go, Rust, PHP, Ruby, Swift, Kotlin
- HTML, CSS, SCSS, LESS, XML, JSON, YAML
- Markdown, SQL, Shell, PowerShell
- 等

### 预设主题

| 主题名称 | 风格 |
|----------|------|
| VS Dark | 深色，VS Code 默认 |
| VS Light | 浅色，VS Code 默认 |
| Monokai | 深色，经典 Sublime |
| Solarized Dark | 深色，护眼配色 |
| One Dark Pro | 深色，Atom 风格 |
| Dracula | 深色，流行配色 |

## 页面结构

```
┌──────────────────────────────────────────────────────────────────────────┐
│ [语言▼] [字体▼] [字号:12─●─24] [主题▼] [🌙/☀️] [📋复制]           │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  Monaco Editor 代码区域                                                   │
│  - 行号显示（左侧）                                                        │
│  - 语法高亮                                                                │
│  - 代码折叠指示器                                                          │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

### 工具栏组件详情

**语言选择（下拉）**
- 默认显示 "自动检测"
- 支持手动选择：JavaScript, Python, Java, C++, Go, TypeScript, HTML, CSS, JSON, Markdown 等
- 启发式检测：粘贴代码后，根据关键字特征推测语言并自动切换下拉选项
- 用户可手动覆盖自动检测结果

**字体选择（下拉）**
- Consolas（默认）
- Monaco
- Source Code Pro
- Fira Code
- JetBrains Mono

**字号调节（滑动）**
- 范围：12px - 24px
- 默认：14px
- 实时生效

**主题选择（下拉）**
- 6 种预设主题
- 选择后立即切换配色

**主题开关（滑动）**
- 快速切换深色/浅色
- 与主题下拉联动

**复制按钮**
- 点击复制全部代码
- 显示"已复制"提示（2秒后消失）

## 技术方案

### 技术选型

| 技术 | 用途 | 来源 |
|------|------|------|
| Monaco Editor | 代码编辑器核心 | CDN: `https://cdn.jsdelivr.net/npm/monaco-editor@latest/` |
| HTML/CSS/JS | 页面结构样式 | 单文件实现 |
| Clipboard API | 复制功能 | 浏览器原生 API |

### 文件结构

```
tool_codeview/
├── codeview.html          # 主文件（单HTML包含所有功能）
└── docs/
    └── superpowers/
        └── specs/
            └── 2026-04-25-code-viewer-design.md
```

### 核心代码结构

```javascript
// 主要模块
const CodeViewer = {
  editor: null,           // Monaco Editor 实例

  // 初始化
  init() {
    this.createEditor();
    this.bindEvents();
    this.loadThemes();
  },

  // 创建编辑器
  createEditor() {
    this.editor = monaco.editor.create(container, {
      value: '',
      language: 'plaintext',
      theme: 'vs-dark',
      fontSize: 14,
      fontFamily: 'Consolas',
      lineNumbers: 'on',
      folding: true,
      automaticLayout: true
    });
  },

  // 启发式语言检测
  detectLanguage() {
    const model = this.editor.getModel();
    const value = model.getValue();
    if (value.length < 20) return;

    // 基于关键字特征的简单检测
    const patterns = {
      javascript: [/function\s*\(/, /const\s+\w+/, /let\s+\w+/, /=>\s*{/, /require\(/],
      python: [/def\s+\w+\s*\(/, /import\s+\w+/, /from\s+\w+\s+import/, /if\s+\w+:\s*$/],
      java: [/public\s+class/, /private\s+\w+/, /import\s+java\./, /System\.out\.print/],
      typescript: [/interface\s+\w+/, /type\s+\w+\s*=/, /:\s*\w+\s*;/, /<\w+>/],
      go: [/func\s+\w+\s*\(/, /package\s+\w+/, /import\s+"fmt"/, /:=/],
      html: [/<html/, /<div/, /<body/, /<script\s+src=/],
      css: [/^\s*\w+\s*{/, /@media/, /margin:\s*\d+/, /color:\s*#/],
      json: [/^\s*{/, /^\s*\[/, /"\w+":\s*"/, /"\w+":\s*\d+/],
      markdown: [/^#+\s+/, /^\*\s+/, /^-\s+/, /\[.*\]\(.*\)/],
      sql: [/SELECT\s+/, /FROM\s+/, /INSERT\s+INTO/, /CREATE\s+TABLE/],
    };

    let detected = 'plaintext';
    let maxScore = 0;

    for (const [lang, regexes] of Object.entries(patterns)) {
      const score = regexes.reduce((count, regex) =>
        count + (regex.test(value) ? 1 : 0), 0);
      if (score > maxScore) {
        maxScore = score;
        detected = lang;
      }
    }

    if (detected !== 'plaintext') {
      monaco.editor.setModelLanguage(model, detected);
      // 更新下拉选择框
      document.getElementById('language-select').value = detected;
    }
  },

  // 切换主题
  setTheme(themeName) {
    monaco.editor.setTheme(themeName);
  },

  // 调整字体
  setFont(fontFamily, fontSize) {
    this.editor.updateOptions({
      fontFamily: fontFamily,
      fontSize: fontSize
    });
  },

  // 复制代码
  copyCode() {
    const code = this.editor.getValue();
    navigator.clipboard.writeText(code);
    this.showCopySuccess();
  }
};
```

### 自定义主题定义

```javascript
// 定义 Monokai 主题示例
monaco.editor.defineTheme('monokai', {
  base: 'vs-dark',
  inherit: true,
  rules: [
    { token: 'keyword', foreground: 'F92672' },
    { token: 'string', foreground: 'E6DB74' },
    { token: 'comment', foreground: '75715E' },
    { token: 'function', foreground: 'A6E22E' },
    { token: 'number', foreground: 'AE81FF' },
  ],
  colors: {
    'editor.background': '#272822',
    'editor.foreground': '#F8F8F2',
  }
});
```

## 交互流程

### 主要流程

1. 用户打开页面，看到空代码区域
2. 用户粘贴代码到编辑器
3. 启发式检测推测语言并应用语法高亮（用户可手动覆盖）
4. 工具栏语言下拉显示当前语言
5. 用户可调整字体、字号、主题
6. 用户可折叠代码块
7. 用户点击复制按钮复制代码

### 主题切换联动

- 滑动开关切换时，自动切换到对应的 VS Dark/VS Light 主题
- 下拉选择其他主题时，滑动开关位置相应更新

## UI 设计规范

### 颜色方案

**深色主题**
- 工具栏背景：`#2d2d2d`
- 编辑器背景：`#1e1e1e`
- 文字颜色：`#d4d4d4`
- 按钮背景：`#555`
- 按钮激活：`#007acc`

**浅色主题**
- 工具栏背景：`#f3f3f3`
- 编辑器背景：`#ffffff`
- 文字颜色：`#333333`
- 按钮背景：`#ddd`
- 按钮激活：`#007acc`

### 响应式设计

- 最小宽度：600px
- 工具栏自适应排列
- 编辑器高度：calc(100vh - toolbar-height)

## 测试要点

1. 语言自动检测准确性
2. 主题切换是否正确生效
3. 字体调整是否实时更新
4. 复制功能是否正常工作
5. 代码折叠是否可用
6. 深色/浅色主题切换是否流畅

## Why

用户需要一个简洁的代码查看工具，方便粘贴和阅读代码，无需安装任何软件。

**How to apply**

开发时遵循此设计文档，确保所有功能按规范实现。