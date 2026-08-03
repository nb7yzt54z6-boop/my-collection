# V的工作台 · 个人收藏平台 Skill

## 一、平台概述

**V的工作台** 是一个基于 GitHub Pages 的静态个人收藏平台，用于展示两类内容：
- **提示词（Prompts）**：纯文本内容，可附带原始 Markdown 文件供下载
- **HTML 网页（Webpages）**：带链接的工具/应用，展示截图预览和文字描述

**访问地址**：`https://nb7yzt54z6-boop.github.io/my-collection/`
**GitHub 仓库**：`https://github.com/nb7yzt54z6-boop/my-collection.git`

> **重要原则**：做任何调整前（包括修改页面、更新内容、删除条目、更改配置等），都需要先跟用户确认需求，用户确认后再进行调整。

---

## 二、仓库结构

```
personal-site/
├── index.html           # 主页面（单页应用，内嵌 CSS + JS）
├── data.json            # 数据源（所有内容存储于此）
├── docs/                # 提示词原文 Markdown 文件存放目录
│   └── fitness-workbench-prompt.md
├── screenshots/         # 网页截图存放目录
│   └── fitness-workbench.png
└── my-collection-skill.md   # 本 Skill 文件
```

---

## 三、数据模型（data.json）

```json
{
  "site_name": "V的工作台",
  "prompts": [
    {
      "id": 1,
      "title": "标题",
      "content": "完整内容文本（支持 Markdown 纯文本格式）",
      "md_file": "docs/文件名.md",
      "created_at": "2026-08-03"
    }
  ],
  "webpages": [
    {
      "id": 1,
      "title": "网页标题",
      "description": "功能描述文字",
      "url": "https://...",
      "screenshot": "screenshots/文件名.png",
      "created_at": "2026-08-03"
    }
  ]
}
```

### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `site_name` | string | 站点标题，显示在页面顶部 |
| `prompts[].id` | number | 自增 ID，新条目 = 当前最大 id + 1 |
| `prompts[].title` | string | 提示词标题 |
| `prompts[].content` | string | 完整内容，支持 Markdown 纯文本格式 |
| `prompts[].md_file` | string | 可选的原始 Markdown 文件路径（相对路径），用户可下载 |
| `prompts[].created_at` | string | 创建日期，格式 `YYYY-MM-DD` |
| `webpages[].id` | number | 自增 ID |
| `webpages[].title` | string | 网页标题 |
| `webpages[].description` | string | 功能描述文字 |
| `webpages[].url` | string | 完整网页链接 |
| `webpages[].screenshot` | string | 截图路径（相对路径） |
| `webpages[].created_at` | string | 创建日期 |

---

## 四、内容添加工作流

### 触发条件

当用户满足以下任一条件时，自动执行本工作流：
1. 用户发送 `.md` 或 `.md` 文件
2. 用户发送一个网页链接（URL）
3. 用户同时发送 MD 文件 + 链接
4. 用户明确提到"我的工作台"、"V的工作台"或"更新内容"

### 工作流 A：添加提示词（MD 文件）

**Step 1：接收 MD 文件**
- 获取用户提供的 MD 文件内容
- 文件路径：保存到 `/workspace/personal-site/docs/` 目录下
- 文件名规则：使用英文短横线命名，如 `fitness-workbench-prompt.md`

**Step 2：提取标题**
- 从 MD 文件中读取第一个 `# ` 开头的行作为标题
- 去除 `# ` 前缀，保留完整标题

**Step 3：提取内容**
- 完整保留 MD 文件全部内容（包括标题行）
- 内容可能很长（数千字），需要完整保留

**Step 4：更新 data.json**
- 读取 `/workspace/personal-site/data.json`
- 在 `prompts` 数组中追加新条目
- `id` = 当前 prompts 最大 id + 1
- `title` = Step 2 提取的标题
- `content` = Step 3 提取的完整内容
- `md_file` = `docs/文件名.md`
- `created_at` = 当天日期 `YYYY-MM-DD`

**Step 5：提交并推送**
- 依次执行：`git add -A` → `git commit -m "添加: 标题"` → `git push`
- 提交信息示例：`添加: 健身跟练工作台 · AI 引导式生成提示词`

### 工作流 B：添加网页链接

**Step 1：接收链接**
- 获取用户提供的完整 URL

**Step 2：截图**
- 使用浏览器工具打开该链接
- 截取全屏截图，保存到 `/workspace/personal-site/screenshots/` 目录
- 文件名规则：使用英文短横线命名，如 `fitness-workbench.png`
- 截图完成后，对截图进行适当裁剪，确保只保留主要内容区域

**Step 3：提取信息**
- 从网页中提取标题：读取 `<title>` 标签内容
- 从网页中提取描述：读取 `<meta name="description">` 内容，或从页面内容中提取 1-2 句概括
- 如果用户提供了描述，优先使用用户提供的描述

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
- 依次执行：`git add -A` → `git commit -m "添加: 标题"` → `git push`
- 提交信息示例：`添加: 健身跟练工作台`

### 工作流 C：同时添加 MD + 链接

如果用户同时提供了 MD 文件和链接，则：
1. 先执行工作流 A（添加提示词）
2. 再执行工作流 B（添加网页链接）
3. 合并为一次 commit 和 push

---

## 五、UI/UX 约定

### 页面布局
- 响应式设计，自动适配电脑和手机
- 顶部：站点标题 + 描述
- 中间：Tab 切换栏（"提示词" / "HTML 网页"）
- 下方：内容卡片列表

### 卡片样式（提示词）
- 标题：18px 字体，加粗
- 内容：14px，灰色文字，`max-height` 折叠展开
- 内容超过 200 字符时显示"展开全文"按钮
- 展开后顶部和底部都有"收起全文"按钮
- 收起时自动滚动到卡片顶部
- 底部显示日期和"下载原文"按钮（如有 MD 文件）

### 卡片样式（网页）
- 顶部：截图区域（`max-height: 260px`，移动端 200px）
- 渐进式图片加载：`opacity: 0` → 加载完成后 `opacity: 1`
- 骨架屏 shimmer 动画占位
- 点击图片可放大查看原图（全屏查看器）
- 标题 + 描述文字 + 日期 + "打开链接"按钮

### 交互细节
- 图片懒加载：`loading="lazy"` + `decoding="async"`
- 图片查看器：全屏半透明黑色遮罩，点击关闭或按 ESC 键关闭
- Tab 切换标签时自动滚动到页面顶部
- 页面滚动超过 300px 时显示"回到顶部"按钮
- 下载按钮使用 `target="_blank"` 在新标签页打开

---

## 六、Git 操作规范

### 仓库配置
```bash
# 远程仓库
https://github.com/nb7yzt54z6-boop/my-collection.git
```

### 工作目录
```bash
cd /workspace/personal-site
```

### 提交流程
```bash
git add -A
git commit -m "类型: 描述"
git push
```

### 提交信息格式
- 添加新内容：`添加: 内容标题`
- 修改页面布局：`优化: 具体改动说明`
- 修复问题：`修复: 问题描述`

### 注意事项
- 始终在 `/workspace/personal-site` 目录下操作
- 推送前先 `git status` 确认变更
- 截图文件需要用 `git add` 显式添加（新文件）

---

## 七、GitHub Pages 部署

- 仓库已启用 GitHub Pages（Source: main branch, root directory）
- 推送后等待约 1-2 分钟自动部署
- 公开访问地址：`https://nb7yzt54z6-boop.github.io/my-collection/`

---

## 八、常见场景处理

### 场景 1：用户发送一个 MD 文件
→ 执行工作流 A（添加提示词）

### 场景 2：用户发送一个链接
→ 执行工作流 B（添加网页链接）
→ 注意：截图需要使用浏览器打开页面并截图

### 场景 3：用户同时发送 MD + 链接
→ 执行工作流 C（同时添加两个内容）

### 场景 4：用户说"更新我的工作台"
→ 确认用户要添加什么内容，引导用户提供 MD 文件或链接

### 场景 5：用户说"修改某个内容"
→ 读取当前 data.json，定位到对应 id 的条目，修改后推送

### 场景 6：用户说"优化页面"
→ 修改 index.html 的 CSS/JS，推送后 GitHub Pages 自动更新

---

## 九、初始化指引（如果仓库不存在）

如果在新环境需要重新搭建，步骤：
1. 创建目录 `personal-site/`
2. 创建 `index.html`（参考现有完整 HTML）
3. 创建 `data.json`（初始结构：`{"site_name":"V的工作台","prompts":[],"webpages":[]}`）
4. 创建 `docs/` 和 `screenshots/` 目录
5. 初始化 Git 仓库 → 关联远程仓库 → 推送
6. 在 GitHub 仓库设置中启用 Pages

---

## 十、Skill 文件本身

- 本文件路径：`/workspace/personal-site/my-collection-skill.md`
- 在新对话中，用户提到"我的工作台"或"V的工作台"时，AI 应读取本文件了解平台逻辑
- 本文件应随平台一起推送到 GitHub 仓库，确保永久可用
- **Skill 主文件下载路径**：`https://raw.githubusercontent.com/nb7yzt54z6-boop/my-collection/main/.trae/skills/my-collection/SKILL.md`
- **本参考文档下载路径**：`https://raw.githubusercontent.com/nb7yzt54z6-boop/my-collection/main/my-collection-skill.md`