---
layout:     post
title:      arguments转成array
subtitle:  
date:       2020-10-23
author:     
header-img: 
catalog: true
tags:
typora-root-url: ..


---

# arguments转成array

## slice函数实现

```
Array.prototype.slice.call(arguments) 
```

```
Array.prototype.slice = function(start, end) {
	var arr = this
	start = start || 0
  end = end || arr.length
  
	var newArr = []
	for (var i = start; i < end; i++) {
		newArr.push(arr[i])
	}
	return newArr
}
```

Array.prototype.slice.call(arguments) this值绑定的是arguments

同样，也可以这样做

```
[].slice.call(arguments)
```

[].slice.call(arguments) [].slice 隐式绑定 .call 显式绑定arguments

## es6

```
Array.from(arguments)
```

```
[...arguments]
```

