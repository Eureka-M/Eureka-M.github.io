---
layout:     post
title:      关于 textarea focus 的两个问题
subtitle:  
date:       2023-04-20
author:     
header-img: 
catalog: true
tags:
  - 工作
typora-root-url: ..

---

## 问题一

实现表情框和输入框的切换。点击表情按钮的时候希望能够隐藏表情框，切换到输入法。**andriod 上表现正常，但是在 ios 上，textarea.focus() 失效**。

### 疑问

**为什么 textarea.focus() 失效了？**

写一个例子在 ios 上验证一下

```
const textarea = document.getElementById("myTextarea");
function focus() {
	textarea.focus();
}
const button = document.getElementById("myButton");
button.addEventListener("click", function() {
   	focus()
});
```

iOS 上运行，可以聚焦。不解，在不久之后询问 chatGPT 得到答案。

<img src="/../img/postImage/image-20230426232107692.png" alt="image-20230426232107692" style="zoom:50%;" />

得到一个关键信息，**如果该调用是在非用户事件（如`load`、`setInterval`、`setTimeout`等）中触发，那么可能无法正常弹出键盘**。

将上面的例子改一下，让 textarea.focus() 在 setTimeout 中 执行。

```
const textarea = document.getElementById("myTextarea");
function focus() {
	setTimeout(() => {
		textarea.focus();
	});
}
const button = document.getElementById("myButton");
button.addEventListener("click", function() {
   	focus()
});
```

果真，textarea 无法聚焦！！！

### 转折

![image-20230426232925934](/../img/postImage/image-20230426232925934.png)

原因在 hideLayerAnimation 中触发的 focus。而 Animation 就意味着很大几率有 setTimeout 的参与。阅读 PopUp 封装的代码，确实如此。

### 解决

直接在点击的地方执行focus。

## 问题二

解决 setSelectionRange 会 focus，blur 之后 导致键盘弹起隐藏的闪烁。解决方式十分巧妙。在 setSelectionRange 前使用 disabled 使得 focus 失效。

```
textarea.disabled = true;
textarea.setSelectionRange(0, 0);
textarea.disabled = false;
textarea.blur();
```

