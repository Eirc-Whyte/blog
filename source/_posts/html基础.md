---
title: html基础
date: 2021-06-03 19:14:03
tags:
- html
- 前端
categories:
- 编程语言
---

基础知识篇：

<!--more-->

# html5

### 什么是HTML

不是一门编程语言，而是用来**告知浏览器**如何组织页面的标记语言。由一系列**元素**构成。（纯的html类似于markdown？）

#### 块级元素和内联元素

块级元素将出现在新的一行，通常用于展示页面上结构化的内容，例如段落、列表、导航菜单、页脚等等。不可以嵌套在内联元素中，但可以嵌套在其他块级元素中；如`<div><p><ul><li><h1><form>`;

内联元素内联元素不会导致文本换行，通常出现在块级元素中，并环绕文档内容的一小部分。通常出现在一堆文字之间。如`<a>`,`<em>`,`<strong>`,`<span>`。

#### 头部元素

`<head>`

不会显示在浏览器中，作用是保存页面的元数据，如网页标题`<title>`、官方元数据`<meta>`、引用图标`<link rel="shortcut icon" type="imgage/x-icon">`以及引入**css**：`<link rel="stylesheet" href="index.css">`可以指定`name,content`来自定义元数据内容，如`<meta name="description" content="...">`可以被搜索引擎收录。Facebook的元数据协议甚至可以包含图片。

#### html5新特性

- 新增了一堆语义化标签

  > 什么是语义化标签？
  >
  > “用正确的标签做正确的事”
  >
  > 例如video里应该放视频，audio里放音频，
  >
  > article里放文章内容，header里放页眉，footer里放页脚
  >
  > nav放导航链接

  有利于SEO，有利于爬虫抓取信息，具备可读性

> 什么是SEO？
>
> Search Engine Optimization搜索引擎优化

- Canvas&SVG

canvas不可缩放，需要使用js绘制

svg是矢量图形，用结构化语言绘制。

- from API

新增表单参数

- 拖放 API

