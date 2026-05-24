---
title: "另一台 PC 同步 Hugo/DoIt 环境简流程"
date: 2026-05-25T10:20:00+08:00
draft: true
categories:
  - 技术
tags:
  - Hugo
  - DoIt
  - Git
  - 环境同步
---

这次在笔记本上已经完成了 Hugo 和 DoIt 的升级。为了避免另一台 PC 出现“本地能跑/不能跑不一致”，这里整理一份最短流程，按顺序执行即可。

<!--more-->

## 0. 先更新源码仓库

在 PC 的博客源码目录执行：

```bash
git pull --rebase --autostash
git submodule sync --recursive
git submodule update --init --recursive
```

这一步会把 DoIt 子模块同步到仓库当前锁定版本。

## 1. 安装（或切换）Hugo 版本

本仓库当前使用 Hugo `0.161.1`（extended）。

在 PC 上确认版本：

```bash
hugo version
```

如果不是 `0.161.1`（或更低版本），请安装 `hugo_extended_0.161.1_windows-amd64.zip`，并把 `hugo.exe` 路径加入 `Path`。

## 2. 安装主题依赖（DoIt v1）

DoIt 新版本需要 Node 依赖来构建样式。在仓库根目录执行：

```bash
npm --prefix themes/DoIt ci
```

如果你的 PC 没装 Node.js，先安装 Node LTS（建议 20+）。

## 3. 本地验证

先做一次构建：

```bash
hugo --gc --minify
```

再本地预览：

```bash
hugo server -D --disableFastRender
```

重点看首页导航、文章列表、图标、友情链接页和你自定义过的 shortcode 页面。

## 4. 常见问题速查

- 报 `languageCode`/`:filename` 相关错误：说明配置还没同步到新版，先 `git pull`。
- 页面结构有了但样式乱：多半是缓存问题，浏览器 `Ctrl+F5` 强刷。
- CI 正常、本地异常：优先检查 `hugo version` 与 `themes/DoIt` 子模块版本是否一致。

## 5. 一条命令快速自检（可选）

```bash
git rev-parse --short HEAD && git submodule status --recursive && hugo version
```

把输出和笔记本对齐，基本就说明两台机器环境一致了。
