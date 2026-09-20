# RESUME — GuitarYuu 个人博客（guitaryuu.github.io）

> 最后更新：2026-09-20（青衿主题上线）。**当前状态：已上线，主题 v2「青衿」验收通过。**

## 站点信息

- 地址：https://guitaryuu.github.io/ （大陆直连可达）
- 仓库：GuitarYuu/guitaryuu.github.io（fork 自 axi404/Axi-Theme，Apache-2.0，主题文档 https://theme.axi404.top/collection/docs ）
- 本地目录：`C:\Users\25448\.zcode\workspace\default\blog\`；推送脚本 `..\push-blog-api.mjs`
- 技术栈：Astro 5 + Tailwind + MDX + Pagefind + KaTeX；构建产物 `dist/`

## 「青衿 Qingjin」独创主题层（2026-09-20）

「青青子衿」学子意象，为数理身份定制：

- **亮色「稿纸」**：暖米纸面 + 淡墨字 + 靛青强调 + 稿纸格纹；**暗色「板书」**：墨绿黑板 + 粉笔白 + 粉笔黄强调 + 粉笔格线
- 核心文件：`src/styles/qingjin.css`（变量双写 specificity 覆盖，零冲突）
- 字体：霞鹜文楷屏幕版 npm 自托管（`lxgw-wenkai-screen-webfont/lxgwwenkaiscreen.css`），97 个 woff2 子集按需加载；引入点在 `src/layouts/BaseLayout.astro`（app.css 之后）
- ⚠️ 主题的 `customCss` 配置字段在当前版本**未接线**（virtual:starlight/user-css 无消费方），别用它
- 独创头像：`src/assets/avatar.svg`（稿纸 + 靛青∑ + 朱印「青」），`public/avatar/avatar.png` 由 sharp 栅格化生成；主题作者的动漫原图已删除
- favicon：`public/favicon/favicon.svg`（∑），BaseHead.astro 加了 svg link、移除了 Satoshi preload
- 首页一言换成 hitokoto 中文诗词（大陆可达）；About/Education 占位文案已填（中南大学/数理方向，可在 `src/pages/index.astro` 改）
- 文章页背景的氛围色 = 头图主色 25% 顶栏渐变（主题特性 `heroImage.color`），示例文章橄榄色是它的演示头图所致，非 bug

## 主题升级流程（替代 WinMerge 的 git 方式）

上游已配好：`git remote add upstream https://github.com/axi404/Axi-Theme.git`（2026-09-20 检查过与 fork 点一致，无需合并）。

```bash
git fetch upstream            # 走全局 gh-proxy 镜像，只读可用
git merge upstream/main       # 冲突集中在本方改过的文件，手工解即可
corepack pnpm install && DEPLOYMENT_PLATFORM=github corepack pnpm build:github
```

我方改动面（合并冲突点）：README.md、package.json、pnpm-workspace.yaml、.github/workflows/deploy-pages.yml、src/site.config.ts、src/styles/qingjin.css、tailwind.config.mjs（一行）、src/components/BaseHead.astro（favicon/preload）、src/layouts/BaseLayout.astro（两行 import）、src/pages/index.astro、src/assets/avatar.*。

## 推送方式（重要）

`github.com:443` 直连可能被阻断（api.github.com 稳定可用），git push 失败时用 API 推：

```bash
cd blog && git add -A && GH_TOKEN=<token> node ../push-blog-api.mjs
# 脚本自动 diff origin/main 找出增/改/删文件，走 Git Data API 建提交
# 推完：git fetch origin main && git reset --hard origin/main 同步本地
```

git 直连恢复时：临时 `git config --global --unset-all url.https://gh-proxy.com/https://github.com/.insteadof` 后 push，推完恢复镜像配置（见记忆 env-china-network）。

## 日常写文章

`src/content/blogs/<slug>/index.md(x)` 新建（frontmatter 参考 axi-theme-basics），push 后 Actions 自动部署（约 2 分钟）。
本地预览：`DEPLOYMENT_PLATFORM=github corepack pnpm dev`；构建：`DEPLOYMENT_PLATFORM=github corepack pnpm build:github`；预览构建产物：`DEPLOYMENT_PLATFORM=github corepack pnpm preview`（不带 env 会报 Vercel 适配器错误）。

## 待办（用户自决）

- [ ] **删除示例文章**：src/content/blogs/ 下 7 篇主题演示文（含英文版 en/index.astro 首页里的 About/Education 也要同步改）
- [ ] QQ 邮箱公开展示问题（site.config.ts personal.email，介意就删）
- [ ] About 页（src/pages/about/index.astro）还是主题默认内容
- [ ] 主题升级时按上文流程 merge upstream
- [ ] 想绑自定义域名：Pages 设置 + site.config.ts domains
