---
title: "Mouse Without Borders 配置 tips"
date: 2022-08-11T17:45:44+08:00
draft: true
---

#### 开机自启动

以管理员方式打开命令行窗口：左下角开始菜单搜索`命令提示符`，点击`以管理员身份运行`。

<div align="center" ><img src="https://img1.wlcheng.cc/images_for_blogs/20220811180951.png" alt="20220811180951" width="600" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/></div>

输入以下命令并按`回车（Enter）`执行：

```
sc config MouseWithoutBordersSvc start=auto //打开自启
sc config MouseWithoutBordersSvc start=demand //禁用自启
```

输出如下提示`成功`字样，则表示设置成功：

<div align="center" ><img src="https://img1.wlcheng.cc/images_for_blogs/20220811181721.png" alt="20220811181721" width="550" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/></div>

