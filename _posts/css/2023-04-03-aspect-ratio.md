---
layout:     post
title:      CSS属性之 aspect-ratio
subtitle:  
date:       2023-04-03
author:     
header-img: 
catalog: true
tags:
    - CSS
typora-root-url: ..
---

背景：刷到一道 CSS 题目：如何实现自适应正方形？通常都会有几种实现方案，比如说百分比，rem，v w这些单位，都是很容易实现的，最后提及到一个属性：`aspect-ratio`，挺有意思的，记录一下。

**`aspect-ratio`** [CSS]() 属性为 box 容器规定了一个**期待的纵横比**，这个纵横比可以用来计算自动尺寸以及为其他布局函数服务。

```
<div class="box-square"></div>

.box-square {
	aspect-ratio: 1 / 1;
	width: 20%;
	backgroud: pink;
}
```

![image-20230403230403629](/../img/postImage/image-20230403230403629.png)
