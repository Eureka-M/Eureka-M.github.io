---
layout:     post
title:      Cookie & Session
subtitle:  
date:       2023-06-27
author:     
header-img: 
catalog: true
tags:
  - HTTP 相关内容
typora-root-url: ..
---

Cookie 是客户端的，而 Session 是服务端的，Cookie 和 Session 是一种会话管理机制，减少 Http 无状态无记忆带来的问题。

### Cookie

Cookie 实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就向客户端浏览器颁发一个 Cookie。

客户端浏览器会把 Cookie 保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie 一同提交给服务器。服务器检查该 Cookie，以此来辨认用户状态。

![image-20230627205943107](/../img/postImage/image-20230627205943107.png)

### Session

Session 是另一种记录客户状态的机制，保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。

客户端浏览器再次访问时只需要从该 Session 中查找该客户的状态就可以了。

![image-20230629212832251](/../img/postImage/image-20230629212832251.png)

> 如果说 cookie 机制是通过检查客户身上的通行证来验证客户身份，那么session 机制就是通过检查服务器上的客户明细表来验证客户身份。

cookie 的有效期可以是**永久**，session 由于会有越来越多的 用户访问服务器，因此 session 也会越来越多，为了防止内存溢出，服务器就会把**长时间不活跃**的 session 从内存中删除。这就是 session 的超时失效。也是 session 的被动失效。通常在调用退出，注销的时候，也可以调用 httpSession.invalidate() 来主动失效。服务器进程异常终止的时候，session 也会失效。

### 区别

**存放位置不同**：cookie 存放在客户端，而 session 存放在服务端（对服务器的压力有影响）。

**安全性不同**：cookie 对于客户端是可见的，但是 session存在服务器中，对于客户端来说是透明的，不可见的，更安全。

**有效期不同**：cookie 可以是永久的，但是 session 会被定时清除。

