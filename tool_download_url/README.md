# 网页下载工具

纯前端网页内容下载工具，无需安装任何依赖。

## 快速开始

直接用浏览器打开 `index.html` 文件即可使用。

## 功能

| 格式 | 说明 | 输出 |
|------|------|------|
| 📄 HTML | 仅下载网页源代码 | .html |
| 📦 HTML+ 资源 | 下载 HTML 及图片/CSS/JS 等资源 | .zip |
| 📝 Markdown | 提取内容为 Markdown 格式 | .md |
| 🖼️ 长截图 | 保存网页完整截图 | .png |

## 特性

- **右侧控制台** - 实时显示下载进度和日志（黑底白字）
- **多 CORS 代理** - 自动切换可用代理
- **资源打包** - HTML+ 资源模式会打包所有可下载的资源
- **长截图** - 使用 html2canvas 渲染完整页面

## 推荐使用的网站

- https://example.com
- https://zh.moegirl.org.cn (萌娘百科)
- https://www.ruanyifeng.com/blog/ (阮一峰博客)

## 限制

⚠️ 百度、谷歌、淘宝等有严格 CORS 限制的网站无法下载

⚠️ HTML+ 资源模式下，部分图床可能有 CORS 限制导致图片无法下载

## 技术实现

- **CORS 代理**: allorigins.win
- **ZIP 打包**: JSZip
- **长截图**: html2canvas
