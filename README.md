# Elric Li 个人主页

基于 [Astro](https://astro.build) + [Tailwind CSS](https://tailwindcss.com) 构建的静态个人站点。

## 页面

- `/` 首页：个人简介、最新博客、精选项目
- `/about` 关于我：详细介绍、技术栈、经历
- `/blog` 博客：技术文章列表
- `/blog/:slug` 博客文章详情（Markdown）
- `/projects` 项目：作品集展示
- `/contact` 联系：邮箱与社交媒体

## 项目结构

```text
├── src/
│   ├── components/     # 可复用组件
│   ├── content/        # 博客文章和项目数据
│   ├── layouts/        # 页面布局
│   ├── pages/          # 路由页面
│   └── styles/         # 全局样式
├── public/             # 静态资源
├── astro.config.mjs
├── content.config.ts   # 内容集合配置
└── tailwind.config.mjs
```

## 本地开发

```bash
npm install
npm run dev
```

开发服务器运行在 http://localhost:4321。

## 构建

```bash
npm run build
```

构建产物输出到 `./dist/` 目录。

## 部署到 Cloudflare Pages

1. 将代码推送到 GitHub 仓库。
2. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com) → Pages → 创建项目。
3. 连接 GitHub 仓库，框架预设选择 **Astro**（或手动填写）。
4. 构建设置：
   - Build command: `npm run build`
   - Build output directory: `dist`
5. 保存并部署。
6. 部署成功后，进入项目设置 → 自定义域名，添加 `elricli.com`。
7. Cloudflare 会自动配置 DNS 和 SSL 证书，稍等片刻即可通过 `https://elricli.com` 访问。

## 自定义内容

- **个人资料**：修改 `src/pages/about.astro`、`src/pages/contact.astro` 和 `src/components/Hero.astro`。
- **项目**：编辑 `src/content/projects.json`。
- **博客**：在 `src/content/blog/` 下新建 `.md` 文件，按现有格式添加 frontmatter。

## 技术栈

- Astro 7
- Tailwind CSS 4
- TypeScript
- Markdown
