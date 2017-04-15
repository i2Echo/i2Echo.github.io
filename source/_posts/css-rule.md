---
title: Css书写规范
tags: [css, rule, css规范]
date: 2016-03-28 20:15:24
categories: Css
---
最近觉得自己写的css有点乱，有点受不了自己了；于是参考了网上的一些css书写规范，自己总结了下css书写规范，希望对自己以后以及前端的童鞋会有帮助。
<!--more-->
### 命名规则
#### 文件命名
* 使用下划线
* 例如：main_sprites.css
#### 常见文件命名
* 主要的 main.css
* 模块 module.css
* 基本共用 base.css
* 布局、版面 layout.css
* 主题 themes.css
#### 选择器命名
* 连接词使用短横线"-"命名
* 尽量使用英文，不要拼音
* 在无歧义的前提下尽量使用缩写
* 全部小写
#### 常见命名
* 头：header
* 内容：content/container
* 页脚：footer
* 导航：nav
* 侧栏：sidebar
* 栏目：column
* 页面外围控制整体佈局宽度：wrapper
* 左右中：left right center
* 登录条：loginbar
* 标志：logo
* 广告：banner
* 页面主体：main
* 热点：hot
* 新闻：news
* 下载：download
* 子导航：subnav
* 菜单：menu
* 子菜单：submenu
* 搜索：search
* 友情链接：friendlink
* 版权：copyright
* 滚动：scroll
* 内容：content
* 标签：tags
* 文章列表：list
* 提示信息：msg
* 小技巧：tips
* 标题：title
* 加入：joinus
* 指南：guide
* 服务：service
* 注册：regsiter
* 状态：status
* 投票：vote
* 合作伙伴：partner
### 属性声明顺序
1. 位置(position, top, right, z-index, display, float等)
2. 大小(width, height, padding, margin)
3. 文本样式(font-family, font-size, line-height, letter-spacing, color, text-align等)
4. 背景(background, border等)
5. 其他(animation, transition等)

### 其他
* love hate 原则（link  visited  hover  active）
* 缩进2个空格大小（兼容linux操作系统2空格大小tab键）
* 注释采用"/* 注释 */", 不使用"//"
* 尽量少用id，使用class，因为class可复用
* 省略小数点前的0
* 尽量使用合并属性
* 颜色值使用16进制小写，能缩写就缩写