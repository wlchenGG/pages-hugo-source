---
title: "RealVNF —— Coord Sim"
date: 2021-09-09T22:07:20+08:00
draft: true
---

近期仿真实验，学习开源仿真工具 coord-sim。

[coord-sim](https://github.com/RealVNF/coord-sim) 能够模拟流级、节点间网络协调，包括服务的扩展和放置以及它们之间流量的调度/平衡。

## 安装

先从官方仓库克隆或下载工具源码。

如果安装了git，则可采用克隆方式：

```bash
git clone https://github.com/RealVNF/coord-sim.git
```

克隆下仓库后，执行命令 `cd coord-sim`进入 coord-sim 根目录。

在根目录下执行 `pip install -r requirements.txt`安装依赖包。最好是在独立的虚拟 Python 环境中安装。

我在安装是出现以下错误：

```bash
WARNING: pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Could not fetch URL https://pypi.org/simple/pip/: There was a problem confirming the ssl certificate: HTTPSConnectionPoopip/ (Caused by SSLError("Can't connect to HTTPS URL because the SSL module is not available.")) - skipping
```

提示 SSL 包不可用，遂通过`pip install openssl`安装，但不管是官方源还是换成清华源、阿里源都未能成功。

```bash
(MP1) C:\WINDOWS\system32>pip install openssl -i https://mirrors.aliyun.com/pypi/simple/
Looking in indexes: https://mirrors.aliyun.com/pypi/simple/
ERROR: Could not find a version that satisfies the requirement openssl (from versions: none)
ERROR: No matching distribution found for openssl
```

谷歌搜索相关问题后，在[stack overflow](https://stackoverflow.com/questions/41328451/ssl-module-in-python-is-not-available-when-installing-package-with-pip3)上找到解决方案。

首先上 SSL官方网站下载 `Win64 OpenSSL v3.0.0` 版本的 OpenSSL，下载完安装后，注意安装时勾选 `将DLL文件加入 system文件夹` 的项。

然后就重新执行`pip install -r requirements.txt`安装依赖包。由于我是在Windows下的Anaconda环境下建立coord-sim环境，因此要在所建环境中执行安装命令。
```bash
# 先激活虚拟Python环境 MP1
activate MP1
# 定位到coord-sim路径下
cd coord-sim
# 安装依赖包
pip install -r requirements.txt
```
如果以上还失败，可以在anaconda自带的命令行工具中执行以上命令。

## 测试

