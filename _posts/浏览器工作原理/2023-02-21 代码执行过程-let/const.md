---
layout:     post
title:      代码执行过程之let、const
subtitle:  
date:       2023-02-21
author:     
header-img: 
catalog: true
tags:
  - 浏览器工作原理
typora-root-url: ..

---

创建执行上下文

- 当 JavaScript 执行全局代码的时候，会编译全局代码并创建全局执行上下文，而且在

    整个页面的生存周期内，全局执行上下文只有一份。

- 当调用一个函数的时候，函数体内的代码会被编译，并创建函数执行上下文，一般情况

    下，函数执行结束之后，创建的函数执行上下文会被销毁。

- 当使用 eval 函数的时候，eval 的代码也会被编译，并创建执行上下文。

### let 和 const 是如何解决变量提升带来的问题的？

let 和 const 会生成块级作用域，而块级作用域会在词法环境中开辟一块空间来存放块级作用域声明的变量。

接下来看一块代码：

<img src="/../img/postImage/image-20230221194740362.png" alt="image-20230221194740362" style="zoom:50%;" />

第一步，编译并执行上下文

<img src="/../img/postImage/image-20230221194859346.png" alt="image-20230221194859346" style="zoom:33%;" />

第二步，继续执行代码，执行到块级作用域里面时，作用域块中通过 let 声明的变量，会被存放在词法环境的一个单独的区域中。

<img src="/../img/postImage/image-20230221195028172.png" alt="image-20230221195028172" style="zoom:33%;" />

第三步，查找变量过程

<img src="/../img/postImage/image-20230221195156074.png" alt="image-20230221195156074" style="zoom:33%;" />

第四步，块级作用域结束之后，内部定义的变量就会从词法环境的栈顶弹出

<img src="/../img/postImage/image-20230221200152283.png" alt="image-20230221200152283" style="zoom:33%;" />

