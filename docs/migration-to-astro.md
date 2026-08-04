# Hexo 到 Astro 迁移记录

开始迁移日期：2026-07-30  
完成验证日期：2026-08-04

## 迁移目标

将原 Hexo 个人博客迁移为 Astro 静态站点，保留原有个人博客定位、页面内容、域名、图片资源、社交信息、备案信息和版权声明，同时降低主题耦合，方便后续扩展文章、碎碎念、项目展示和 AI 工具入口。

## 原站点概况

- 框架：Hexo 8
- 主题：hexo-theme-tangyuxian
- 域名：lyccyl.cn
- 内容：关于我、文章归档、友情链接、碎碎念、默认文章 Hello World
- 静态资源：头像、背景图和文章标签封面图
- 发布方式：Hexo deploy 到 `biubiubi4/biubiubi4.github.io` 的 `master` 分支

## 关键变更

### 框架模块

- `package.json` 从 Hexo 命令切换为 Astro 命令。
- `package-lock.json` 已重新生成为 Astro 依赖锁文件。
- 新增 `astro.config.mjs`，站点地址设置为 `https://lyccyl.cn`。
- 新增 `tsconfig.json` 和 `src/env.d.ts`，使用 Astro 的 TypeScript 配置。

### 内容模块

- 原 `source/about.md` 迁移为 `src/pages/about.astro`。
- 原 `source/friend.md` 迁移为 `src/pages/friend.astro`。
- 原 `source/memos.md` 迁移为 `src/pages/memos.astro`。
- 原 `source/archives.md` 迁移为 `src/pages/archives.astro`。
- 原 `source/_posts/hello-world.md` 迁移为 `src/content/blog/hello-world.md`。
- 新增 `src/content/config.ts`，使用 Astro Content Collections 管理文章。

### 站点信息

- 原 `_config.yml` 和 `_config.tangyuxian.yml` 中的重要信息迁移到 `src/data/site.ts`。
- 保留标题、描述、作者、GitHub、邮箱、头像、背景、备案号、AI 分身链接和版权协议。
- “关于我”的工具栈中将 Hexo 更新为 Astro，以反映迁移后的实际技术栈。

### 视觉与布局

- 新增 `src/layouts/BaseLayout.astro` 作为全站基础布局。
- 新增 `src/layouts/BlogPostLayout.astro` 作为文章详情布局。
- 新增 `src/components/PostList.astro` 作为文章列表组件。
- 首页改为个人主页式入口，包含最近文章、关注主题和碎碎念入口。

### 静态资源

- Astro 使用 `public` 目录作为静态资源目录。
- 保留 `/images/head/head.svg`、`/images/background/xiaomai.svg` 等原有资源路径，避免页面引用失效。
- 保留 `public/CNAME`，内容为 `lyccyl.cn`，用于自定义域名。

### 部署模块

- 新增 `.github/workflows/deploy.yml`。
- GitHub Pages 改为由 GitHub Actions 执行 `npm ci` 和 `npm run build`，并发布 `dist` 目录。
- `.gitignore` 已更新为忽略 `dist/`、`.astro/` 和 `node_modules/`，不再忽略 Astro 所需的 `public/`。

## 已清理的 Hexo 文件

以下 Hexo 专用文件和目录已删除，避免仓库同时维护两套框架：

- `_config.yml`
- `_config.tangyuxian.yml`
- `scaffolds/`
- `source/`
- `themes/`
- `db.json`
- `.deploy_git/`
- `.git-template-empty/`
- `public` 中旧 Hexo 生成的 HTML、CSS、JS、lib、archives、preset 等产物

`public/images` 和 `public/CNAME` 已保留。

## 验证结果

已执行：

```bash
npm install
npm run build
```

构建结果：

- Astro 类型检查通过。
- Astro 静态构建通过。
- 生成 6 个页面：首页、关于、归档、文章详情、友链、碎碎念。
- 构建产物输出到 `dist/`，该目录不提交，由 GitHub Actions 发布。

## 注意事项

- `npm install` 后提示 4 个依赖审计问题，其中 1 个 moderate、3 个 high。未执行 `npm audit fix --force`，因为强制修复可能引入破坏性依赖升级。
- 本机未安装 GitHub CLI `gh`，因此无法在本机用 `gh` 自动创建 Pull Request；本次会优先推送迁移分支到 GitHub。
- 如果 GitHub Pages 当前仍配置为“从分支发布”，需要在仓库设置中切换为“GitHub Actions”发布。

## 后续建议

- 将默认的 Hello World 替换为真正的第一篇文章。
- 将碎碎念页面接入 Memos API，或改成本地 Markdown 数据源。
- 增加按标签过滤、站内搜索、RSS、sitemap 和评论系统。
