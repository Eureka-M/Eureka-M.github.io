---
layout:     post
title:      JavaScript 如何影响 DOM 的生成
subtitle:  
date:       2023-03-09
author:     
header-img: 
catalog: true
tags:
  - 浏览器工作原理
typora-root-url: ..
---

# JavaScript 如何影响 DOM 的生成

## DOM 是什么？

从页面的视角来看，DOM 是生成页面的基础数据结构。

从 javaScript 脚本的视角来看，DOM 提供给 JavaScript 脚本操作的接口，通过这套接口，JavaScript 可以对 DOM 结构进行访问，从而改变文档的结构，样式和内容。

从安全视角来看，DOM 是一道安全防护线。

## DOM 树如何生成？

- 网路进程接收到响应头中 content-type 为 'text/html'的请求，通知浏览器进程
- 浏览器进程为该请求创建/选择一个渲染进程
- 渲染进程准备完毕
- 网络进程和渲染进程之间建立一个共享数据的管道
- 网络进程 ‘喂’ 数据给渲染进程
- 渲染进程将数据 ‘喂’ 给 HTML 解析器

> 以上可知：HTML 解析器并不是等待整个文档加载完成之后再解析的，而是网络进程加载了多少数据，HTML 解析器便解析多少数据

### 字节流是如何转换成 DOM 的？

<img src="/../img/postImage/image-20230309203041922.png" alt="image-20230309203041922" style="zoom:50%;" />

如图：

字节流通过分词器转换成 Token，分为 Tag Token 和 文本 Token。同时 Tag Token 还分为 StartTag 和 EndTag。然后将 Token 解析成 DOM 节点，并将 DOM 点添加到DOM树中。

### Token 如何解析成 DOM 节点的？

HTML 解析器维护了一个Token 栈结构，该 Token 栈主要用来计算节点之间的父子关系。分词器生成的 Token 会被按照顺序压到这个栈中。

- 如果压入到栈中的是StartTag Token，HTML 解析器会为该 Token 创建一个 DOM 节点，然后将该节点加入到 DOM 树中，它的父节点就是栈中相邻的那个元素生成的节点。
- 如果分词器解析出来是文本 Token，那么会生成一个文本节点，然后将该节点加入到DOM 树中，文本 Token 是不需要压入到栈中，它的父节点就是当前栈顶 Token 所对应的 DOM 节点。
- 如果分词器解析出来的是EndTag 标签，比如是 EndTag div，HTML 解析器会查看
    Token 栈顶的元素是否是 StarTag div，如果是，就将 StartTag div 从栈中弹出，表示该 div 元素解析完成。

通过分词器产生的新 Token 就这样不停地压栈和出栈，整个解析过程就这样一直持续下去，直到分词器将所有字节流分词完成。

接下来看一段代码：

```
<html>
    <body>
         <div>1</div>
         <div>test</div>
    </body>
</html>
```

HTML 解析器开始工作时，会默认创建了一个根为 document 的空 DOM 结构，同时会将一个 StartTag document 的 Token 压入栈底。

1. 经过分词器处理，解析出来的第一个Token 是 StartTag html，解析出来的 Token 会被压入到栈中，并同时创建一个 html 的DOM 节点，将其加入到 DOM 树中。

    <img src="/../img/postImage/image-20230309205253146.png" alt="image-20230309205253146" style="zoom: 33%;" />

2. 然后按照同样的流程解析出来 StartTag body 和 StartTag div，其 Token 栈和DOM 的状态如下图所示：

    <img src="/../img/postImage/image-20230309205333557.png" alt="image-20230309205333557" style="zoom:33%;" />

3. 接下来解析出来的是第一个 div 的文本 Token，渲染引擎会为该 Token 创建一个文本节点，并将该 Token 添加到 DOM 中，它的父节点就是当前 Token 栈顶元素对应的节点。

    <img src="/../img/postImage/image-20230309205446890.png" alt="image-20230309205446890" style="zoom:33%;" />

4. 再接着，解析出来第一个 EndTag div，这时候 HTML 解析器判断当前栈顶元素是否是 StartTag div, 如果是则从栈顶弹出 StartTag div

    <img src="/../img/postImage/image-20230309205632369.png" alt="image-20230309205632369" style="zoom:33%;" />

5. 最终

    <img src="/../img/postImage/image-20230309205722824.png" alt="image-20230309205722824" style="zoom:33%;" />

### 那 JavaScript 如何影响 DOM 生成？

```
<html>
    <body>
         <div>1</div>
         <script>
         	let div = document.getElementByTagName('div')[0]
         	div.innerText = 'time.geekbang'
         </script>
         <div>test</div>
    </body>
</html>
```

再遇到 script 标签之前的流程还是和之前的一样，之后渲染引擎判断这是一段脚本，HTML 解析器就会暂停 DOM 的解析，因为 js 可能会修改当前生成的 DOM 结构。

HTML 解析器暂停，JavaScript 引擎执行 script 标签中的脚本。执行脚本时 DOM的状态如下：

<img src="/../img/postImage/image-20230309231628132.png" alt="image-20230309231628132" style="zoom:50%;" />

执行完成之后，HTML 解析器恢复解析，继续解析后续内容，直至生成最终的 DOM。

再稍微复杂一点，如果script 是引入 javaScipt 文件呢？

```
<html>
    <body>
         <div>1</div>
         <script type="text/javascipt" src="xxx.js"></script>
         <div>test</div>
    </body>
</html>
```

执行到 script 标签时，暂停整个 DOM 的解析，执行这个 js 文件之前，要先下载这个文件。而 js 的下载过程会阻塞 DOM 解析，通常下载受网络环境，资源大小等因素是比较耗时的。

### 如何解决这个阻塞问题？

- 预解析

    当渲染引擎收到字节流之后，会开启一个预解析线程，用来分析HTML 文件中包含的 javascript, css 等相关文件，预解析线程会提前下载这些文件。

- CDN加速文件加载

- 压缩文件体积

- 异步加载

    如果 js 文件中没有操作 dom 相关代码，可以设置加载脚本的方式异步加载。

    ```
    <script async type="text/javascript" src="foo.js"></script>
    <script defer type="text/javascript" src="foo.js"></script>
    ```

    - async 和 defer 都是异步

    - async 标记脚本一旦加载完成，立即执行

    - defer 标记脚本，在 DOMContentLoaded 事件之前执行

### CSS文件也会阻塞 js 的执行

```
<html>
	<head>
		<style src="xxx.css"></style>
	</head>
    <body>
         <div>1</div>
         <script>
         	let div = document.getElementsByTagName('div')[0]
         	div.innerText = 'abc' // DOM 操作
         	div.style.color = 'red' // CSSOM 操作
         </script>
         <div>test</div>
    </body>
</html>
```

script 中的 div.style.color = 'red' 需要操作 CSSOM，而在执行 script 之前，需要先解析 js 语句上所有的 css 样式，如果有 css 文件，还需要等待 css 文件下载，才能解析生成 CSSOM 对象之后，最后再执行 js 脚本。