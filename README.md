# GuitarYuu 的博客

基于 [Axi-Theme](https://github.com/axi404/Axi-Theme)（Astro）构建的个人博客。

- 在线地址：<https://guitaryuu.github.io/>
- 主题文档：<https://theme.axi404.top/collection/docs>

## 写文章

在 `src/content/blogs/<目录名>/index.md` 新建 Markdown 文件（含 frontmatter，参考已有文章），push 到 main 后 GitHub Actions 自动构建发布。

## 本地开发

```bash
corepack pnpm install
corepack pnpm dev        # 本地预览
DEPLOYMENT_PLATFORM=github corepack pnpm build:github   # 构建（产物在 dist/）
```

主题更新：与上游 [axi404/Axi-Theme](https://github.com/axi404/Axi-Theme) diff 合并（作者推荐 WinMerge）。
