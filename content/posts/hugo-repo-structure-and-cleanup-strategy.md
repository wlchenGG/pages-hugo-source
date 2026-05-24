---
title: "Hugo 仓库结构与清理策略：哪些该提交，哪些可删除"
date: 2026-05-24T20:40:00+08:00
draft: true
categories:
  - 技术
tags:
  - hugo
  - git
  - blog
---

Hugo 项目里最容易乱的，往往不是写文章，而是仓库管理：哪些目录该提交，哪些只是构建产物，升级后报错该怎么排查。本文结合我这次实战，整理一套可复用的清单。

<!--more-->

## 先给结论

- `content/`、`layouts/`、`assets/`、`static/`、`config.*`：通常应该提交。
- `public/`：通常不提交（纯构建产物）。
- `resources/_gen/`：通常不提交（Hugo 生成缓存/中间产物）。
- `themes/<name>`：若是子模块，不应忽略，应作为子模块指针管理。

## 目录作用速览

### `content/`

Markdown 文章源文件，属于核心内容资产，必须入库。

### `static/`

静态资源原样发布到站点根目录，通常入库。

### `assets/`

交给 Hugo Pipes 处理的资源源文件（如 SCSS），属于源码，建议入库。

### `layouts/`

模板覆盖层，属于站点行为定义，建议入库。

### `themes/`

主题代码目录。如果使用子模块，主仓库记录主题 commit 指针而不是主题全部历史。

### `public/`

`hugo` 构建输出目录，删除后可重新生成。一般放进 `.gitignore`。

### `resources/_gen/`

Hugo 生成的资源缓存与中间结果，通常可删除并在下次构建自动再生。

## 本仓库清理实践

本次针对博客仓库做了两件标准化处理：

1. `.gitignore` 增加 `resources/_gen/`。
2. 对已被跟踪的 `resources/_gen/*` 执行 `git rm --cached`，从版本控制中移除。

这样做的好处：

- 减少无意义变更噪音。
- 降低提交体积。
- 避免不同机器换行或构建差异导致重复变动。

## 什么时候可以放心清理

通常可随时清理：

```bash
Remove-Item -Recurse -Force public
Remove-Item -Recurse -Force resources/_gen
```

再次构建：

```bash
hugo
```

不建议清理或误删：

- `content/`
- `assets/`
- `layouts/`
- `config.toml` 或 `hugo.toml`

## 建议的 .gitignore 片段

```gitignore
public/
resources/_gen/
```

如果你会在本机生成临时编辑器文件，也建议补充 IDE 相关忽略规则。

## 与子模块协作时的注意点

1. 不要忽略 `themes/DoIt`。
2. 升级主题要显式提交子模块指针。
3. 新机器拉取后记得初始化子模块：

```bash
git submodule update --init --recursive
```

## 本机 Hugo 安装清单（Windows，手动安装）

前面想本地渲染时，终端提示 `hugo` 命令不存在，说明本机还没把 Hugo 主程序装好或加入环境变量。

建议优先安装和仓库 CI 一致的 Hugo 版本，避免“本地能跑、云端失败”。

### 安装目标

- 版本：`0.161.1`
- 发行类型：`extended`
- 平台：`Windows amd64`

### 安装步骤

1. 打开 Hugo Release 页面，下载 `hugo_extended_0.161.1_windows-amd64.zip`。
2. 新建本地目录，比如 `D:\Dev\hugo_0.161.1\`。
3. 解压压缩包，把 `hugo.exe` 放到该目录。
4. 打开 **此电脑 -> 属性 -> 高级系统设置 -> 环境变量**。
5. 在用户变量或系统变量中找到 `Path`，点击编辑。
6. 新增一条 Hugo 路径：`D:\Dev\hugo_0.161.1\`。
7. 点击保存，关闭所有窗口。
8. 重新打开 PowerShell 或 VS Code 终端。
9. 执行 `hugo version`，确认能输出版本号。

### 本仓库本地渲染命令

```bash
hugo server -D
```

`-D` 代表把 `draft: true` 的草稿文章也渲染出来，适合写作阶段预览。

## Hugo 升级检查清单（通用）

Hugo 最新版功能更多，但跨多个版本升级时，不建议直接在主分支硬切。推荐走“新分支验证 -> 云端构建验证 -> 再合并”的稳妥流程。

### 为什么要谨慎升级

- 新版有安全策略收紧（例如 `security.http.urls` 默认更严格）。
- Node 相关工具（如 Tailwind/PostCSS）在新版本有权限模型变化。
- 即使主题最低版本兼容，也可能出现模板细节或渲染行为差异。

### 升级流程（推荐）

1. 新建升级分支：`chore/upgrade-hugo`。
2. 本机先安装目标 Hugo 版本（仍建议 `extended`）。
3. 将仓库 `.env` 中 `HUGO_VERSION` 改为目标版本。
4. 本地执行：

```bash
hugo --minify
hugo server -D
```

5. 重点检查页面：
   - 首页、文章列表、文章详情页
   - 标签页、分类页、RSS
   - 友链页、你自定义过的 shortcode 页面
6. 推送升级分支，观察 GitHub Actions 是否构建通过。
7. 若本地和云端都正常，再合并到 `main`。

### 建议的回滚预案

如果升级后出现异常，可快速回退：

1. 把 `.env` 的 `HUGO_VERSION` 改回上一个稳定值。
2. 本机切回旧 Hugo 可执行文件。
3. 重新构建验证并提交修复 commit。

这套做法可以把风险控制在很小范围，不影响持续写作和发布。

## 升级到 Hugo 0.161.1 的实战修复记录

这次本仓库把 Hugo 升级到 `0.161.1` 后，首次本地运行出现两类典型报错：

1. `languageCode` 已废弃，提示改为 `locale`。
2. `:filename` permalink token 已移除，提示改为 `:contentbasename`。

对应修复如下。

### 修复 1：站点语言配置键

把：

```toml
languageCode = "zh-CN"
```

改为：

```toml
locale = "zh-CN"
```

### 修复 2：永久链接 token

把：

```toml
[Permalinks]
posts = "posts/:filename"
```

改为：

```toml
[Permalinks]
posts = "posts/:contentbasename"
```

### 验证命令

```bash
hugo --minify
hugo server -D --disableFastRender
```

构建通过后，再继续检查首页、文章页、标签页和你常用的 shortcode 页面。

### 小提示

- `hugo server` 在 `development` 环境下，DoIt 会提示评论/PWA/CDN/fingerprint 未启用，这是正常提示。
- 若要在本地模拟生产行为，可用：

```bash
hugo server -e production -D --disableFastRender
```

## 本次主题升级联动记录（DoIt）

这次从 DoIt `v0.3.0` 升到 `v1.0.2` 时，还出现过一轮版本联动问题：

- 新主题要求 Hugo `>= 0.146.0`。
- 本机 Hugo 还在 `0.145.0` 时，会出现模板/shortcode 相关错误。

处理顺序建议固定为：

1. 先升级 DoIt 子模块并提交指针。
2. 再把本机和 CI 的 Hugo 版本同步升级。
3. 最后处理配置迁移（`locale`、permalink token 等）并回归验证。

## 小结

把 Hugo 仓库管理好，本质就是“源码入库，产物不入库，外部依赖可复现”。

做到这三点，博客项目在多设备、多时间线下都能保持稳定、可维护。
