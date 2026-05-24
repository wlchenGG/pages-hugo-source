---
title: "Git 子模块实战：Hugo 主题 DoIt 的版本管理"
date: 2026-05-24T20:35:00+08:00
draft: true
categories:
  - 技术
tags:
  - git
  - submodule
  - hugo
  - theme
---

这篇记录一下我在博客仓库里管理 DoIt 子模块的实战过程：为什么会出现 `M themes/DoIt`、怎么升级主题、怎么保证多台电脑版本一致。

<!--more-->

## 一句话结论

Git 子模块的核心是：**主仓库只记录子模块的一个 commit 指针，不跟踪子模块分支自动前进。**

## 先说结论

- 子模块要管的是“指针”，不是“目录里的文件”。
- 不要把 `themes/DoIt` 加到 `.gitignore`。
- 升级主题后，一定要在主仓库提交一次 `themes/DoIt` 指针更新。

## 本次场景

博客仓库主题目录是：`themes/DoIt`，通过子模块引入。`git status` 出现：

```text
M themes/DoIt
```

这通常不是“主题源码被改坏了”，而是“子模块当前检出的 commit 和主仓库记录的 commit 不一致”。

## 子模块与主仓库的关系

### 1) 子模块是独立仓库

- 有自己的提交历史、分支、标签。
- `themes/DoIt` 里执行 `git log`，看到的是 DoIt 仓库历史，不是你的博客历史。

### 2) 主仓库保存的是指针

- 主仓库提交中，`themes/DoIt` 本质是一个 gitlink。
- 当你升级主题后，主仓库里只显示“子模块指针变更”。

### 3) 团队一致性来自“锁定提交”

- 其他机器拉源码后执行 `git submodule update --init --recursive`，会检出到锁定 commit。
- 这保证可复现，而不是“每个人都拉到子模块最新分支头”。

## 本次实际操作策略（按时间线）

### 1) 先把拉取通道改成 HTTPS（兜底）

由于本机 SSH 22 异常，先将 `.gitmodules` 改为 HTTPS，确保子模块能稳定拉取：

```ini
[submodule "themes/DoIt"]
    path = themes/DoIt
    url = https://github.com/HEIGE-PCloud/DoIt.git
```

并执行同步：

```bash
git submodule sync --recursive
```

这一步的目标只是先恢复可用，不是最终形态。

### 2) 升级到 DoIt 最新发布版

后续拉取上游后，发现 DoIt 已有更新版本，最终升级到 `v1.0.2`（而不是之前的 `v0.3.0`）。

```bash
git -C themes/DoIt fetch --tags --prune
git -C themes/DoIt checkout v1.0.2
```

然后回主仓库提交子模块指针变更。

### 3) 再把 SSH 固化到 443

会话后续又补了 SSH 443 方案：在 `~/.ssh/config` 将 `github.com` 映射到 `ssh.github.com:443`。这样可以继续使用 SSH URL，同时绕开 22 端口链路问题。

如果你完成了公钥注册，也可以把子模块 URL 切回 SSH 形式：

```ini
[submodule "themes/DoIt"]
    path = themes/DoIt
    url = git@github.com:HEIGE-PCloud/DoIt.git
```

并执行：

```bash
git submodule sync --recursive
```

是否切回 SSH，取决于你本机网络环境和团队习惯。

## DoIt 与 Hugo 的版本联动提醒

这次踩坑里最关键的一点：主题升级后，Hugo 也要跟着看最低版本要求。

- DoIt `v1.0.2` 要求 Hugo `>= 0.146.0`。
- 我本机后来升级到 Hugo `0.161.1` 才完全对齐。

另外，DoIt 配置里的 `params.version` 是“配置代际标识”，不是 Git tag。

- 旧文档常见 `0.2.X`
- 新文档示例是 `0.4.X`

这个字段和你拉到的主题模板要对应，不对应会触发兼容性报错。

## 是否要把子模块加入 .gitignore？

不建议。

原因：

1. 你会失去“版本漂移”可见性。
2. 机器间不可复现风险变高。
3. 升级和回滚难追踪。

正确方式是：**保留子模块受控，按需显式升级并提交指针。**

## 推荐管理流程

### 日常开发（稳定优先）

```bash
git pull --rebase --autostash
git submodule update --init --recursive
```

### 升级主题（变更可追踪）

```bash
git -C themes/DoIt fetch --tags --prune
git -C themes/DoIt checkout <target-tag-or-branch>
git add themes/DoIt
git commit -m "chore(theme): bump DoIt to <target-tag>"
```

### 回滚主题（快速止损）

```bash
git checkout <某个历史提交> -- themes/DoIt
git commit -m "revert(theme): rollback DoIt pointer"
```

### 新机器初始化（容易漏）

```bash
git submodule update --init --recursive
```

## 常见误区

- 误区 1：在子模块里 `git pull` 就等于主仓库完成升级。
  - 事实：还必须在主仓库提交子模块指针。
- 误区 2：子模块用了就会自动跟官方最新。
  - 事实：不会自动跟，除非你手动更新并提交。
- 误区 3：忽略子模块目录更省事。
  - 事实：短期省事，长期容易失控。

## 参考

- [Git Docs - git-submodule](https://git-scm.com/docs/git-submodule)
- [Atlassian - Git submodules](https://www.atlassian.com/git/tutorials/git-submodule)
