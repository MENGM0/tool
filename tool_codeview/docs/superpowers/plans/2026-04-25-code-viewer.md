# 代码查看器 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建单HTML网页代码查看器，支持语法高亮、代码折叠、主题切换、字体调整、一键复制等功能。

**Architecture:** 单HTML文件实现，Monaco Editor CDN加载，纯前端无后端依赖。工具栏包含语言选择、字体/字号调整、主题选择、主题切换开关、复制按钮。Monaco Editor提供代码区域，内置行号、折叠、高亮。

**Tech Stack:** Monaco Editor (CDN), HTML5, CSS3, JavaScript ES6, Clipboard API

---

## File Structure

```
tool_codeview/
├── codeview.html          # 主文件（单HTML包含所有功能）
└── docs/
    └── superpowers/
        ├── specs/
        │   └── 2026-04-25-code-viewer-design.md
        └── plans/
        │   └── 2026-04-25-code-viewer.md
```

---

### Task 1: 创建基础HTML骨架和工具栏结构

**Files:**
- Create: `D:\workspace\Git\tool\tool_codeview\codeview.html`

- [ ] **Step 1: 创建HTML文件基础结构**

创建包含DOCTYPE、head、body的基础HTML骨架，引入Monaco Editor CDN。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>代码查看器</title>
  <!-- Monaco Editor CDN -->
  <script src="https://cdn.jsdelivr.net/npm/monaco-editor@0.52.0/min/vs/loader.js"></script>
  <style>
    /* CSS将在后续步骤添加 */
  </style>
</head>
<body>
  <div id="toolbar"></div>
  <div id="editor-container"></div>
  <script>
    // JavaScript将在后续步骤添加
  </script>
</body>
</html>
```

- [ ] **Step 2: 添加工具栏HTML结构**

在 `<body>` 中添加完整的工具栏结构。

```html
<body>
  <div id="toolbar">
    <!-- 语言选择 -->
    <div class="toolbar-item">
      <label for="language-select">语言:</label>
      <select id="language-select">
        <option value="plaintext">自动检测</option>
        <option value="javascript">JavaScript</option>
        <option value="typescript">TypeScript</option>
        <option value="python">Python</option>
        <option value="java">Java</option>
        <option value="cpp">C++</option>
        <option value="csharp">C#</option>
        <option value="go">Go</option>
        <option value="rust">Rust</option>
        <option value="php">PHP</option>
        <option value="html">HTML</option>
        <option value="css">CSS</option>
        <option value="json">JSON</option>
        <option value="markdown">Markdown</option>
        <option value="sql">SQL</option>
        <option value="yaml">YAML</option>
        <option value="shell">Shell</option>
      </select>
    </div>

    <!-- 字体选择 -->
    <div class="toolbar-item">
      <label for="font-select">字体:</label>
      <select id="font-select">
        <option value="Consolas">Consolas</option>
        <option value="Monaco">Monaco</option>
        <option value="'Source Code Pro'">Source Code Pro</option>
        <option value="'Fira Code'">Fira Code</option>
        <option value="'JetBrains Mono'">JetBrains Mono</option>
      </select>
    </div>

    <!-- 字号调节 -->
    <div class="toolbar-item">
      <label for="font-size">字号:</label>
      <input type="range" id="font-size" min="12" max="24" value="14">
      <span id="font-size-value">14px</span>
    </div>

    <!-- 主题选择 -->
    <div class="toolbar-item">
      <label for="theme-select">主题:</label>
      <select id="theme-select">
        <option value="vs-dark">VS Dark</option>
        <option value="vs">VS Light</option>
        <option value="monokai">Monokai</option>
        <option value="solarized-dark">Solarized Dark</option>
        <option value="one-dark-pro">One Dark Pro</option>
        <option value="dracula">Dracula</option>
      </select>
    </div>

    <!-- 深色/浅色开关 -->
    <div class="toolbar-item toggle-switch">
      <span class="toggle-label">☀️</span>
      <div class="toggle-track" id="theme-toggle">
        <div class="toggle-slider"></div>
      </div>
      <span class="toggle-label">🌙</span>
    </div>

    <!-- 复制按钮 -->
    <div class="toolbar-item">
      <button id="copy-btn">📋 复制</button>
      <span id="copy-success" class="hidden">已复制!</span>
    </div>
  </div>

  <!-- 编辑器容器 -->
  <div id="editor-container"></div>
</body>
```

- [ ] **Step 3: 添加基础CSS样式**

在 `<style>` 中添加基础样式，包括深色主题默认样式。

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  background: #1e1e1e;
  color: #d4d4d4;
  min-height: 100vh;
}

#toolbar {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 12px 20px;
  background: #2d2d2d;
  border-bottom: 1px solid #404040;
  flex-wrap: wrap;
}

.toolbar-item {
  display: flex;
  align-items: center;
  gap: 6px;
}

.toolbar-item label {
  font-size: 12px;
  color: #888;
}

.toolbar-item select,
.toolbar-item button {
  background: #1e1e1e;
  color: #d4d4d4;
  border: 1px solid #404040;
  padding: 6px 12px;
  border-radius: 4px;
  font-size: 13px;
  cursor: pointer;
}

.toolbar-item select:hover,
.toolbar-item button:hover {
  border-color: #007acc;
}

.toolbar-item button {
  background: #007acc;
  border: none;
}

.toolbar-item button:hover {
  background: #0098ff;
}

#font-size {
  width: 80px;
  cursor: pointer;
}

#font-size-value {
  font-size: 12px;
  color: #888;
  min-width: 30px;
}

/* 滑动开关样式 */
.toggle-switch {
  display: flex;
  align-items: center;
  gap: 8px;
}

.toggle-label {
  font-size: 14px;
}

.toggle-track {
  width: 48px;
  height: 24px;
  background: #555;
  border-radius: 12px;
  position: relative;
  cursor: pointer;
  transition: background 0.3s;
}

.toggle-track.dark {
  background: #007acc;
}

.toggle-slider {
  width: 20px;
  height: 20px;
  background: #fff;
  border-radius: 50%;
  position: absolute;
  left: 2px;
  top: 2px;
  transition: transform 0.3s;
}

.toggle-track.dark .toggle-slider {
  transform: translateX(24px);
}

#copy-success {
  font-size: 12px;
  color: #4ec9b0;
  margin-left: 8px;
}

.hidden {
  display: none;
}

#editor-container {
  height: calc(100vh - 60px);
  width: 100%;
}
```

- [ ] **Step 4: 在浏览器测试基础结构**

打开 `codeview.html`，检查：
- 工具栏是否正确显示
- 各下拉菜单和滑动条是否可见
- 页面布局是否正确

---

### Task 2: 集成Monaco Editor并实现基础功能

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html` (script部分)

- [ ] **Step 1: 添加Monaco Editor初始化代码**

在 `<script>` 中添加Monaco Editor加载和初始化代码。

```javascript
// Monaco Editor加载
require.config({
  paths: {
    'vs': 'https://cdn.jsdelivr.net/npm/monaco-editor@0.52.0/min/vs'
  }
});

require(['vs/editor/editor.main'], function () {
  // 定义自定义主题（将在后续任务添加）
  defineCustomThemes();

  // 创建编辑器
  const editor = monaco.editor.create(document.getElementById('editor-container'), {
    value: '// 在此处粘贴代码...\n',
    language: 'javascript',
    theme: 'vs-dark',
    fontSize: 14,
    fontFamily: 'Consolas',
    lineNumbers: 'on',
    folding: true,
    automaticLayout: true,
    minimap: { enabled: false },
    scrollBeyondLastLine: false,
    wordWrap: 'on',
    padding: { top: 16 }
  });

  // 初始化事件绑定
  initEvents(editor);
});
```

- [ ] **Step 2: 在浏览器测试Monaco Editor**

打开 `codeview.html`，检查：
- Monaco Editor是否正确加载和显示
- 行号是否显示
- 代码区域是否可编辑
- 粘贴代码是否正常工作

---

### Task 3: 实现语言选择和启发式检测

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html` (script部分)

- [ ] **Step 1: 添加语言检测函数**

在 `initEvents` 函数之前添加语言检测逻辑。

```javascript
// 启发式语言检测
function detectLanguage(code) {
  if (code.length < 20) return 'plaintext';

  const patterns = {
    javascript: [/function\s*\(/, /const\s+\w+/, /let\s+\w+/, /=>\s*{/, /require\(/, /export\s+/],
    typescript: [/interface\s+\w+/, /type\s+\w+\s*=/, /:\s*\w+\s*[;=]/, /<\w+>/, /enum\s+\w+/],
    python: [/def\s+\w+\s*\(/, /import\s+\w+/, /from\s+\w+\s+import/, /if\s+__name__/, /print\s*\(/],
    java: [/public\s+class/, /private\s+\w+/, /import\s+java\./, /System\.out\.print/, /throws\s+/],
    cpp: [/#include\s*</, /class\s+\w+\s*{/, /namespace\s+\w+/, /cout\s*<</, /int\s+main\s*\(/],
    go: [/func\s+\w+\s*\(/, /package\s+\w+/, /import\s+"fmt"/, /:=/, /go\s+\w+\(/],
    rust: [/fn\s+\w+\s*\(/, /let\s+mut/, /impl\s+\w+/, /pub\s+fn/, /use\s+\w+::/],
    html: [/<html[\s>]/, /<head[\s>]/, /<body[\s>]/, /<div[\s>]/, /<script[\s>]/],
    css: [/^\s*[\w.#][\w-]*\s*{/, /@media/, /margin:\s*\d+/, /color:\s*#/, /background:/],
    json: [/^\s*{[\s\n]*"/, /^\s*\[[\s\n]*"/, /"\w+":\s*"/, /"\w+":\s*\d+/],
    markdown: [/^#+\s+/, /^\*\s+\w/, /^-\s+\w/, /\[.*\]\(.*\)/, /^>\s+/],
    sql: [/SELECT\s+/i, /FROM\s+/i, /INSERT\s+INTO/i, /CREATE\s+TABLE/i, /WHERE\s+/i],
    yaml: [/^\w+:\s+/, /^\s+-\s+\w+:/, /^\s+\w+:\s+/, /---\s*$/],
    shell: [/^#!/, /\$\w+/, /echo\s+/, /if\s*\[\s*\]/, /for\s+\w+\s+in/],
    php: [/<\?php/, /\$\w+/, /function\s+\w+\s*\(/, /echo\s+/, /require\s+/],
    csharp: [/namespace\s+\w+/, /class\s+\w+/, /using\s+/, /public\s+\w+/, /Console\.Write/],
  };

  let detected = 'plaintext';
  let maxScore = 0;

  for (const [lang, regexes] of Object.entries(patterns)) {
    const score = regexes.reduce((count, regex) =>
      count + (regex.test(code) ? 1 : 0), 0);
    if (score > maxScore) {
      maxScore = score;
      detected = lang;
    }
  }

  return maxScore >= 2 ? detected : 'plaintext';
}
```

- [ ] **Step 2: 添加语言选择事件绑定**

在 `initEvents` 函数中添加语言相关的事件处理。

```javascript
function initEvents(editor) {
  const model = editor.getModel();

  // 语言选择下拉
  const languageSelect = document.getElementById('language-select');
  languageSelect.addEventListener('change', function () {
    monaco.editor.setModelLanguage(model, this.value);
  });

  // 内容变化时自动检测语言
  model.onDidChangeContent(() => {
    const code = model.getValue();
    if (code.length > 50 && languageSelect.value === 'plaintext') {
      const detected = detectLanguage(code);
      if (detected !== 'plaintext') {
        monaco.editor.setModelLanguage(model, detected);
        languageSelect.value = detected;
      }
    }
  });

  // 后续任务将添加更多事件...
}
```

- [ ] **Step 3: 在浏览器测试语言检测**

打开 `codeview.html`，测试：
- 粘贴一段JavaScript代码，检查语言下拉是否自动变为JavaScript
- 粘贴一段Python代码，检查语言下拉是否自动变为Python
- 手动选择其他语言，检查高亮是否正确切换

---

### Task 4: 实现字体和字号调整

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html` (script部分)

- [ ] **Step 1: 添加字体字号事件绑定**

在 `initEvents` 函数中添加字体字号调整逻辑。

```javascript
  // 字体选择下拉
  const fontSelect = document.getElementById('font-select');
  fontSelect.addEventListener('change', function () {
    editor.updateOptions({ fontFamily: this.value });
  });

  // 字号滑动条
  const fontSizeInput = document.getElementById('font-size');
  const fontSizeValue = document.getElementById('font-size-value');
  fontSizeInput.addEventListener('input', function () {
    const size = parseInt(this.value);
    editor.updateOptions({ fontSize: size });
    fontSizeValue.textContent = size + 'px';
  });
```

- [ ] **Step 2: 在浏览器测试字体字号调整**

打开 `codeview.html`，测试：
- 选择不同字体，检查代码字体是否变化
- 拖动字号滑动条，检查字号是否实时变化
- 检查14px默认值是否正确显示

---

### Task 5: 实现主题切换功能

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html` (script部分)

- [ ] **Step 1: 添加自定义主题定义函数**

在Monaco初始化之前添加 `defineCustomThemes` 函数。

```javascript
// 定义自定义主题
function defineCustomThemes() {
  // Monokai
  monaco.editor.defineTheme('monokai', {
    base: 'vs-dark',
    inherit: true,
    rules: [
      { token: 'keyword', foreground: 'F92672' },
      { token: 'keyword.control', foreground: 'F92672' },
      { token: 'keyword.other', foreground: 'F92672' },
      { token: 'string', foreground: 'E6DB74' },
      { token: 'string.quoted', foreground: 'E6DB74' },
      { token: 'comment', foreground: '75715E', fontStyle: 'italic' },
      { token: 'function', foreground: 'A6E22E' },
      { token: 'function.call', foreground: 'A6E22E' },
      { token: 'number', foreground: 'AE81FF' },
      { token: 'constant', foreground: 'AE81FF' },
      { token: 'variable', foreground: 'F8F8F2' },
      { token: 'type', foreground: '66D9EF' },
      { token: 'class', foreground: 'A6E22E' },
    ],
    colors: {
      'editor.background': '#272822',
      'editor.foreground': '#F8F8F2',
      'editorLineNumber.foreground': '#75715E',
      'editor.selectionBackground': '#49483E',
      'editor.lineHighlightBackground': '#3E3D32',
    }
  });

  // Solarized Dark
  monaco.editor.defineTheme('solarized-dark', {
    base: 'vs-dark',
    inherit: true,
    rules: [
      { token: 'keyword', foreground: '859900' },
      { token: 'string', foreground: '2AA198' },
      { token: 'comment', foreground: '586E75', fontStyle: 'italic' },
      { token: 'function', foreground: '268BD2' },
      { token: 'number', foreground: 'D33682' },
      { token: 'variable', foreground: '839496' },
      { token: 'type', foreground: 'B58900' },
    ],
    colors: {
      'editor.background': '#002B36',
      'editor.foreground': '#839496',
      'editorLineNumber.foreground': '#586E75',
      'editor.selectionBackground': '#073642',
    }
  });

  // One Dark Pro
  monaco.editor.defineTheme('one-dark-pro', {
    base: 'vs-dark',
    inherit: true,
    rules: [
      { token: 'keyword', foreground: 'C678DD' },
      { token: 'string', foreground: '98C379' },
      { token: 'comment', foreground: '5C6370', fontStyle: 'italic' },
      { token: 'function', foreground: 'E5C07B' },
      { token: 'number', foreground: 'D19A66' },
      { token: 'variable', foreground: 'ABB2BF' },
      { token: 'type', foreground: 'E5C07B' },
    ],
    colors: {
      'editor.background': '#282C34',
      'editor.foreground': '#ABB2BF',
      'editorLineNumber.foreground': '#5C6370',
      'editor.selectionBackground': '#3E4451',
    }
  });

  // Dracula
  monaco.editor.defineTheme('dracula', {
    base: 'vs-dark',
    inherit: true,
    rules: [
      { token: 'keyword', foreground: 'FF79C6' },
      { token: 'string', foreground: 'F1FA8C' },
      { token: 'comment', foreground: '6272A4', fontStyle: 'italic' },
      { token: 'function', foreground: '50FA7B' },
      { token: 'number', foreground: 'BD93F9' },
      { token: 'variable', foreground: 'F8F8F2' },
      { token: 'type', foreground: '8BE9FD' },
    ],
    colors: {
      'editor.background': '#282A36',
      'editor.foreground': '#F8F8F2',
      'editorLineNumber.foreground': '#6272A4',
      'editor.selectionBackground': '#44475A',
    }
  });
}
```

- [ ] **Step 2: 添加主题切换事件绑定**

在 `initEvents` 函数中添加主题切换逻辑。

```javascript
  // 主题选择下拉
  const themeSelect = document.getElementById('theme-select');
  themeSelect.addEventListener('change', function () {
    monaco.editor.setTheme(this.value);
    // 更新滑动开关状态
    updateToggleState(this.value);
  });

  // 深色/浅色滑动开关
  const themeToggle = document.getElementById('theme-toggle');
  themeToggle.addEventListener('click', function () {
    const isDark = this.classList.toggle('dark');
    const theme = isDark ? 'vs-dark' : 'vs';
    monaco.editor.setTheme(theme);
    themeSelect.value = theme;
    updateUITheme(isDark);
  });

  // 初始化滑动开关状态（默认深色）
  themeToggle.classList.add('dark');

function updateToggleState(theme) {
  const themeToggle = document.getElementById('theme-toggle');
  const isDark = theme.includes('dark') || theme === 'monokai' ||
                 theme === 'solarized-dark' || theme === 'one-dark-pro' ||
                 theme === 'dracula';
  themeToggle.classList.toggle('dark', isDark);
  updateUITheme(isDark);
}

function updateUITheme(isDark) {
  const toolbar = document.getElementById('toolbar');
  const body = document.body;
  const selects = document.querySelectorAll('select, #font-size-value');
  const labels = document.querySelectorAll('label');

  if (isDark) {
    toolbar.style.background = '#2d2d2d';
    toolbar.style.borderBottom = '1px solid #404040';
    body.style.background = '#1e1e1e';
    body.style.color = '#d4d4d4';
    selects.forEach(s => { s.style.color = '#d4d4d4'; });
    labels.forEach(l => { l.style.color = '#888'; });
  } else {
    toolbar.style.background = '#f3f3f3';
    toolbar.style.borderBottom = '1px solid #ddd';
    body.style.background = '#ffffff';
    body.style.color = '#333333';
    selects.forEach(s => { s.style.color = '#333333'; });
    labels.forEach(l => { l.style.color = '#666'; });
  }
}
```

- [ ] **Step 3: 在浏览器测试主题切换**

打开 `codeview.html`，测试：
- 选择不同主题，检查配色是否正确变化
- 点击滑动开关，检查深色/浅色切换
- 检查主题下拉和滑动开关是否联动

---

### Task 6: 实现一键复制功能

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html` (script部分)

- [ ] **Step 1: 添加复制按钮事件绑定**

在 `initEvents` 函数中添加复制功能。

```javascript
  // 复制按钮
  const copyBtn = document.getElementById('copy-btn');
  const copySuccess = document.getElementById('copy-success');
  copyBtn.addEventListener('click', function () {
    const code = editor.getValue();
    if (code.trim()) {
      navigator.clipboard.writeText(code).then(() => {
        copySuccess.classList.remove('hidden');
        copySuccess.style.color = '#4ec9b0';
        setTimeout(() => {
          copySuccess.classList.add('hidden');
        }, 2000);
      }).catch(err => {
        copySuccess.classList.remove('hidden');
        copySuccess.textContent = '复制失败';
        copySuccess.style.color = '#f92672';
        setTimeout(() => {
          copySuccess.classList.add('hidden');
          copySuccess.textContent = '已复制!';
        }, 2000);
      });
    }
  });
```

- [ ] **Step 2: 在浏览器测试复制功能**

打开 `codeview.html`，测试：
- 粘贴代码后点击复制按钮
- 检查"已复制!"提示是否显示并在2秒后消失
- 在其他应用粘贴，检查复制内容是否正确

---

### Task 7: 添加浅色主题CSS样式和优化

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html` (style部分)

- [ ] **Step 1: 添加浅色主题CSS变量和响应式样式**

更新CSS样式，添加浅色主题样式和响应式设计。

```css
/* 浅色主题样式类 */
body.light-theme {
  background: #ffffff;
  color: #333333;
}

body.light-theme #toolbar {
  background: #f3f3f3;
  border-bottom: 1px solid #ddd;
}

body.light-theme select,
body.light-theme button:not(#copy-btn) {
  background: #ffffff;
  color: #333333;
  border: 1px solid #ccc;
}

body.light-theme select:hover,
body.light-theme button:not(#copy-btn):hover {
  border-color: #007acc;
}

body.light-theme .toggle-track {
  background: #ccc;
}

body.light-theme .toggle-track.dark {
  background: #007acc;
}

body.light-theme label {
  color: #666;
}

body.light-theme #font-size-value {
  color: #666;
}

/* 响应式设计 */
@media (max-width: 800px) {
  #toolbar {
    gap: 12px;
    padding: 10px 12px;
  }

  .toolbar-item label {
    display: none;
  }

  #font-size {
    width: 60px;
  }
}

@media (max-width: 600px) {
  #toolbar {
    flex-wrap: wrap;
    row-gap: 8px;
  }
}
```

- [ ] **Step 2: 更新updateUITheme函数使用body类**

修改 `updateUITheme` 函数，使用CSS类切换。

```javascript
function updateUITheme(isDark) {
  document.body.classList.toggle('light-theme', !isDark);
}
```

- [ ] **Step 3: 在浏览器测试浅色主题和响应式**

打开 `codeview.html`，测试：
- 切换到浅色主题，检查所有元素颜色是否正确
- 调整浏览器窗口宽度，检查响应式布局

---

### Task 8: 最终测试和提交

**Files:**
- Modify: `D:\workspace\Git\tool\tool_codeview\codeview.html`

- [ ] **Step 1: 完整功能测试**

打开 `codeview.html`，逐一测试所有功能：
1. 语言自动检测：粘贴JavaScript/Python/Java代码
2. 语言手动选择：切换不同语言
3. 字体选择：切换Consolas/Fira Code等
4. 字号调整：滑动条调整12-24px
5. 主题选择：切换6种主题
6. 深色/浅色开关：滑动切换
7. 一键复制：复制代码
8. 代码折叠：点击折叠指示器

- [ ] **Step 2: 修复发现的问题**

根据测试结果修复任何问题。

- [ ] **Step 3: 提交代码**

```bash
git add codeview.html
git commit -m "feat: add code viewer with Monaco Editor

Features:
- Auto language detection with heuristics
- Manual language selection dropdown
- Font family selection (Consolas, Monaco, Fira Code, etc.)
- Font size slider (12-24px)
- 6 theme presets (VS Dark, VS Light, Monokai, Solarized, One Dark, Dracula)
- Dark/Light toggle switch
- One-click copy to clipboard
- Code folding support
- Line numbers display

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

---

## Summary

本计划创建一个完整的代码查看器，包含8个主要任务：
1. HTML骨架和工具栏结构
2. Monaco Editor集成
3. 语言选择和自动检测
4. 字体字号调整
5. 主题切换
6. 一键复制
7. 浅色主题和响应式优化
8. 最终测试和提交