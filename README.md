# GuitarYuu 的博客

基于 [Axi-Theme](https://github.com/axi404/Axi-Theme)（Astro）构建的个人博客。

- 在线地址：<https://guitaryuu.github.io/>
- 主题文档：<https://theme.axi404.top/collection/docs>

## 「青衿 Qingjin」独创主题层

「青青子衿，悠悠我心」—— 为数理学子定制的视觉风格：

- **亮色「稿纸」**：暖米纸面 + 淡墨文字 + 靛青强调色 + 稿纸格纹底
- **暗色「板书」**：墨绿黑板 + 粉笔白文字 + 粉笔黄强调色 + 粉笔格线
- **字体**：霞鹜文楷屏幕版（`lxgw-wenkai-screen-webfont`，npm 自托管，按需子集加载）
- **细节**：靛青选中文本、批注式引用块、虚线正文链接、∑ favicon、首页中文一言（hitokoto）
- **实现**：走官方 customCss 扩展点 + 字体包（src/styles/qingjin.css），与上游合并几乎零冲突

## 写文章

在 `src/content/blogs/<目录名>/index.md` 新建 Markdown 文件（含 frontmatter，参考已有文章），push 到 main 后 GitHub Actions 自动构建发布。

## 本地开发

```bash
corepack pnpm install
corepack pnpm dev        # 本地预览
DEPLOYMENT_PLATFORM=github corepack pnpm build:github   # 构建（产物在 dist/）
```

主题更新：与上游 [axi404/Axi-Theme](https://github.com/axi404/Axi-Theme) diff 合并（作者推荐 WinMerge）。
