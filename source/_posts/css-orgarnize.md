---
title: 前端工程化思考之CSS的管理组织方式 
tags: [css]
categories: [CSS]
date: 2017-09-09 15:35:33
---
## 前言：
对于前端而言，css作为基础之一，大概觉得熟悉的样式规则语法就OK了，就觉得这很简单嘛，但是用久了经常会有一些思考，就是怎么写好css，写了之后需要修改怎么去维护管理，因为一般修改需求后需要重写，很麻烦，现在也有一些css的预处理器，像sass，scss，less，stylus等等的，确实提高不少写css的效率以及便利性。<!--more-->但是这只是一方面的问题，并没有从根本上涉及到css的管理与维护，那么怎么更好的维护或者说管理CSS？到网上一查其实已经有很多优秀的设计方案，有的我们在平时可能多多少少用到了，但是并没有特别去提取抽象并很好的表达出来。

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
2 Layout rules：定义一些全局元素的的样式，比如header、footer、sidebar等等这些基本上只出现一次的元素，而且主要是整体布局类的元素，像这种一般直接用ID选择器会比较清晰
3 Module rules：主要适用可以在一个页面上重用的块，使用时一般为类选择器，避免使用ID选择器以类选择器，尽量解耦，使其独立于上下文。
4 State rules：这里主要是定义一些需要改变状态或场合的情况，比如一个菜单的展开合并，消息的失败成功等等，注意只有这里是可以使用关键字"!important"的。
5 Theme rules：用于改变页面的主题风格，不是很常用，只有需要经常改变主题风格或者供用户选择主题风格时就可以用到。

### AtomicCSS
![AtomicCSS](/images/upload/AtomicCSS.webp)
AtomicCSS是为每个可重用的属性创建一个独立的最小化的类，这种方式是很方便修改样式的并且不易破坏其他样式，更改为模块也比较容易，这种方式以前还是用的比较多的，在一些css框架也比较常见，但是他也有一些比较大的缺点的：
* Class都是些属性名，没有什么描述的语义性，很容易随着开发变得越来越复杂化。
* 显示样式直接体现在了HTML中了
这种方式用在小型项目中就会很冗余，毕竟重用性不高，大型项目倒是还比较适合，有利于降低代码量，还因为他的直接性可以用在一些框架项目的样式矫正。

### BEM
![BEM](/images/upload/BEM.png)
BEM(Block,Element,Modifier)是一种基于组件的开发方式，其核心思想就是将页面分离成独立的块，每一块独立于上下文不会相互影响，方便项目中组件间复用以及快速扩展而原有的可以不受影响。个人还是比较喜欢这种组织方式，可读性以及复用性不要太爽。像现在流行的一些前端框架react，vue都是基于组件的开发方式。下面介绍下一些基本概念：
* Block：功能上独立的他页面组件，可以重复使用，一般用类名表示，注意类名应该具有一定的描述语义性，避免使用外观等不是功能上的描述。
* Element： 一个不能单独使用的复合块，比如表单元素的form和其input
* Modiffier: 定义块或元素的外观状态或行为的实体；
* Mix：单个DOM节点上使用不同BEM实体
* File structure：将BEM的分离思想应用于文件结构

### CSS in JS

未完待续。。。
## 参考文献
* [BEM](https://en.bem.info/methodology/quick-start/)
* [Methods to Organize CSS](https://css-tricks.com/methods-organize-css/)
* [CSS Evolution](https://m.alphasights.com/css-evolution-from-css-sass-bem-css-modules-to- styled-components-d4c1da3a659b)
* [SMACSS](https://smacss.com/)