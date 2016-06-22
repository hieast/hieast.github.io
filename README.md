# 简介

网站基于[Lanyon](https://github.com/poole/lanyon)制作，Lanyon是一个[Jekyll](http://jekyllrb.com)的主题。
本文件包含了网站的版本变动。

## 0.00版

* 网站包括模板和内容，Jekyll读取每一个文件，并根据头部信息生成对应网页。
* 使用[Liquid语法](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers), [参考](http://blog.csdn.net/dont27/article/details/38097581)。
* CSS文件内置了不同颜色的风格，添加body到对应类即可。
* 在根目录下创建'layout: page'（用于区分主页）的文章即可从创建导航栏链接。
* <body class="layout-reverse">用于将导航栏右置
* <body class="sidebar-overlay">用于使导航栏覆盖内容
* id为"sidebar-checkbox"的checkbox用于控制导航栏的出现和隐藏

## 0.1版

* 使用fonts.useso.com（不支持https）替换fonts.googleapis.com，由于众所周知的原因。
* 修复了地址错误，由于生成网址时‘/’的不确定。
* 优化了slide效果，导航栏滑出后文章仍在空白部分的中间。

## 0.2版

* 添加了 disqus 评论功能，最大限度中文化，支持匿名评论。

## 0.3版
* 添加了serch 标题功能。感谢[codeboy](https://github.com/androiddevelop)的插件。
* 添加了 多说 评论功能， disqus仅用于留言板。优化了评论的展示。
* 修复了搜索的一个地址bug。

## 下一步

* 修复对php的代码高亮
* 添加tag
* 优化中文字体
* 添加网站统计如Google Analytics
* 待文章达到一定数量后优化搜索功能

## License

Open sourced under the [MIT license](LICENSE.md).

