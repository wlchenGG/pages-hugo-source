---
title: "Windows 10 上基于 Jekyll 搭建静态博客（记录）"
date: 2025-08-02T09:37:42+08:00
draft: true
---

## 前言

最近在 Windows 10 上搭建了一个基于 Jekyll 的静态博客，记录一下搭建过程。

## 安装 Ruby

Jekyll 是用 Ruby 写的，所以需要先安装 Ruby。

1. 下载 Ruby 安装包：https://rubyinstaller.org/downloads/
   
2. 安装 Ruby，记得勾选“Add Ruby executables to your PATH”选项。
3. 打开命令行，输入`ruby -v`，如果显示 Ruby 版本号，则表示安装成功。

## 安装 Jekyll

1. 打开命令行，输入`gem install jekyll`，等待安装完成。
2. 安装完成后，输入`jekyll -v`，如果显示 Jekyll 版本号，则表示安装成功。

## 创建 Jekyll 项目

1. 在要存放 Jekyll 站点文件的文件夹内，`Shift+右键` 打开命令行，然后输入`jekyll new myblog`回车执行，创建一个名为 `myblog` 的 Jekyll 项目。（注意：myblog 是项目名称，可以自定义）
2. 进入项目文件夹，输入`cd myblog`回车执行，进入项目文件夹。
3. 启动 Jekyll 服务器，输入`jekyll serve`回车执行，启动 Jekyll 服务器。
4. 打开浏览器，输入`http://localhost:4000`，即可在本地预览博客。

## 配置 Jekyll 项目

1. 打开 `_config.yml` 文件，可以修改博客的标题、描述、作者等信息。
2. 在 `_posts` 文件夹内，可以创建新的博客文章，文件名格式为 `YYYY-MM-DD-文章标题.md`，例如 `2021-01-01-hello-world.md`。
3. 在文章文件内，可以编写 Markdown 格式的文章内容。
4. 在 `_layouts` 文件夹内，可以修改博客的模板文件，例如修改首页模板 `index.html`，可以自定义博客的布局和样式。
5. 在 `_includes` 文件夹内，可以添加公共的 HTML 代码，例如添加导航栏、页脚等。
6. 在 `_sass` 文件夹内，可以修改博客的样式文件，例如修改 CSS 文件，可以自定义博客的样式。
7. 在 `_data` 文件夹内，可以添加数据文件，例如添加分类、标签等。
8. 在 `_site` 文件夹内，是 Jekyll 生成的静态网站文件，可以将其部署到服务器上。

## 部署 Jekyll 项目

1. 在 GitHub 上创建一个仓库，用于存放 Jekyll 项目文件。
2. 将 Jekyll 项目文件上传到 GitHub 仓库中。
3. 在 GitHub 仓库中，点击“Settings”按钮，找到“GitHub Pages”选项，选择“master branch /docs folder”选项，然后点击“Save”按钮。
4. 等待 GitHub Pages 生效，即可在浏览器中访问博客。

## 总结

以上就是在 Windows 10 上基于 Jekyll 搭建静态博客的记录，谨以此备忘。