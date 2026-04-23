# COMP4141 Notes Sheet

期末考场 A4 双面速查的网页版。纯静态，零构建，客户端渲染 Markdown。

## 本地预览

```bash
cd notes_sheet_site
python3 -m http.server 8000
```

然后打开 http://localhost:8000

## 部署到 GitHub Pages

1. 在 GitHub 新建公开仓库（例如 `comp4141-notes`）。
2. 把本目录三个文件（`index.html`、`notes.md`、`README.md`）推到 `main` 分支根目录：
   ```bash
   git init
   git add index.html notes.md README.md
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/<仓库名>.git
   git push -u origin main
   ```
3. 仓库 → **Settings** → **Pages** → Source 选 `Deploy from a branch`，Branch 选 `main` / `/ (root)`，保存。
4. 等 1–2 分钟，访问 `https://<你的用户名>.github.io/<仓库名>/`。

## 更新笔记内容

只需改 `notes.md`（或用同步脚本把 `_teaching_outputs/notes_sheet/notes_sheet_content.md` 复制过来），push 到 `main` 即可，页面会自动反映最新内容。

## 文件说明

| 文件 | 作用 |
|------|------|
| `index.html` | 页面主体，内嵌 CSS 和渲染逻辑，载入 `notes.md` 后渲染成卡片式布局 |
| `notes.md` | 笔记原文，同时用于页面渲染和 Markdown 下载 |
| `README.md` | 本文件 |

## 技术栈

- 原生 HTML + CSS（无框架）
- [marked.js](https://marked.js.org/) v11（CDN）做 Markdown → HTML 渲染
- 浏览器 `fetch()` + `IntersectionObserver` 做目录滚动高亮

## 免责声明

本笔记为个人学习整理，仅作参考，内容可能随课程更新而过时。使用者应以课程官方材料为准。
