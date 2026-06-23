---
title: '使用 Astro 构建高性能静态站点'
description: '为什么我选择 Astro 作为个人站框架，以及它如何解决内容型站点的性能痛点。'
pubDate: 2024-06-15
tags: ['Astro', '前端性能', '静态站点']
---

在内容型网站中，首屏速度和 SEO 往往是最关键的指标。传统框架为了交互性引入了沉重的客户端 JavaScript，而对于博客、文档或作品集来说，这些成本很多时候是不必要的。

## 为什么选择 Astro

Astro 的核心理念是 **群岛架构（Islands Architecture）**：默认输出零 JavaScript 的静态 HTML，只在需要交互的地方按需注入脚本。这使得：

- 首屏加载极快
- Lighthouse 分数容易保持高位
- 内容可以用熟悉的 Markdown / MDX 管理

## 与 Cloudflare 集成

由于域名已经托管在 Cloudflare，把站点部署到 Cloudflare Pages 是最自然的选择。只需连接 GitHub 仓库，设置构建命令为 `npm run build`，输出目录为 `dist`，即可实现推送即部署。

## 写在最后

对于前端开发者来说，Astro 既保留了组件化的开发体验，又提供了静态站点应有的简洁和性能，是搭建个人主页的绝佳选择。
