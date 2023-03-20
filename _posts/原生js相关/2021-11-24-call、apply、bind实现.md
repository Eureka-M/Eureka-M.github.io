---
layout:     post
title:      Call、Apply、Bind实现
subtitle:  
date:       2021-11-24
author:     
header-img: 
catalog: true
tags:
    - 原生 Javascript 相关
typora-root-url: ..

---

# Call、Apply、Bind实现

## 实现call

1. 任意函数都可以调用call方法，给所有函数添加myCall方法

   ```
   Function.Prototype.myCall = function () {}
   ```

2. 如何获取到是哪一个函数执行了myCall,  sum.call(), 这种调用方式不是正好是隐式绑定吗？使用this就可以获取到是那个函数在调用myCall

   ```
   Function.Prototype.myCall = function () {
   	console.log(this) //
   	var fn = this
   	fn() // 独立函数调用 this为window
   }
   function sum(num1, num2) {
   	 console.log('sum', this) // sum, Window
   }
   sum.myCall()
   ```

3. 改变this指向

   ```
   Function.Prototype.myCall = function (thisArg) {
   	// 获取需要被执行的函数
   	var fn = this
   	thisArg.fn = fn
   	thisArg.fn() // 隐式绑定，改变this指向
   	delete thisArg.fn // 模拟实现的this会多出一个fn变量
   }
   function sum(num1, num2) {
   	 console.log('sum', this) // sum, Window
   }
   sum.myCall({name: 'hi'}) // {name: 'hi', fn: {}}
   ```

4. 使用thisArg的时候可以给对象添加属性，但是如果传入的是简单类型，会报错，而call函数会将'123'转为 String 123 （字符串对象）

   ```
   Function.Prototype.myCall = function (thisArg) {
   	// 获取需要被执行的函数
   	var fn = this
   	// 使用 Object(thisArg) 转为对象 且需要判断thisArg是否为null / undefined，是的话 thisArg = window
   	thisArg = thisArg ? Object(thisArg) : window
   	thisArg.fn = fn
   	thisArg.fn() // 隐式绑定，改变this指向
   	delete thisArg.fn // 模拟实现的this会多出一个fn变量
   }
   function sum(num1, num2) {
   	 console.log('sum', this) // sum, Window
   }
   sum.myCall(123) // Number { 123 , fn: {}}
   ```

5. 接受参数，arguments, 或者 rest(剩余参数)

   function sum(...nums) {

   ​	console.log(nums)

   }

   ```
   Function.Prototype.myCall = function (thisArg, ...args) {
   	// 获取需要被执行的函数
   	var fn = this
   	// 使用 Object(thisArg) 转为对象 且需要判断thisArg是否为null / undefined，是的话 thisArg = window
   	thisArg = thisArg ? Object(thisArg) : window
   	thisArg.fn = fn
   	// ...args 展开运算符（spread）
   	thisArg.fn(...args) // 隐式绑定，改变this指向
   	delete thisArg.fn // 模拟实现的this会多出一个fn变量
   }
   function sum(num1, num2) {
   	 console.log('sum', this) // sum, Window
   }
   sum.myCall(123, 1 , 2, 3) // Number { 123 , fn: {}}
   ```

6. Sum.call() 接受返回值

   ```
   Function.Prototype.myCall = function (thisArg, ...args) {
   	// 获取需要被执行的函数
   	var fn = this
   	// 使用 Object(thisArg) 转为对象 且需要判断thisArg是否为null / undefined，是的话 thisArg = window
   	thisArg = (thisArg !== null || thisArg !== undefined) ? Object(thisArg) : window
   	thisArg.fn = fn
   	// ...args 展开运算符（spread）
   	var result = thisArg.fn(...args) // 隐式绑定，改变this指向
   	delete thisArg.fn // 模拟实现的this会多出一个fn变量
   	
   	return thisArg
   }
   function sum(num1, num2) {
   	 console.log('sum', this) // sum, Window
   }
   sum.myCall(123, 1 , 2, 3) // Number { 123 , fn: {}}
   ```

## apply

```
Function.prototype.myApply = function(thisArg, argArray) {
  // 1.获取到要执行的函数
  var fn = this

  // 2.处理绑定的thisArg
  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg): window

  // 3.执行函数
  thisArg.fn = fn
  argArray = argArray || []
  var result = thisArg.fn(...argArray)

  delete thisArg.fn

  // 4.返回结果
  return result
}

function sum(num1, num2) {
  console.log("sum被调用", this, num1, num2)
  return num1 + num2
}

bar.myApply(0)
```



## bind实现

```
Function.prototype.myBind = function(thisArg, ...argArray) {
  // 1.获取到真实需要调用的函数
  var fn = this

  // 2.绑定this
  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg): window

  function proxyFn(...args) {
    // 3.将函数放到thisArg中进行调用
    thisArg.fn = fn
    // 特殊: 对两个传入的参数进行合并
    var finalArgs = [...argArray, ...args]
    var result = thisArg.fn(...finalArgs)
    delete thisArg.fn

    // 4.返回结果
    return result
  }

  return proxyFn
}

function sum(num1, num2, num3, num4) {
  console.log(num1, num2, num3, num4)
}

// var newSum = sum.bind("aaa", 10, 20, 30, 40)
// newSum()

// var newSum = sum.bind("aaa")
// newSum(10, 20, 30, 40)

// var newSum = sum.bind("aaa", 10)
// newSum(20, 30, 40)

var newSum = sum.myBind("abc", 10, 20)
var result = newSum(30, 40)


```

