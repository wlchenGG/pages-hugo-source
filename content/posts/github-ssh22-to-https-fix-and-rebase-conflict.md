---
title: "Git Pull 报错 port 22 关闭：排查与修复实录"
date: 2026-05-24T20:30:00+08:00
draft: true
categories:
  - 技术
tags:
  - git
  - github
  - ssh
  - rebase
---

记录一次真实的 Git 故障处理过程，作为排查备忘：`git pull` 报 `Connection closed by ... port 22`，先用 HTTPS 兜底恢复同步，再切回 SSH 并固定到 443 端口。

<!--more-->

## 问题现象

在本地 Hugo 博客源码仓库执行：

```bash
git pull
```

报错：

```text
Connection closed by 20.205.243.166 port 22
fatal: Could not read from remote repository.
```

## 根因分析

先检查远程地址：

```bash
git remote -v
```

结果为 SSH 格式：

```text
origin  git@github.com:wlchenGG/pages-hugo-source.git (fetch)
origin  git@github.com:wlchenGG/pages-hugo-source.git (push)
```

再测试 SSH 连通：

```bash
ssh -T git@github.com
```

仍然是 `port 22` 连接关闭，说明当前网络环境无法正常使用 GitHub SSH 22 端口。

另外本机 `~/.ssh` 仅有 `known_hosts`，没有可用私钥，SSH 方案本身也不完整。

## 修复步骤

### 1) 将远程仓库改为 HTTPS

```bash
git remote set-url origin https://github.com/wlchenGG/pages-hugo-source.git
git remote -v
```

再验证远程可达：

```bash
git ls-remote --heads origin
```

返回了 `refs/heads/main`，说明 HTTPS 通道正常。

### 2) 执行更稳妥的拉取命令

```bash
git pull --rebase --autostash
```

这条命令的作用：

- `pull`：抓取远端更新并整合。
- `--rebase`：把本地提交“接”到远端新提交之后，避免额外 merge commit。
- `--autostash`：有未提交改动时自动暂存，rebase 完再自动恢复。

## 冲突处理复盘

本次 rebase 中，` .gitignore` 和 `content/posts/github_token.md` 发生冲突。

处理过程：

1. 先查看冲突文件内容。
2. 决定保留上游版本（`--ours` / `Updated upstream` 对应 rebase 语义要特别小心）。
3. `git add` 标记已解决。
4. `git rebase --continue`。

随后 autostash 回放又引入一次冲突，继续同样流程解决后，rebase 完成。

## 经验总结

### 1) 优先判断“连接层问题”

遇到 `Could not read from remote repository`，先区分：

- 地址写错？
- 协议不可达（SSH/HTTPS）？
- 认证失败？

### 2) `--rebase --autostash` 适合日常同步

尤其是本地有改动、又想保持历史整洁时。

### 3) 文档中不要保存明文 token

如果曾把 token 写入仓库（哪怕私有仓库），建议尽快轮换并移除。

## 补充：后续的 SSH 443 固化方案

在完成一次 HTTPS 修复后，为了避免每次 push 走口令认证，本次又补做了 SSH 443 方案。

### 1) 先确认问题不是本地端口被占用

本次连通性测试显示：

- `github.com:22` TCP 可达，但 SSH 在 `kex_exchange_identification` 阶段被远端关闭。
- `ssh.github.com:443` 可完成握手并进入认证阶段。

这更像是网络路径或代理链路对 22 端口 SSH 会话不稳定，而不是本机端口冲突。

### 2) 写入 `~/.ssh/config` 强制走 443

```sshconfig
Host github.com
  HostName ssh.github.com
  Port 443
  User git
```

这样即使继续使用 `git@github.com:<owner>/<repo>.git` 这种常规 SSH 地址，也会自动通过 `ssh.github.com:443` 连接。

### 3) 生成并注册 SSH 密钥

```bash
ssh-keygen -t ed25519 -C "<your-email>"
```

把 `~/.ssh/id_ed25519.pub` 内容添加到 GitHub -> `Settings` -> `SSH and GPG keys`。

### 4) 认证验证

```bash
ssh -T git@github.com
```

看到 `successfully authenticated` 后，`git push` 即可恢复为 SSH 密钥认证流程。

### 5) 本次会话状态记录

- 已创建 `~/.ssh/config` 并写入 443 映射。
- 已生成新的 `ed25519` 密钥对。
- 仓库远程已切回 SSH：`git@github.com:wlchenGG/pages-hugo-source.git`。
- 当时测试结果为 `Permission denied (publickey)`，原因是公钥尚未添加到 GitHub 账号。

## 常用命令速查

```bash
# 查看远程地址
git remote -v

# SSH 连通性测试
ssh -T git@github.com

# 改为 HTTPS
git remote set-url origin https://github.com/<user>/<repo>.git

# 验证远程
git ls-remote --heads origin

# 稳妥拉取
git pull --rebase --autostash

# 生成 SSH 密钥
ssh-keygen -t ed25519 -C "<your-email>"

# SSH 认证测试
ssh -T git@github.com
```

## 参考

- [GitHub Docs - About SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [GitHub Docs - About remote repositories](https://docs.github.com/en/get-started/git-basics/about-remote-repositories)

## 会话后续补充：子模块升级与 Hugo 版本联动修复

这次会话后续还发生了一次典型的“工具链联动问题”：DoIt 升级后，本地 Hugo 版本和旧配置项也要同步升级。

### 1) 子模块从旧版本升级到最新发布

`themes/DoIt` 从 `v0.3.0` 升级到 `v1.0.2`。升级后，主题最低 Hugo 版本要求变为 `>= 0.146.0`。

### 2) 本机 Hugo 升级到 0.161.1

升级 Hugo 后，旧配置语法报错，属于正常的版本迁移问题。

### 3) 配置迁移修复

- `languageCode = "zh-CN"` -> `locale = "zh-CN"`
- `posts/:filename` -> `posts/:contentbasename`

### 4) 验证构建

```bash
hugo --minify
hugo server -D --disableFastRender
```

修复后可正常构建。`development` 环境下 DoIt 关于评论/PWA/CDN/fingerprint 的提示为预期行为。
