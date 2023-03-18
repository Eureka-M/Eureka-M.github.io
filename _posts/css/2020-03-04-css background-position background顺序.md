---
layout:     post
title:      background-position 顺序
subtitle:  
date:       2020-03-04
author:     
header-img: 
catalog: true
tags:
  - CSS
typora-root-url: ..
---

# background-position 顺序

问题：先写background-position 再写 background ，发现得到的图片是空

查看background定义

发现：background是一个缩写，如果先写background-position，后写background, 会覆盖前面写的background-position