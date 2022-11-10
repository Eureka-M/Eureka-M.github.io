---
layout:     post
title:      Observe Api
subtitle:  
date:       2022-04-18
author:     
header-img: 
catalog: true
tags:
    - 原生js相关
typora-root-url: ..
---

# Observe Api

Browser Apis that can observe information about the DOM

我们常常使用 addEventListener 添加事件监听器监听各种用户操作（click, mousemove, input...）
那非用户触发的事件，比如元素的不可见到可见，元素大小的改变，属性和子节点的修改，如何监听？

#### Intersection Observer 交叉观察器

传统方式监听一个元素是否可见：scroll 事件+getBoundingClientRect()
Chrome 51+ 提供新的 API，IntersectionObserver API , 自动观察元素是否 visible，定义 visible 的本质是目标元素和 viewport 产生交叉区

```
let io = new IntersectionObserver(callback, option)
// 指定观察元素，可以多次调用观察多个元素
io.observe(element)
// 停止观察某个元素
io.unobserve(element)
// 停止观察器
io.disconnect()
// callback 的参数为entries数组，对应多个IntersectionEntry 对象
```

##### IntersectionObserver

-   IntersectionObserver.root（只读）
    所监听对象的具体祖先元素(element), 如果未传入值或值为 null，则默认使用顶级文档的视窗。

-   IntersectionObserver.rootMargin（只读）
    计算交叉时添加到根（root）边界盒 bounding box 的矩形偏移量，可以有效的缩小或扩大根的判定范围从而满足计算需要。此属性返回的值可能与调用构造函数时指定的值不同，因此可能需要更改该值，以匹配内部要求。所有的偏移量均可用像素(pixel)(px)或者百分比(percentage)(%)来表达，默认值为"0px 0px 0px 0px"

-   IntersectionObserver.thresholds
    一个包含阈值的列表, 按升序排列, 列表中的每个阈值都是监听对象的交叉区域与边界区域的比率。当监听对象的任何阈值被越过时，都会生成一个通知(Notification)。如果构造器未传入值, 则默认值为 0。

##### IntersectionObserverEntry

-   IntersectionObserverEntry.boundingClientRect (只读)
    返回包含目标元素的边界信息的 DOMRectReadOnly. 边界的计算方式与 Element.getBoundingClientRect() 相同.

-   IntersectionObserverEntry.intersectionRatio (只读)
    返回 intersectionRect 与 boundingClientRect 的比例值.

-   IntersectionObserverEntry.intersectionRect (只读)
    返回一个 DOMRectReadOnly 用来描述根和目标元素的相交区域.

-   IntersectionObserverEntry.isIntersecting (只读)
    返回一个布尔值, 如果目标元素与交叉区域观察者对象(intersection observer) 的根相交，则返回 true .如果返回 true, 则 IntersectionObserverEntry 描述了变换到交叉时的状态; 如果返回 false, 那么可以由此判断,变换是从交叉状态到非交叉状态.

-   IntersectionObserverEntry.rootBounds (只读)
    返回一个 DOMRectReadOnly 用来描述交叉区域观察者(intersection observer)中的根.

-   IntersectionObserverEntry.target (只读)
    与根出现相交区域改变的元素 (Element).

-   IntersectionObserverEntry.time (只读)
    返回一个记录从 IntersectionObserver 的时间原点(time origin)到交叉被触发的时间的时间戳(DOMHighResTimeStamp).

##### 应用

-   懒加载
-   无限加载
-   元素显示的数据采集
-   polyfill:https://github.com/que-etc/intersection-observer-polyfill

监听元素的属性和子节点的变化

-   监听 js 对象的变化，使用 Object.defineProperty 或者 Proxy
-   那么如果要监听对元素的属性修改或者子节点的增删改如何实现呢？

#### MutationObserver

```
    let mo = new MutationObserver(callback)
    // 设置监听元素的哪些变化
    let observerOptions = {
        childList: true, // 观察目标子节点是否有添加或者删除
        attributes: true, // 观察属性变化
        subtree: true // 观察后代节点，默认为false
    }
    // 观察元素
    mo.observe(targetNode, observerOptions)
    // 阻止mo 继续接收通知，mo 回调函数取消所有监听调用
    mo.disconnect()
```

##### 应用

-   防止去水印
-   结合 trace 追踪代码修改的来源

#### ResizeObserver

可以监听元素大小

```
const ro = new ResizeObserver(callback)
// 监听ele 元素的resize
ro.observe(ele)
// 取消监听ele元素的resize
ro.ubobserve(ele)
// 取消ro 对象监听的所有ele的回调
ro.disconnect()
```

##### 应用

-   元素 resize 行为的检测（白板重绘，echarts 图标重绘）
-   感知交互行为的发生
-   感知元素是否显示或者隐藏
-   polyfill: https://github.com/juggle/resize-observer
