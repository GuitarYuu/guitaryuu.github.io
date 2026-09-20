# RESUME — GuitarYuu 个人博客（guitaryuu.github.io）

> 最后更新：2026-09-20（三皮肤 + 自选头像）。**当前状态：已上线，默认皮肤「青衿」。**

## 站点信息

- 地址：https://guitaryuu.github.io/ （大陆直连可达）
- 仓库：GuitarYuu/guitaryuu.github.io（fork 自 axi404/Axi-Theme，Apache-2.0，主题文档 https://theme.axi404.top/collection/docs ）
- 本地目录：`C:\Users\25448\.zcode\workspace\default\blog\`；推送脚本 `..\push-blog-api.mjs`
- 技术栈：Astro 5 + Tailwind + MDX + Pagefind + KaTeX；构建产物 `dist/`

## 「青衿 Qingjin」主题层 + 三皮肤（2026-09-20）

字体与架构（三皮肤共用）：霞鹜文楷屏幕版（`lxgw-wenkai-screen-webfont/lxgwwenkaiscreen.css`），import 在 `src/layouts/BaseLayout.astro`（app.css 之后）。

**皮肤三选一**（改 BaseLayout.astro 的 import，注释其余两行）：

| 皮肤 | 文件 | 亮色 | 暗色 |
|---|---|---|---|
| 青衿【默认】 | `src/styles/qingjin.css` | 稿纸：米纸+靛青+格纹 | 板书：墨绿+粉笔黄+格线 |
| 青鸟 Aoi | `src/styles/liz.css` | 晴空：云白蓝+青鸟蓝+细雨纹 | 月夜：藏蓝+萤蓝 |
| 终霞 Yagate | `src/styles/yagate.css` | 霞空：粉→白→蓝渐变+玫瑰 | 夜霞：紫夜渐变+晚霞粉（引用块/分隔线粉蓝渐变） |

- 变量双写选择器（`:root:root`/`.dark.dark`）覆盖主题默认，上游合并零冲突
- ⚠️ 主题的 `customCss` 字段未接线（virtual:starlight/user-css 无消费方），不能用
- ⚠️ 文章页氛围色 = 头图主色 25% 顶栏渐变（主题特性 `heroImage.color`），非 bug

**头像**：`src/assets/avatar.jpg`（用户自选插画，青鸟少女），友链方形图 `public/avatar/avatar.png` 由 sharp attention 裁切；**主题作者原插画保留在 `src/assets/avatar.png`**（上游原路径，不参与渲染，仅留存/合并兼容）。favicon 仍为 ∑ svg。

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
cd blog && git add -A && GH_TOKEN=<token> node ../push-blog-api.mjs "提交说明"
# 脚本自动 diff origin/main 找出增/改/删文件，走 Git Data API 建提交（execSync 已设 64MB maxBuffer，大图可推）
# 推完：git fetch origin main && git reset --hard origin/main 同步本地
```

⚠️ 推送命令若带管道（`| tail`）必须先 `set -o pipefail`：管道会吞掉脚本退出码，导致失败后误执行后续 `reset --hard` 抹掉工作区（2026-09-20 踩过，已靠 git 历史+上下文全量恢复）。

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
