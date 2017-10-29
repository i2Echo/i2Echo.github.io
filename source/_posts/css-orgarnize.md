---
title: 前端工程化思考之CSS的管理组织方式 
tags: [css]
categories: [CSS]
date: 2017-09-09 15:35:33
---
## 前言：
对于前端而言，css作为基础之一，大概觉得熟悉的样式规则语法就OK了，就觉得这很简单嘛，但是用久了经常会有一些思考，就是怎么写好css，写了之后需要修改怎么去维护管理，因为一般修改需求后需要重写，很麻烦，现在也有一些css的预处理器，像sass，scss，less，stylus等等的，确实提高不少写css的效率以及便利性。但是这只是一方面的问题，并没有从根本上涉及到css的管理与维护，那么怎么更好的维护或者说管理CSS？到网上一查其实已经有很多优秀的设计方案，有的我们在平时可能多多少少用到了，但是并没有特别去提取抽象并很好的表达出来。
<!--more-->
### OOCSS
OOCSS(object-oriented CSS)即面向对象css，该方案核心思想主要包含两点：
* Separation of structure and design
* Separation of container and content
![oocss](/images/upload/oocss.png)
使用这种结构，我们可以在不同的地方使用一些通用类；当然这种方式也是各有优劣：
* 优：总体上会减少代码量（DRY原则）
* 劣：增加代码复杂程度，因为当你想修改特定元素的样式时，你可能不仅需要修改css样式（因为大部分类是公用的，不能随意修改），而且修改之后还需要添加到该元素标签，修改起来还是有点麻烦的，特别是项目代码很多的时候。

当然这种方式还没有形成正式的一个标准，完全可以按照自己的需求自定义一套规则，所以这也给不同开发人员更替维护时候增加一定难度。

### SMACSS
![smacss](/images/upload/smacss.png)
SMACSS是指可扩展化模块化css，主要目的是为了减少代码量以及代码结构简化；它的核心就是通过分类CSS规则，通过分类能过更好的组织样式，主要分为以下5个类别：
1 Base rules：定义所有基础元素的样式，像标签选择器，伪类选择器，自选择器等等这些元素，注意这里不包括ID选择器以及类选择器，最常见的就是我们css reset的用法。
2 Layout rules：
![smacss](/images/upload/s.webp)
## 参考文献
* [BEM](http://getbem.com/introduction/)
* [Methods to Organize CSS](https://css-tricks.com/methods-organize-css/)
* [CSS Evolution](https://m.alphasights.com/css-evolution-from-css-sass-bem-css-modules-to- styled-components-d4c1da3a659b)
* [SMACSS](https://smacss.com/)