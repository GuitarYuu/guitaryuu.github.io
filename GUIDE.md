# 博客维护指南（详细版）

> 仓库 `GuitarYuu/guitaryuu.github.io` · 线上 <https://guitaryuu.github.io/> · 本地 `D:\github\blog`
> 主题：Axi-Theme（Astro 5 + Tailwind + MDX + Pagefind 搜索 + KaTeX 公式）· 官方文档：<https://theme.axi404.top/collection/docs>

**改任何东西都是同一条流水线：改文件 → `git add -A && git commit -m "说明" && git push` → 约 2 分钟自动上线。**
推送被网络重置时用 API 推（见 §6）。拿不准就先本地预览（见 §5）。

---

## 0. 常用命令速查

| 想做什么 | 在 `D:\github\blog` 下执行（Git Bash） |
|---|---|
| 实时预览写作 | `DEPLOYMENT_PLATFORM=github corepack pnpm dev` → 开 <http://localhost:4321> |
| 完整构建测试 | `DEPLOYMENT_PLATFORM=github corepack pnpm build:github` |
| 预览构建产物 | `DEPLOYMENT_PLATFORM=github corepack pnpm preview` |
| 发布 | `git add -A && git commit -m "说明" && git push` |
| 推送失败时 API 推 | `git add -A && GH_TOKEN=<token> node ../push-blog-api.mjs "说明"` |
| 新装依赖（换机器/删过 node_modules） | `corepack pnpm install` |

PowerShell 里环境变量写法：`$env:DEPLOYMENT_PLATFORM='github'`。⚠️ `preview` 不带该变量会报 Vercel 错误。

---

## 1. 认识你的博客

```
你（D:\github\blog） --push--> GitHub 仓库 --Actions 自动构建--> GitHub Pages（线上）
```

线上只认 GitHub 仓库里的内容；本地文件夹只是工作副本，搬动/删除本地不影响已上线的站点（但会丢未推送的改动，所以改完记得 push）。

```
D:\github\blog
├─ src/
│  ├─ site.config.ts        ★ 站点总配置（标题/菜单/个人信息/页脚/一言）
│  ├─ styles/               ★ 三套皮肤（qingjin / liz / yagate）
│  ├─ layouts/BaseLayout.astro   皮肤切换 import 在这里
│  ├─ content/blogs/        ★ 你的文章（一个文件夹一篇）
│  ├─ content/collection/    合集页示例（可删）
│  ├─ pages/
│  │  ├─ index.astro        ★ 首页（About/Education 区块）
│  │  ├─ about/index.astro  ★ About 页
│  │  ├─ academic|projects|links/   对应导航页
│  │  └─ en/                英文版首页
│  ├─ components/           主题组件（一般不用动）
│  └─ assets/avatar.jpg     ★ 你的头像
├─ public/
│  ├─ links.json            ★ 友链数据
│  ├─ avatar/avatar.png     友链申请展示的方形头像
│  ├─ favicon/favicon.svg   站点图标（∑）
│  └─ images/               固定 URL 的静态图片
├─ tailwind.config.mjs      字体栈（第一位=霞鹜文楷）
├─ .github/workflows/       自动部署（不用动）
├─ GUIDE.md / RESUME.md / README.md
└─ ../push-blog-api.mjs     推送失败时的 API 备用脚本
```

---

## 2. 写文章

### 2.1 最小模板（抄这份就能发）

新建 `src/content/blogs/my-first-post/index.md`（**文件夹名 = 文章链接**）：

```markdown
---
title: 我的第一篇文章
description: 一句话摘要，会出现在列表、搜索和分享卡片里
publishDate: 2026-09-20
category: tech
tags:
  - 随笔
---

正文从这里开始。
```

### 2.2 frontmatter 完整字段（schema：`src/content.config.ts`）

| 字段 | 必填 | 限制/格式 | 说明 |
|---|---|---|---|
| `title` | ✅ | ≤60 字 | 文章标题 |
| `description` | ✅ | ≤160 字 | 摘要，也进搜索索引 |
| `publishDate` | ✅ | `YYYY-MM-DD` | 发布日期（排序依据） |
| `updatedDate` | ❌ | 同上 | 修改日期，显示"Update" |
| `category` | ❌ | 任意字符串 | 顶部 Blog 下拉的分组 |
| `tags` | ❌ | 数组 | 标签，自动去重转小写 |
| `language` | ❌ | `zh` | 标记语言 |
| `draft: true` | ❌ | — | 草稿：本地可见，不会构建上线 |
| `comment: false` | ❌ | — | 关闭该文评论区 |
| `heroImage` | ❌ | 见 2.5 | 封面图 + 页面氛围色 |

### 2.3 分类与标签的可见效果

- `category`：顶部「Blog ▾」下拉按分类分组；文章页顶部显示分类徽章
- `tags`：文章页显示标签 chips；`/tags` 页汇总所有标签
- 建议分类控制在少数几个固定值（如 `tech` / `math` / `life`），标签随意

### 2.4 草稿与双语

- **草稿**：frontmatter 加 `draft: true`，写完删掉这行再发布
- **双语**：同目录加 `index-en.mdx`（frontmatter 相同，内容英文）；没有英文版时英文读者自动看中文

### 2.5 封面图与正文图片

**封面图**（可选）：图片放文章文件夹里，frontmatter 写相对路径：

```markdown
heroImage:
  src: ./cover.jpg
  alt: 封面说明
```

文章页顶部的氛围色会自动取封面图主色（不设则用主题色）。

**正文图片**：也放文章文件夹里，正文 `![说明](./pic.jpg)`，自动进优化管线。需要固定外链 URL 的图放 `public/images/`，正文用 `/images/xxx.jpg` 引用。

### 2.6 数学公式与代码块

**数学公式**（KaTeX 自动渲染）：

```markdown
行内：$E = mc^2$

块级：
$$
\int_0^1 x^2 \, dx = \frac{1}{3}
$$
```

**代码块**（Shiki 高亮 + 复制按钮 + 标题）：

````markdown
```python title="demo.py"
print("hello")   # 普通行
print("新增")    # [!code ++]
print("删除")    # [!code --]
print("重点")    # [!code highlight]
```
````

### 2.7 进阶组件（`.mdx` 才能用）

`.md` 不支持 import；想用组件就把文件命名成 `index.mdx`，文件头加：

```mdx
import { Aside, Tabs, TabItem, Spoiler, Collapse, Steps, Timeline, Card } from '@/components/user'
import { GithubCard, LinkPreview, QRCode, ImageGroup, WebVideo } from '@/components/advanced'
```

常用示例（更多照 `src/content/blogs/mdx-components/` 演示文抄）：

```mdx
<Aside type='tip' title='提示'>
侧边提示框（type 还有 note / caution / danger 等）。
</Aside>

<Tabs>
  <TabItem label='标签A'>内容 A</TabItem>
  <TabItem label='标签B'>内容 B</TabItem>
</Tabs>

<Spoiler label='点击展开剧透'>隐藏内容</Spoiler>

<GithubCard repo='axi404/Axi-Theme' />
<LinkPreview href='https://astro.build/' />
```

### 2.8 发文 SOP（照做即可）

1. `src/content/blogs/<slug>/index.md` 套 §2.1 模板写正文
2. 本地 `pnpm dev` 预览，确认排版、公式、图片
3. `git add -A && git commit -m "文章：标题" && git push`
4. 等 2 分钟刷新线上确认；站内搜索能搜到即索引完成

---

## 3. 修改个人主页

### 3.1 首页 `src/pages/index.astro`

| 页面上看到的 | 改哪里 |
|---|---|
| 大名字 GuitarYuu | `site.config.ts` → `author`（英文版 `author_en`） |
| 位置/GitHub/Email 标签 | `site.config.ts` → `personal.location` / `githubUsername` / `email` |
| 头像 | 整张替换 `src/assets/avatar.jpg`（方形最佳） |
| About 一段话 | `index.astro` 第 56 行附近，直接改那段文字 |
| Education 卡片 | `index.astro` 第 60~68 行的 `heading/subheading/date` |
| Statistics 数字 | 自动统计（起始日 = `personal.blogStartDate`） |
| GitHub 绿墙 | 自动（取 `personal.githubUsername`） |
| 页脚一言 | `site.config.ts` → `integ.quote` |

About 区块改法示例（当前内容如下，改成你要说的话即可）：

```astro
<p class='text-muted-foreground'>CSU 数理方向在读。写写学习笔记、代码，以及一些灵光一现的想法。也在运营一个数学笔记站：<a class='hover:text-primary' href='https://guitaryuu.github.io/csu-shuliren/'>CSU数理人</a>。</p>
```

### 3.2 站点总配置 `src/site.config.ts`（逐项）

| 字段 | 作用 |
|---|---|
| `title` / `description` | 浏览器标签标题 / SEO 描述 |
| `author` / `author_en` | 站长名（首页大标题、版权） |
| `header.menu` | 顶部导航。加一项 = 数组加 `{ title: '名字', link: '/路径' }` |
| `footer.social` | 页脚社交链接（当前指向你的 GitHub 主页） |
| `personal.location` / `email` / `githubUsername` | 首页标签。⚠️ email 公开展示，介意就删 |
| `personal.blogStartDate` | 统计"在线天数"起点 |
| `personal.domains` | 域名（绑自定义域名时改） |
| `integ.quote` | 页脚一言（当前 hitokoto 诗词接口，大陆可直连） |
| `integ.waline.enable` | 评论系统（当前关闭；开启需自建 Waline 服务端） |

### 3.3 About 页

`src/pages/about/index.astro`，目前是主题默认内容。照文件里已有的区块结构替换成自己的经历、技能、联系方式。改完如需英文版，`en` 路由对应文件同步。

### 3.4 友链页

- 友链列表：**`public/links.json`**，数组里每个对象照已有格式加
- 「申请友链」卡片：`site.config.ts` → `integ.links.applyTip`（头像字段引用 `public/avatar/avatar.png` 方形图）

### 3.5 Academic / Projects 页

数据直接写在页面文件里：`src/pages/academic/index.astro`、`src/pages/projects/index.astro`。找卡片数组（含 `title`/`description`/`links:` 的对象）照格式增删。暂时用不上可不动，或在 §3.2 的 `header.menu` 里删掉对应导航项。

### 3.6 图标

- 站点 favicon：`public/favicon/favicon.svg`（当前 ∑，换任意 svg 即可）
- 换后浏览器有缓存，强刷或无痕窗口查看

---

## 4. 皮肤与外观

三套皮肤三选一，改 `src/layouts/BaseLayout.astro` 第 13~16 行：**注释其余两行、只放开一行**。

```ts
// import '../styles/qingjin.css'   // 青衿：亮=稿纸(靛青格纹) 暗=板书(粉笔黄)
import '../styles/liz.css'          // 青鸟 Aoi【当前默认】：亮=晴空 暗=月夜
// import '../styles/yagate.css'   // 终霞 Yagate：粉蓝白渐变暧昧色（霞空/夜霞）
```

- 字体：整站霞鹜文楷（`tailwind.config.mjs` sans 栈第一位），想去掉把该项删除即可
- 亮/暗色跟随系统自动切换，站内右上角按钮可手动切

---

## 5. 本地开发环境

- 依赖：Node（自带 corepack）+ pnpm（本机用 `corepack pnpm` 调用）
- 首次/换机器/依赖异常：`corepack pnpm install`（可重复执行，幂等）；彻底坏就删整个 `node_modules` 重装
- `dist/` 是构建产物，可随时删，重新 build 就有
- `dev` 模式改文件即时热更新，写作时开着最方便

---

## 6. 发布与推送

**正常**：`git add -A && git commit -m "说明" && git push`。

**push 报 Connection reset / Could not connect**（大陆访问 github.com 波动）时，用 API 推（api.github.com 稳定）：

```bash
git add -A
GH_TOKEN=<你的token> node ../push-blog-api.mjs "提交说明"
# 成功后同步本地：
git fetch origin main && git reset --hard origin/main
```

- 脚本自动找出所有增/改/删的文件，通过 GitHub API 直接建提交，效果等同 push
- token 就是本机凭据管理器里存的 GitHub classic token（`git push` 用的同一个）
- ⚠️ 推送命令带管道（`| tail`）时要先 `set -o pipefail`，否则失败后会误执行后续命令

---

## 7. 排错速查

| 症状 | 原因/处理 |
|---|---|
| 文章没出现在列表 | frontmatter 缺必填/格式错（title≤60、description≤160、日期格式）；`draft: true`；文件名必须是 `index.md`/`index.mdx` |
| 构建失败（Actions 红叉） | 多为 frontmatter 校验，开 Actions 失败日志搜 `error` 定位文件行 |
| push 连不上 | 重试几次；仍不行走 §6 API 推 |
| `pnpm preview` 报错 | 少了 `DEPLOYMENT_PLATFORM=github` |
| 改了没生效 | Actions 还在跑（约 2 分钟）+ 浏览器缓存（Ctrl+F5） |
| 搜索搜不到新文 | 部署完成后的刷新即有；再不行强刷 |
| 页面某图挂了 | 相对路径：正文 `./xx.jpg` 相对文章文件夹；`/images/` 相对 public |
| 依赖装不上 | 换网络重试；`corepack pnpm install` 幂等可重跑 |

---

## 8. 主题升级（上游更新时）

```bash
git fetch upstream
git merge upstream/main
corepack pnpm install
DEPLOYMENT_PLATFORM=github corepack pnpm build:github   # 本地验证再推
```

我方改动面（冲突点，手解即可）：`README.md`、`GUIDE.md`、`RESUME.md`、`package.json`、`pnpm-workspace.yaml`、`.github/workflows/deploy-pages.yml`、`src/site.config.ts`、`src/styles/`（三皮肤，新增文件不冲突）、`tailwind.config.mjs`（一行字体）、`src/components/BaseHead.astro`（favicon/preload 两处）、`src/layouts/BaseLayout.astro`（两行 import）、`src/pages/index.astro`、`src/assets/avatar.*`。

---

## 9. 待办清单（自己决定何时做）

- [ ] 删 7 篇主题示例文（`src/content/blogs/` 下），顺带删 `src/content/collection/docs.md`
- [ ] 重写 About 页（§3.3）
- [ ] 英文版首页 `src/pages/en/index.astro` 的 About/Education 还是英文占位
- [ ] `site.config.ts` 的 `personal.email` 公开问题，介意就删
- [ ] 想开评论：自建 Waline 后 `integ.waline.enable` 改 true 并填 server
- [ ] 绑自定义域名：仓库 Pages 设置 + `site.config.ts` 的 `domains`
