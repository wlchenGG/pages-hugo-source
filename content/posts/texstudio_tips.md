---
title: "TeXstudio 常见问题"
date: 2023-03-24T00:34:19+01:00
draft: true
categories:
  - 软件
tags:
  - TeXstudio
---

记录使用 TeXstudio 的过程中，我遇见的问题或需求，以及解决方法。只管其然，不管所以然。

<!--more-->

## 编辑器

### 问题：中文括号与英文字符重叠

- **问题如下：**

<div align="center" ><img src="https://fastly.jsdelivr.net/gh/wlchengg/PicBed@main/images_for_blogs/0e3cb956e2ec890a71595cd0847f220.png" alt="0e3cb956e2ec890a71595cd0847f220" width="75%" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/><br><div style="color:orange; border-bottom: 1px solid #d9d9d9; display: inline-block; color: #777; font-size: 90%; padding: 1px;">中英混排重叠问题</div></div>

- **解决方法：**

  TeXstudio 上方菜单栏的 `选项` `设置 TeXstudio`，弹出的窗口如下图，按照以下流程进行设置：先确定勾选左下角 `显示高级选项`-> 左侧选项栏的 `高级编辑器`，右侧设置界面拉到最下面，取消勾选 `自动选择最佳显示选项`，勾选 `禁用行缓存`，渲染模式选择 `单个字母`。

<div align="center" ><img src="https://fastly.jsdelivr.net/gh/wlchengg/PicBed@main/images_for_blogs/20230324005140.png" alt="20230324005140" width="75%" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/><br><div style="color:orange; border-bottom: 1px solid #d9d9d9; display: inline-block; color: #777; font-size: 90%; padding: 1px;">设置渲染模式</div></div>

- **英文界面：**

<div align="center" ><img src="https://fastly.jsdelivr.net/gh/wlchengg/PicBed@main/images_for_blogs/bd2f031982550ed61021fffe7942e93.png" alt="bd2f031982550ed61021fffe7942e93" width="75%" style="box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);border-radius:10px;"/><br><div style="color:orange; border-bottom: 1px solid #d9d9d9; display: inline-block; color: #777; font-size: 90%; padding: 1px;">Render Mode</div></div>


- **参考：**
  
1. 隐私保护小白，TeXstudio 在扩展屏上显示段落出现重叠或不清晰的解决办法，https://blog.csdn.net/weixin_43476788/article/details/117965803  
2. 本帅哥屏蔽了凡人，TeXstudio 中英文混合编辑器显示异常，https://blog.csdn.net/qq_23947237/article/details/121807618
