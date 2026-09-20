# 博客维护指南（GuitarYuu 专用）

> 仓库：`GuitarYuu/guitaryuu.github.io` · 线上：https://guitaryuu.github.io/ · 本地：`workspace/default/blog/`
> 主题：Axi-Theme（Astro 5）+ 青衿架构三皮肤。官方主题文档：https://theme.axi404.top/collection/docs

---

## 一、写文章（最常用）

### 1. 新建文章

在 `src/content/blogs/` 下建一个文件夹，文件夹名就是文章链接（slug）：

```
src/content/blogs/my-first-post/index.md
```

`index.md` 最小模板（**直接抄这份**）：

```markdown
---
title: 文章标题（必填，最长 60 字）
description: 一句话摘要（必填，最长 160 字，会显示在列表和搜索里）
publishDate: 2026-09-20
category: tech
tags:
  - astro
  - 随笔
---

正文从这里开始，标准 Markdown。公式用 $E=mc^2$ 或 $$\int_0^1 x\,dx$$，
代码块 ```python ... ``` 自带高亮和复制按钮。
```

**frontmatter 字段速查**（schema 在 `src/content.config.ts`）：

| 字段 | 必填 | 说明 |
|---|---|---|
| `title` | ✅ | ≤60 字 |
| `description` | ✅ | ≤160 字 |
| `publishDate` | ✅ | `YYYY-MM-DD` |
| `updatedDate` | ❌ | 有修改时填 |
| `category` | ❌ | 分类，首页 Blog 下拉按它分组（示例用 `tech`） |
| `tags` | ❌ | 标签数组 |
| `language` | ❌ | `zh` |
| `draft: true` | ❌ | 草稿，不会发布 |
| `comment: false` | ❌ | 关闭该文评论 |
| `heroImage` | ❌ | 封面图（见下） |

### 2. 封面图（可选）

图片放在文章同目录，frontmatter 里写相对路径：

```markdown
---
heroImage:
  src: ./cover.jpg
  alt: 封面说明
---
```

文章页顶部的氛围色会自动取封面图的主色（不设封面则用主题色）。

### 3. 英文版（可选）

同目录放 `index-en.mdx`（同样 frontmatter）。没有英文版时，英文访客自动回退中文。

### 4. 发布

推送后 GitHub Actions 自动构建部署，**约 2 分钟生效**：

```bash
git add -A && git commit -m "文章：标题" && git push
```

若 `git push` 因网络失败（`Connection was reset`），用 API 推（脚本已配好）：

```bash
git add -A
GH_TOKEN=<你的token> node ../push-blog-api.mjs "文章：标题"
git fetch origin main && git reset --hard origin/main   # 同步本地
```

### 5. 本地预览（可选但推荐）

```bash
# Git Bash：
DEPLOYMENT_PLATFORM=github corepack pnpm dev      # 实时预览 http://localhost:4321
DEPLOYMENT_PLATFORM=github corepack pnpm build:github   # 完整构建到 dist/
DEPLOYMENT_PLATFORM=github corepack pnpm preview  # 预览构建产物
# PowerShell 里环境变量写法：$env:DEPLOYMENT_PLATFORM='github'
```

⚠️ `preview` 不带 `DEPLOYMENT_PLATFORM=github` 会报 Vercel 适配器错误。

---

## 二、修改个人主页

首页 = `src/pages/index.astro`，站点信息 = `src/site.config.ts`。改完同样推送即上线。

### 1. 首页各区块对照

| 你看到的 | 在哪改 |
|---|---|
| 大名字「GuitarYuu」 | `src/site.config.ts` → `author` |
| 位置/GitHub/邮箱 三个小标签 | `site.config.ts` → `personal.location / githubUsername / email` |
| 头像 | `src/assets/avatar.jpg`（整张替换同名文件即可，方形最佳） |
| About 一段话 | `src/pages/index.astro` 第 56 行附近 |
| Education 卡片（中南大学） | `src/pages/index.astro` 第 60~68 行 |
| Statistics 三个数字 | 自动统计（在线天数取 `personal.blogStartDate`） |
| GitHub 绿墙 | 自动（取 `personal.githubUsername`） |
| 页脚一言 | `site.config.ts` → `integ.quote`（当前 hitokoto 诗词） |

英文版首页 `src/pages/en/index.astro` 是独立文件，About/Education 目前还是主题英文占位，想改英文站记得同步。

### 2. 站点标题 / 菜单 / 页脚

都在 `src/site.config.ts`：

- `title`（浏览器标签标题）、`description`（SEO 描述）
- `header.menu`：顶部导航（Blog/Academic/Projects/Links/About）
- `footer.social`：页脚社交链接
- `personal.email`：⚠️ 当前公开展示，介意就删掉该行

### 3. About 页（点导航 About 进的页面）

`src/pages/about/index.astro`，还是主题默认内容，直接在该文件里按现有 `<Section>` 结构写自己的介绍。

### 4. 友链页

友链数据在 **`public/links.json`**（数组，每个对象含 name/url/avatar 等，照格式加）。「申请友链」展示的信息在 `site.config.ts` → `integ.links.applyTip`（头像引用的是 `public/avatar/avatar.png` 方形图）。

### 5. Academic / Projects 页

这两个页面的数据**直接写在各自的 astro 文件里**：`src/pages/academic/index.astro`、`src/pages/projects/index.astro`，搜索 `links:` / 卡片数组照葫芦画瓢。暂时不用就先不动。

### 6. 换皮肤 / 换字体

- 皮肤三选一：`src/layouts/BaseLayout.astro` 里三行 import，注释其余两行（青衿/青鸟/终霞，详见 README）
- 字体：整站霞鹜文楷（`tailwind.config.mjs` 的 sans 栈第一位）

---

## 三、删除主题示例文章

`src/content/blogs/` 下这些都是演示文，确认自己会写后可整目录删除：
`axi-theme-basics`、`deploy-vercel`、`waline`、`friend-circle`、`mdx-components`、`writing-markdown-mdx`、`tech`。
删掉后首页统计字数和 Blog 列表会自动更新。`src/content/collection/docs.md` 是 Collection 页示例，同理可删。

---

## 四、排错速查

| 症状 | 原因/处理 |
|---|---|
| 文章没出现在列表 | frontmatter 少必填字段/格式错；`draft: true`；文件名不是 `index.md` |
| 构建失败邮件 | 多半是 frontmatter 校验（超 60/160 字、日期格式），看 Actions 日志定位行 |
| push 被重置 | 大陆网络波动：重试几次；或用 API 推（见上） |
| `pnpm preview` 报错 | 忘了 `DEPLOYMENT_PLATFORM=github` |
| 搜索搜不到新文章 | 正常，搜索索引构建时生成，重新部署后即有 |
| 改了没生效 | Actions 还在跑（约 2 分钟）或浏览器缓存，Ctrl+F5 |

---

## 五、升级主题（上游更新时）

```bash
git fetch upstream
git merge upstream/main        # 冲突只可能在本方改过的文件，手工解
corepack pnpm install
DEPLOYMENT_PLATFORM=github corepack pnpm build:github   # 本地验证后再推
```
