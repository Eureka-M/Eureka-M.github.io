---
layout:     post
title:      XMLHttpRequest 运行机制
subtitle:  
date:       2023-02-26
author:     
header-img: 
catalog: true
tags:
  - 浏览器工作原理
typora-root-url: ..


---



# XMLHttpRequest 运行机制

XMLHttpRequest 出现之前，如果服务器数据有更新，需要重新刷新整个页面。XMLHttpRequest 提供了从 Web 服务器获取数据的能力。

每个任务在执行过程中都有自己的调用站，同步回调就是在当前住函数的上下文中执行回调函数。异步回调是指回调函数在主函数外执行的，一般有两种方式：

- 把异步函数做成一个任务，添加到信息队列尾部
- 把异步函数添加到微任务队列中，这样就可以在当前任务的末尾处执行微任务。



### XMLHttpRequest 的运行机制

<img src="/../img/postImage/image-20230302000029697.png" alt="image-20230302000029697" style="zoom: 50%;" />

下面结合一下 XMLHttpRequest 用法，来看看它的运行机制：

```

 function GetWebData(URL){
    /**
     * 1:新建XMLHttpRequest请求对象
     */
    let xhr = new XMLHttpRequest()

    /**
     * 2:注册相关事件回调处理函数 
     */
    xhr.onreadystatechange = function () {
        switch(xhr.readyState){
          case 0: //请求未初始化
            console.log("请求未初始化")
            break;
          case 1://OPENED
            console.log("OPENED")
            break;
          case 2://HEADERS_RECEIVED
            console.log("HEADERS_RECEIVED")
            break;
          case 3://LOADING  
            console.log("LOADING")
            break;
          case 4://DONE
            if(this.status == 200||this.status == 304){
                console.log(this.responseText);
                }
            console.log("DONE")
            break;
        }
    }

    xhr.ontimeout = function(e) { console.log('ontimeout') }
    xhr.onerror = function(e) { console.log('onerror') }

    /**
     * 3:打开请求
     */
    xhr.open('Get', URL, true);//创建一个Get请求,采用异步


    /**
     * 4:配置参数
     */
    xhr.timeout = 3000 //设置xhr请求的超时时间
    xhr.responseType = "text" //设置响应返回的数据格式
    xhr.setRequestHeader("X_TEST","time.geekbang")

    /**
     * 5:发送请求
     */
    xhr.send();
}
```

1. **创建 XMLHttpRequest 对象**

2. **为 xhr 对象注册回调函数**

    - ontimeout，用来监控超时请求，如果后台请求超时了，该函数会被调用
    - onerror，用来监控出错信息，如果后台请求出错了，该函数会被调用
    - onreadystatechange，用来监控后台请求过程中的状态，比如可以监控到 HTTP 头加载完成的消息、HTTP 响应体消息以及数据加载完成的消息等

3. **配置基础的请求信息**

    - 首先要通过 open 接口配置一些基础的请求信息，包括请求的地址、请求方法（是 get 还是 post）和请求方式（同步还是异步请求）
    - xhr 内部属性类配置一些其他可选的请求信息，如通过xhr.timeout = 3000来配置超时时间。
    - 还可以通过xhr.responseType = "text"来配置服务器返回的格式，将服务器返回的数据自动转换为自己想要的格式。
    - 添加自己专用的请求头属性，可以通过 xhr.setRequestHeader 来添加

4. **发起请求**

    调用xhr.send来发起网络请求。

    渲染进程会将请求发送给网络进程，然后网络进程负责资源的下载，等网络进程接收到数据之后，就会利用 IPC 来通知渲染进程；渲染进程接收到消息之后，会将 xhr 的回调函数封装成任务并添加到消息队列中，等主线程循环系统执行到该任务的时候，就会根据相关的状态来调用对应的回调函数。



### XMLHttpRequest 坑：

- 跨域
- HTTPS 混合内容的问题，HTTPS 混合内容是 HTTPS 页面中包含了不符合 HTTPS 安全要求的内容，比如包含了 HTTP 资源，通过 HTTP 加载的图像、视频、样式表、脚本等，都属于混合内容。

![image-20230302232157194](/../img/postImage/image-20230302232157194.png)