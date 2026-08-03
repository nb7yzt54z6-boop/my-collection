---
name: 我的工作台
description: 管理「V的工作台」的内容更新。当用户发送 MD 文件、网页链接，或提到"我的工作台"、"V的工作台"时自动触发，完成内容添加、截图、数据更新和 Git 推送。
---

# 我的工作台

## 描述

这是一个用于管理「V的工作台」的技能。该平台是一个基于 GitHub Pages 的静态网站，展示提示词（Prompts）和 HTML 网页（Webpages）两类内容。本技能负责接收用户提供的内容（MD 文件或链接），自动完成提取、截图、数据更新和 Git 推送的全流程。

> **重要原则**：做任何调整前（包括修改页面、更新内容、删除条目、更改配置等），都需要先跟用户确认需求，用户确认后再进行调整。

## 使用场景

当用户满足以下任一条件时，自动加载本技能：

1. 用户发送 `.md` 或 `.md` 文件
2. 用户发送一个网页链接（URL）
3. 用户同时发送 MD 文件 + 链接
4. 用户明确提到"我的工作台"、"V的工作台"、"工作台平台"、"更新内容"等关键词
5. 用户说"修改某个内容"、"优化页面"等涉及平台管理的请求

## 平台信息

- **访问地址**：`https://nb7yzt54z6-boop.github.io/my-collection/`
- **GitHub 仓库**：`https://github.com/nb7yzt54z6-boop/my-collection.git`
- **工作目录**：`/workspace/personal-site/`
- **Git 远程仓库**：`https://github.com/nb7yzt54z6-boop/my-collection.git`（远程已配置为 origin，直接 git push 即可）
- **Skill 文件下载路径**：`https://raw.githubusercontent.com/nb7yzt54z6-boop/my-collection/main/.trae/skills/my-collection/SKILL.md`

## 仓库结构

```
personal-site/
├── index.html           # 主页面（单页应用，内嵌 CSS + JS）
├── data.json            # 数据源（所有内容存储于此）
├── docs/                # 提示词原文 Markdown 文件存放目录
├── screenshots/         # 网页截图存放目录
├── .trae/skills/my-collection/SKILL.md  # 本技能文件
└── my-collection-skill.md              # 详细参考文档
```

## 数据模型（data.json）

```json
{
  "site_name": "V的工作台",
  "prompts": [
    {
      "id": 1,
      "title": "标题",
      "content": "完整内容文本",
      "md_file": "docs/文件名.md",
      "created_at": "2026-08-03"
    }
  ],
  "webpages": [
    {
      "id": 1,
      "title": "网页标题",
      "description": "功能描述",
      "url": "https://...",
      "screenshot": "screenshots/文件名.png",
      "created_at": "2026-08-03"
    }
  ]
}
```

## 指令

### 工作流 A：添加提示词（MD 文件）

**Step 1：保存 MD 文件**
- 将用户提供的 MD 文件内容保存到 `/workspace/personal-site/docs/` 目录下
- 文件名：英文短横线命名，如 `fitness-workbench-prompt.md`

**Step 2：提取标题**
- 从 MD 文件中读取第一个 `# ` 开头的行作为标题
- 去除 `# ` 前缀，保留完整标题

**Step 3：提取内容**
- 完整保留 MD 文件全部内容（包括标题行）

**Step 4：更新 data.json**
- 读取 `/workspace/personal-site/data.json`
- 在 `prompts` 数组中追加新条目
- `id` = 当前 prompts 最大 id + 1
- `title` = Step 2 提取的标题
- `content` = Step 3 提取的完整内容（文件全部内容）
- `md_file` = `docs/文件名.md`
- `created_at` = 当天日期 `YYYY-MM-DD`

**Step 5：提交并推送**
```bash
cd /workspace/personal-site
git add -A
git commit -m "添加: 标题"
git push
```

### 工作流 B：添加网页链接

**Step 1：获取链接**
- 获取用户提供的完整 URL

**Step 2：截图**
- 使用浏览器工具打开该链接
- 截取全屏截图，保存到 `/workspace/personal-site/screenshots/` 目录
- 文件名：英文短横线命名，如 `fitness-workbench.png`
- 截图后适当裁剪，保留主要内容区域

**Step 3：提取信息**
- 从网页中提取标题（`<title>` 标签）
- 提取描述（`<meta name="description">` 或页面内容概括 1-2 句）
- 如果用户提供了描述，优先使用用户提供的

**Step 4：更新 data.json**
- 读取 `/workspace/personal-site/data.json`
- 在 `webpages` 数组中追加新条目
- `id` = 当前 webpages 最大 id + 1
- `title` = Step 3 提取的标题
- `description` = Step 3 提取的描述
- `url` = 用户提供的原始链接
- `screenshot` = `screenshots/文件名.png`
- `created_at` = 当天日期 `YYYY-MM-DD`

**Step 5：提交并推送**
```bash
cd /workspace/personal-site
git add -A
git commit -m "添加: 标题"
git push
```

### 工作流 C：同时添加 MD + 链接
1. 先执行工作流 A（添加提示词）
2. 再执行工作流 B（添加网页链接）
3. 合并为一次 commit 和 push

### 其他场景

**修改内容**：读取 data.json，定位对应 id 的条目，修改后推送。

**优化页面**：修改 `index.html` 的 CSS/JS，推送后 GitHub Pages 自动更新。

**初始化**：如果仓库不存在，则创建目录、index.html、data.json（初始结构：`{"site_name":"V的工作台","prompts":[],"webpages":[]}`）、docs/、screenshots/ 目录，初始化 Git 并推送。

## UI/UX 规范

### 1. 内容预览展开/收起逻辑

首页的内容卡片（prompts 提示词）使用预览模式，展开前只显示约 4 行文字，点击「展开全文」后展示全部内容。

**CSS 实现（index.html）：**
```css
.card-content {
  max-height: 6.8em;        /* 约 4 行文字 */
  overflow: hidden;
  transition: max-height 0.4s ease;
  white-space: pre-wrap;
  word-break: break-word;
}
.card-content.expanded {
  max-height: none;          /* 展开后无高度限制 */
}
```

**HTML 结构：**
- 内容由 `div.card-content` 包裹，默认 `max-height: 6.8em` 截断
- 内容超过 200 字符时显示「展开全文」按钮（`card-content-trigger`）
- 展开后顶部显示「收起全文」按钮（`collapse-top`）
- 内容少于 200 字符时直接全部展示，不显示展开按钮

**JS 逻辑：** `toggleContent(id)` 函数通过切换 `expanded` 类控制展开/收起，使用双 `requestAnimationFrame` 实现平滑动画。

**修改指引：** 如果需要对首页内容展示做类似「展开全文」的折叠效果，参考以上 CSS 类和 JS 函数实现。

### 2. 图标系统（Emoji → SVG 替换规范）

截图环境中的 emoji 字体可能未正确渲染，导致显示为 `X` 方框。解决方案：将页面中的 emoji 图标全部替换为 SVG 矢量图标。

**实现方式：**
1. 定义一个 `ICONS` 对象，集中存储所有 SVG 图标字符串
2. 定义一个 `icon(name)` 辅助函数，用于在模板字符串中引用图标
3. 所有 SVG 使用 `currentColor` 继承父元素文本颜色

**SVG 设计规范：**
- `viewBox="0 0 24 24"`，统一的 24x24 坐标系
- `stroke="currentColor"`，`fill="none"`，`stroke-width="2"`
- 添加 `style="vertical-align:middle"` 确保与文本对齐

**代码示例（dance-workbench 参考）：**
```javascript
const ICONS = {
  play: '<svg viewBox="0 0 24 24" width="24" height="24" fill="currentColor" style="vertical-align:middle"><polygon points="8 5 19 12 8 19 8 5"/></svg>',
  clock: '<svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" style="vertical-align:middle"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>',
  star: '<svg viewBox="0 0 24 24" width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" style="vertical-align:middle"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>',
  // ... 其他图标
};
function icon(name) { return ICONS[name] || ''; }
```

**使用方式：** 在模板字符串中通过 `\${icon('play')}` 引用，替代原来的 emoji 字符。

**适用场景：** 所有需要截图展示的页面、需要跨平台稳定渲染的图标（避免 emoji 在不同系统/截图环境中显示不一致）。

## 详细参考

更完整的平台逻辑说明（含 UI/UX 约定、Git 规范、常见场景等）请参考 `/workspace/personal-site/my-collection-skill.md` 文件。