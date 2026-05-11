# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

纯前端网页下载工具,无需后端服务器。用户输入 URL 后,通过 CORS 代理获取网页内容,支持多种格式导出。

## 架构说明

**技术栈:**
- 纯 HTML/CSS/JavaScript(ES6+)
- 依赖 CDN 库: JSZip(打包资源)、html2canvas(截图)
- CORS 代理: allorigins.win

**核心功能模块:**
1. **HTML 获取** - 通过多个 CORS 代理轮询获取网页内容
2. **资源提取** - 正则解析 HTML 提取图片/CSS/JS 链接
3. **资源下载** - 通过代理下载外部资源并打包为 ZIP
4. **Markdown 转换** - 简单的 HTML 到 Markdown 转换
5. **长截图** - 使用 html2canvas 渲染整页截图

**特殊处理:**
- 维基百科使用官方 API (`w/api.php?action=parse`)
- 自动处理相对 URL、`//` 开头的协议相对 URL
- 图片资源通过 `blobToBase64` 转换后打包

## 关键文件

- `index.html` - 单文件应用,包含全部逻辑(1000+ 行)

## 开发说明

- 本地直接打开 `index.html` 即可运行
- 无构建工具依赖
- 所有外部资源通过 CDN 引入
