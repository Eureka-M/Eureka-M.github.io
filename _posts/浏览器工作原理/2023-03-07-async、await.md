---
layout:     post
title:      async、await
subtitle:  
date:       2023-03-07
author:     
header-img: 
catalog: true
tags:
  - 浏览器工作原理
typora-root-url: ..
---



ES7 引入 async/await，这是 JavaScript 异步编程的一个重大改进，提供了在不阻塞主线程的情况下使用同步代码实现异步访问资源的能力，并且使得代码逻辑更加清晰。

async/await 使用了 Generator 和 Promise 两种技术，所以先介绍一下它们：

### Generator

生成器函数是一个带星号函数，可以暂停执行和恢复执行。

```
function* genDemo() {
 console.log(" 开始执行第一段 ")
 yield 'generator 2'
 console.log(" 开始执行第二段 ")
 yield 'generator 2'
 console.log(" 开始执行第三段 ")
 yield 'generator 2'
 console.log(" 执行结束 ")
 return 'generator 2'
}
console.log('main 0')
let gen = genDemo()
console.log(gen.next().value)
console.log('main 1')
console.log(gen.next().value)
console.log('main 2')
console.log(gen.next().value)
console.log('main 3')
console.log(gen.next().value)
console.log('main 4')
```

执行上面代码，可以发现，genDemo 并不是一次执行完成的，全局代码和 genDemo 函数**交替执行**。这就是生成器函数的特性：**可以暂停执行，也可以恢复执行**。生成器函数的使用方式：

1. 生成器内部函数执行一段代码，遇到 **yield** 关键字，javaScript 引擎会返回关键字后面的内容给外部，并暂停该函数的执行。
2. 外部函数可以通过 **next** 方法恢复函数的执行。

那么函数是如何恢复和暂停的呢？

首先，了解一下协程的概念。**协程是一种比线程更加轻量级的存在，一个线程上可以存在多个协程，但是在线程上同时只能执行一个协程。**比如当前执行的是 A 协程，要启动 B 协程，那么 A 协程就需要将主线程的控制权交给 B 协程，这就体现在 A 协程暂停执行，B 协程恢复执行；同样，也可以从 B 协程中启动 A 协程。

**如果从 A 协程启动 B 协程，我们就把 A 协程称为B 协程的父协程。**

> 一个进程可以拥有多个线程一样，一个线程也可以拥有多个协程。最重要的是，协程不是被操作系统内核所管理，而完全是由程序所控制（也就是在用户态执行）。这样带来的好处就是性能得到了很大的提升，不会像线程切换那样消耗资源。

下面是上面代码模拟出来的一个“协程执行流程图”：

![image-20230307233625309](/../img/postImage/image-20230307233625309.png)

可以看出：

1. 通过调用生成器函数 genDemo 来创建一个协程 gen，创建之后，gen 协程并没有立即执行。
2. 要让 gen 协程执行，需要通过调用 gen.next。
3. 当协程正在执行的时候，可以通过 yield 关键字来暂停 gen 协程的执行，并返回主要信息给父协程。
4. 如果协程在执行期间，遇到了 return 关键字，那么 JavaScript 引擎会结束当前协程，并将 return 后面的内容返回给父协程。

那么，父协程和 gen 协程是如何切换调用栈的？

<img src="/../img/postImage/image-20230307234017364.png" alt="image-20230307234017364" style="zoom:67%;" />

当在 gen 协程中调用了 yield 方法时，JavaScript 引擎会保存 gen 协程当前的调用栈信息，并恢复父协程的调用栈信息。同样，当在父协程中执行 gen.next 时，JavaScript 引擎会保存父协程的调用栈信息，并恢复 gen 协程的调用栈信息。

通常，我们把执行生成器的代码封装成一个函数，并把这个执行生成器代码的函数称为执行器（可参考著名的 co 框架），如下面这种方式：

```
function* foo() {
     let response1 = yield fetch('https://www.geekbang.org')
     console.log('response1')
     console.log(response1)
     let response2 = yield fetch('https://www.geekbang.org/test')
     console.log('response2')
     console.log(response2)
}
co(foo());
```

通过使用生成器配合执行器，就能实现使用同步的方式写出异步代码了，这样也大大加强了代码的可读性。

### async / await

async/await 技术背后的秘密就是 Promise 和生成器应用，往低层说就是微任务和协程应用。

#### async

async 是一个通过**异步执行**并**隐式返回 Promise** 作为结果的函数。

```
async function foo() {
 return 2
}
console.log(foo()) // Promise {<resolved>: 2}
```

#### await

先看一段代码，并分析执行流程：

```
async function foo() {
 console.log(1)
 let a = await 100
 console.log(a)
 console.log(2)
}
console.log(0)
foo()
console.log(3)
```

我们先站在协程的视角来看看这段代码的整体执行流程图：

![image-20230307235224323](/../img/postImage/image-20230307235224323.png)

分析，执行流程如下：

1. 执行console.log(0)这个语句，打印出来 0。
2. 紧接着就是执行 foo 函数，由于 foo 函数是被 async 标记过的，所以当进入该函数的时候，JavaScript 引擎会保存当前的调用栈等信息，然后执行 foo 函数中的console.log(1)语句，并打印出 1。
3. 当执行到await 100时，会默认创建一个 Promise 对象。在这个 promise_ 对象创建的过程中，我们可以看到在 executor 函数中调用了 resolve 函数，JavaScript 引擎会将该任务提交给微任务队列。然后 JavaScript 引擎会暂停当前协程的执行，将主线程的控制权转交给父协程执行，同时会将 promise_ 对象返回给父协程。主线程的控制权已经交给父协程了，这时候父协程要做的一件事是调用 promise_.then 来监控 promise 状态的改变。
4. 接下来继续执行父协程的流程，这里我们执行console.log(3)，并打印出来 3。
5. 随后父协程将执行结束，在结束之前，会进入微任务的检查点，然后执行微任务队列，微任务队列中有resolve(100)的任务等待执行，执行到这里的时候，会触发 promise_.then 中的回调函数。该回调函数被激活以后，会将主线程的控制权交给 foo 函数的协程，并同时将 value 值传给该协程。
6. foo 协程激活之后，会把刚才的 value 值赋给了变量 a，然后 foo 协程继续执行后续语句，打印console.log(a) // 100 , console.log(2),  执行完成之后，将控制权归还给父协程。