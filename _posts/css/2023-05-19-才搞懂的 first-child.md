---
layout:     post
title:      才搞懂的 first-child
subtitle:  
date:       2023-05-19
author:     
header-img: 
catalog: true
tags:
    - CSS
typora-root-url: ..
---

### first-child

直接上图吧，今天做需求时，发现像选中下面框中的第一个formItem，给它加一个右边距。

![image-20230519160528728](/../img/postImage/image-20230519160528728.png)

直接在 css 中这么写：

```
.good-price-form-item:first-child {
    margin-right: 24px;
}
```

不生效，百度了一波懂了，原来之前的 first-child 都是误打误撞？？

> first-child 匹配父元素第一个元素，且第一个元素的类名要符合才会匹配中！！

而上面代码中，父元素的第一个字元素的类名并不是 . good-price-form-item，所以匹配不了。

### first-of-type

在 search 的时候有小伙棒提到这个 first-of-type。刚好也一并了解了它的匹配规则。

> first-of-type 第一步匹配类名对应的标签，找到标签的第一个元素，第二步再需要匹配类名。

```
<h1>123</h1>
<p>123</p>
<h1 class="test">123</h1>
<h1 class="test">123</h1>

.test:first-of-type {
   background: pink;
}
```

<img src="/../img/postImage/image-20230519161448559.png" alt="image-20230519161448559" style="zoom:50%;" />

无法匹配到第 1 个元素，因为类名不匹配；也无法匹配到第 3 个元素，因为位置不匹配。相当于 first-of-type 会先找到三个 h1 的元素，取第一个元素去比较类名是否匹配。

如果改一下，把第一个元素加上 class="test"，那就可以匹配上了。

<img src="/../img/postImage/image-20230519161859428.png" alt="image-20230519161859428" style="zoom:50%;" />

