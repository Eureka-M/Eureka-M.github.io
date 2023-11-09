---
layout:     post
title:      TypeScript
subtitle:  
date:       2023-11-05
author:     
header-img: 
catalog: true
tags:
    - 原生 Javascript 相关
typora-root-url: ..
---

## 背景

随着近几年前端领域的快速发展，让JavaScript迅速被普及和受广大开发者的喜爱，借助于JavaScript本身的强大，也让使用JavaScript开发的人员越来越多。其实上由于各种历史因素，JavaScript语言本身存在很多的缺点：

- 比如ES5以及之前的使用的var关键字关于作用域的问题
- 如最初JavaScript设计的数组类型并不是连续的内存空间
- 也没有加入类型检测

**类型带来的问题**：

错误出现的越早越好，能在**写代码的时候**发现错误，就不要在**代码编译时**再发现（IDE的优势就是在代码编写过程中帮助我们发现错误），能在**代码编译期间**发现错误，就不要在**代码运行期间**再发现（类型检测就可以很好的帮助我们做到这一点）。能在开发阶段发现错误，就不要在测试期间发现错误，能在测试期间发现错误，就不要在上线后发现错误。

**类型约束方案：**

- flow
- TypeScript



## 定义

TypeScript是拥有类型的JavaScript超集，它可以编译成普通、干净、完整的JavaScript代码，TypeScript理解成加强版的JavaScript：

- JavaScript所拥有的特性，TypeScript全部都是支持的，并且它紧随ECMAScript的标准，所以ES6、ES7、ES8等新语法标准，它都是

    支持的；

- 并且在语言层面上，不仅仅增加了类型约束，而且包括一些语法的扩展，比如枚举类型（Enum）、元组类型（Tuple）等；

- TypeScript在实现新特性的同时，总是保持和 ES 标准的同步甚至是领先；

- TypeScript最终会被编译成JavaScript代码，所以你并不需要担心它的兼容性问题，在编译时也不需要借助于 Babel 这样的工具



## 安装及运行

**安装：**

```
npm install typescript -g
```

**查看版本：**

```
tsc --version
```

**运行**

1. 通过 tsc 编译成 js, 再在浏览器或者 Node环境下运行 js代码

2. 通过 webpack，配置本地的 TypeScript 编译环境和开启一个本地服务，可以直接运行在浏览器上，下面是几个环境要点：

    - 本地依赖 TypeScript（webpack会在本地去查找TypeScript的依赖）

    - tsc --init 生成 tsconfig.json文件，对 TypeScript 进行相关的配置

    - 配置 tslint 来约束代码

    - webpack 环境搭建，在package.json中添加启动命令

        - 安装

            ```
            npm install webpack webpack-cli webpack-dev-server -D
            ```

        - 启动命令

            ```
            "scripts": {
              "serve": "cross-env NODE_ENV=development webpack-dev-server --mode=development --config build/webpack.config.js"
            }
            ```

        - cross-env

        - ts-loader

        - html-webpack-plugin

        - 配置webpack.config.js文件

            

3. 通过ts-node库，为TypeScript的运行提供执行环境；

    - 安装ts-node，另外ts-node需要依赖 tslib 和 @types/node 两个包

        ```
        npm install ts-node -g
        npm install tslib @types/node -g
        ```

    - 接通过 ts-node 来运行 TypeScript 的代码

        ```
        ts-node math.ts
        ```

        



## 变量声明

在 ts 中，定义变量需要指定 **标识符** 的类型。所以完整的声明格式如下：

```
var/let/const 标识符: 数据类型 = 赋值;
```

声明了类型后 TypeScript 就会进行类型检测，声明的类型可以称之为 类型注解；



## 类型推断

在开发中，有时候为了方便起见，我们并不会在声明每一个变量时都写上对应的数据类型，我们更希望可以通过TypeScript本身的特性帮助我们推断出对应的变量类型。

```
let message = 'What a beautiful day'
message = 10 // Type 'number' is not assignable to type 'string'
```



## javascript 类型

```javascript
// 字符
const name: string = "abc"

// 数字
let num1: number = 100 
let num2: number = 0b100
let num3: number = 0o100
let num4: number = 0x100

// 布尔
let flag: boolean = true

// 数组
// 一个数组中在TypeScript开发中, 最好存放的数据类型是固定的
const names1: Array<string> = [] // 不推荐(react jsx中是有冲突   <div></div>)
const names2: string[] = [] // 推荐

// object
const info = {
  	name: "why",
  	age: 18
}

// null & undefined
let n1: null = null
let n2: undefined = undefined

// symbol
const title1 = Symbol("title")
const title2 = Symbol('title')

const info = {
  	[title1]: "程序员",
  	[title2]: "老师"
}
```



## Typescript 类型

##### any

在某些情况下，我们确实无法确定一个变量的类型，并且可能它会发生一些变化时，可以使用 any

```javascript
let message: any = "Hello World"
message = 123
message = true

const arr: any[] = []
```



##### unknow

unknown 类型只能赋值给 any 和 unknown 类型（any 类型可以赋值给任意类型）

```javascript
function foo() {
  	return "abc"
}

function bar() {
  	return 123
}

let flag = true
let result: unknown // 最好不要使用any
if (flag) {
  	result = foo()
} else {
  	result = bar()
}

let message: string = result // 不能将类型“unknown”分配给类型“string”
let num: number = result // 不能将类型“unknown”分配给类型“number”
```



##### void

通常用来指定一个函数是没有返回值的，那么它的返回值就是 void 类型

```
function sum(num1: number, num2: number): void {
  	console.log(num1 + num2)
}

sum(20, 30)
```



##### never

never 表示永远不会发生值的类型，比如说，如果一个函数中是一个死循环或者抛出一个异常，那么这个函数会返回东西吗？不会，那么写void类型或者其他类型作为返回值类型都不合适，我们就可以使用never类型。

```
function foo(): never {
  // 死循环
  	while(true) {
	
  	}
}

function bar(): never {
   	throw new Error()
}
```



##### tuple

元组中每个元素都有自己特性的类型，根据索引值获取到的值可以确定对应的类型

```
const info: [string, number, number] = ['why', 18, 1.88]
const name = info[0]
console.log(name.length)
const age = info[1]
console.log(age.length) // 类型“number”上不存在属性“length”
```

数组做不到

```
const info: any[] = ["why", 18, 1.88]
const name = info[1]
console.log(name.length) // 在编辑器这里不会报错
```

tuple 通常可以作为返回的值：

```
function useState<T>(state: T) {
  	let currentState = state
  
  	const changeState = (newState: T) => {
    	currentState = newState
  	}

  	const tuple: [T, (newState: T) => void] = [currentState, changeState]
  	return tuple
}

const [counter, setCounter] = useState(10);
setCounter(1000)
const [title, setTitle] = useState("abc")
```



## 类型补充

##### 枚举类型

枚举类型是为数不多的 TypeScript 特性有的特性之一

```
enum Direction {
  	UP = 'up',
  	RIGHT = 'right',
  	DOWN = 'down',
  	LEFT = 'left'
}

```

###### 数字枚举

在 TS 中, 枚举内的每一个常量, 当你不设置值的时候, 默认就是 number 类型

```
enum Direction {
  	LEFT, // Direction.LEFT = 0
  	RIGHT, // Direction.LEFT = 1
  	TOP,
  	BOTTOM
}

// 也可以指定值
enum Pages {
    ONE = 10,    // 10
    TWO = 20,    // 20
    THREE = 30   // 30
}

// 指定常量后面的未指定常量, 就会按照 +1 的规则一次递增
enum Pages {
    ONE = 10,    // 10
    TWO,         // 11
    THREE        // 12
}

enum Pages {
    ONE,         // 0
    TWO = 10,    // 10
    THREE        // 11
}

enum Pages {
    ONE,         // 0
    TWO = 10,    // 10
    THREE,       // 11
    FOUR = 30,   // 30
    FIVE         // 31
}
```

**反向映射**：可以通过 key 得到对应的数字, 也可以通过对应的数字得到对应的 key

```
enum Pages {
    ONE,    // 0
    TWO,    // 1
    THREE   // 2
}

console.log(Pages.ONE)    // 0
console.log(Pages.TWO)    // 1
console.log(Pages.THREE)  // 2
console.log(Pages[0])     // 'ONE'
console.log(Pages[1])     // 'TWO'
console.log(Pages[2])     // 'THREE'
```

如何做到的呢？在编译的时候, 会同时将 key 和 value 分别颠倒编译一次。

```
var Pages;
(function (Pages) {
    Pages[Enum["ONE"] = 0] = "ONE"
    Pages[Enum["TWO"] = 1] = "TWO"
    Pages[Enum["THREE"] = 2] = "THREE"
})(Pages || (Pages = {}));
```



###### 异构枚举

其实就是在一个枚举集合内同时混合了数字枚举和字符串枚举

```
enum Info {
  ONE,
  UP = 'up',
  TWO = 2,
  LEFT = 'left'
}
```

注意：在枚举集合内,  如果前一个是 字符串枚举, 那么下一个必须要手动赋值, 不然会报错。如果前一个是 数字枚举, 那么下一个可以不必要手动赋值, 会按照上一个 +1 计算。

###### 枚举合并

在 TS 内的枚举, 是支持合并的。多个枚举类型可以分开书写, 会在编译的时候自动合并

```
enum Direction {
  UP = 'up',
  RIGHT = 'right',
  DOWN = 'down',
  LEFT = 'left'
}

enum Direction {
  TOP = 'top',
  BOTTOM = 'bottom'
}

function util(dir: Direction) {}

util(Direction.BOTTOM)
util(Direction.LEFT)
```





##### 函数参数

```
function sum(num1: number, num2: number) {
  	return num1 + num2
}
```

我们也可以添加返回值的类型注解，这个注解出现在函数列表的后面，在开发中,通常情况下可以不写返回值的类型，TypeScript会根据 return 返回值推断函数的返回类型

```
function sum(num1: number, num2: number): number {
  return num1 + num2
}
```



##### 匿名函数参数

```
const names = ["abc", "cba", "nba"]
// item 根据上下文的环境推导出来的, 这个时候可以不添加的类型注解
// 上下文中的函数: 可以不添加类型注解
names.forEach(function(item) {
  	console.log(item.split(""))
})
```

我们并没有指定 item 的类型，但是 item 是一个 string 类型，这是因为 TypeScript 会根据 forEach 函数的类型以及数组的类型推断出item 的类型；这个过程称之为**上下文类型（*contextual typing*）**，因为函数执行的上下文可以帮助确定参数和返回值的类型；



##### 对象类型

对象类型的属性之间可以使用 , 或者 ; 来分割，最后一个分隔符是可选的；每个属性的类型部分也是可选的，如果不指定，那么就是any类型；

```
function printPoint(point: {x: number, y: number}) {
  	console.log(point.x);
  	console.log(point.y)
}
```

###### 可选类型

对象类型也可以指定哪些属性是可选的，可以在属性的后面添加一个?：

```
function printPoint(point: {x: number, y: number, z?: number}) {
    console.log(point.x)
    console.log(point.y)
    console.log(point.z)
}

printPoint({x: 123, y: 321})
printPoint({x: 123, y: 321, z: 111})
```



##### 联合类型

联合类型是由两个或者多个其他类型组成的类型，表示可以是这些类型中的任何一个值，联合类型中的每一个类型被称之为**联合成员（union's *members*）**

传入给一个联合类型的值是非常简单的，只要保证是联合类型中的某一个类型的值即可，但是我们拿到这个值之后，我们应该如何使用它呢？因为它可能是任何一种类型，我们需要使用缩小（narrow）联合。TypeScript可以根据我们缩小的代码结构，推断出更加具体的类型；

```
function printID(id: number|string|boolean) {
    // 使用联合类型的值时, 需要特别的小心
    // narrow: 缩小
    if (typeof id === 'string') {
    	// TypeScript 帮助确定 id 一定是 string 类型
    	console.log(id.toUpperCase())
    } else {
    	console.log(id)
    }
}

printID(123)
printID("abc")
printID(true)
```

> 可选类型可以看做是 类型 和 undefined 的联合类型：让一个参数本身是可选的，一个参数一个可选类型的时候, 它其实类似于是这个参数是 类型|undefined 的联合类型



##### 交叉类型

交叉类似表示需要满足多个类型的条件；交叉类型使用 & 符号； 

```
type myType = number & string // myType 其实是一个 never 类型
```

开发中，通常是对对象类型进行交叉的：

```
interface ISwim {
  swimming: () => void
}

interface IFly {
  flying: () => void
}

type MyType = ISwim & IFly

// ❌ 不能将类型“{ swimming(): void; }”分配给类型“MyType2”。类型 "{ swimming(): void; }" 中缺少属性 "flying"，但类型 "IFly" 中需要该属性。
const obj: MyType = { 
  swimming() {

  }
}

// 🉑️
const obj2: MyType = { 
  swimming() {

  }
  flying() {
    
  }
}

```



##### 类型别名

```
// type用于定义类型别名(type alias)
type IDType = string | number | boolean
type PointType = {
  x: number
  y: number
  z?: number
}

function printId(id: IDType) {
}

function printPoint(point: PointType) {
}
```



##### 类型断言 as

有时候TypeScript无法获取具体的类型信息，这个我们需要使用类型断言（Type Assertions）。

比如我们通过document.getElementById，TypeScript只知道该函数会返回 HTMLElement ，但并不知道它具体的类型：

```
const el = document.getElementById("why") as HTMLImageElement
el.src = "url地址"
```

```
class Person {}

class Student extends Person {
  studying() {
  }
}

function sayHello(p: Person) {
  (p as Student).studying()
}
```

TypeScript只允许类型断言转换为 更具体 或者 不太具体 的类型版本，此规则可防止不可能的强制转换：

```
const str1 = 'why' as number // ❌ 类型 "string" 到类型 "number" 的转换可能是错误的，因为两种类型不能充分重叠。
const str = ('why' as unknown) as number // 🉑️
```

###### 非空类型断言

```
function printMessageLength(message?: string) {
	console.log(message.length) // “message”可能为“未定义”，因为传入的 message 有可能是为 undefined 的
}
```

但是，我们确定传入的参数是有值的，这个时候我们可以使用非空类型断言。非空断言使用的是 ! ，表示可以确定某个标识符是有值的，跳过ts在编译阶段对它的检测。

```
function printMessageLength(message?: string) {
	console.log(message!.length)
}
```



##### 字面量类型

```
// "Hello World"也是可以作为类型的, 叫做字面量类型
const str: "Hello World" = "Hello World"
```

默认情况下这么做是没有太大的意义的，但是我们可以将多个类型联合在一起

```
// 字面量类型的意义, 就是必须结合联合类型
type Alignment = 'left' | 'right' | 'center'

let align: Alignment = 'left'
align = 'right'
align = 'center'

align = 'hehehehe' // 不能将类型 "hehehehe" 分配给类型 “Alignment”
```

###### 字面量推理

```
const info = {
	url: 'https://www.coderwhy.org/abc',
	method: 'GET'
}

function request(url: string, method: 'GET' | 'POST') {}

request(info.url, info.method) // info.method 会报错，无法将一个 string 赋值给一个 字面量 类型
```

正确写法

```
type Method = 'GET' | 'POST'
function request(url: string, method: Method) {}

const options = {
  url: "https://www.coderwhy.org/abc",
  method: "POST"
} as const

request(options.url, options.method)
```



##### 类型缩小和类型保护

我们可以通过类似于 typeof padding === "number" 的判断语句，来改变TypeScript的执行路径；在给定的执行路径中，我们可以缩小比声明时更小的类型，这个过程称之为 缩小;

而我们编写的 typeof padding === "number 可以称之为 类型保护（type guards）；常见的类型保护有如下几种：

- typeof
- 平等缩小（比如===、!==）
- instanceof
- in

###### typeof

```
type IDType = number | string
function printID(id: IDType) {
    if (typeof id === 'string') {
    	console.log(id.toUpperCase())
    } else {
    	console.log(id)
    }
}
```

###### 平等缩小 ===，！==

```
type Direction = "left" | "right" | "top" | "bottom"
function printDirection(direction: Direction) {
	if (direction == 'left') {
		
	} else if (direction == 'right') {
	
	}
}
```

###### instanceof

```
function printTime(time: string | Date) {
  	if (time instanceof Date) {
    	console.log(time.toUTCString())
  	} else {
    	console.log(time)
  	}
}
```

```
class Student {
  	studying() {}
}

class Teacher {
 	 teaching() {}
}

function work(p: Student | Teacher) {
  	if (p instanceof Student) {
    	p.studying()
  	} else {
    	p.teaching()
  	}
}

const stu = new Student()
work(stu)
```

###### in

```
type Fish = {
  swimming: () => void
}

type Dog = {
  running: () => void
}

function walk(animal: Fish | Dog) {
  if ('swimming' in animal) {
    animal.swimming()
  } else {
    animal.running()
  }
}

const fish: Fish = {
  swimming() {
    console.log("swimming")
  }
}

walk(fish)
```



## 函数

##### 函数类型

```
type calcFun = (num1: number, num2: number) => void

function calc(fn: calcFun) {
	console.log(fn(20, 30))
}

function sum(num1: number, num2: number) {
	return num1 + num2
}

function mul(num1: number, num2: number) {
	return num1 * num2
}

calc(sum)
calc(mul)
```



##### 函数参数类型

```
// 可选类型是必须写在必选类型的后面的
// y -> undefined | number
function foo(x: number, y?: number) {

}

foo(20, 30)
foo(20)
```



##### 函数默认参数

```
// 必传参数 - 有默认值的参数 - 可选参数 
// x 的类型其实是 undefined 和 number 类型的联合
function foo(y: number, x: number = 20) {
  console.log(x, y)
}

foo(30)
```



##### 函数剩余参数

```
function sum(initalNum: number, ...nums: number[]) {
  let total = initalNum
  for (const num of nums) {
    total += num
  }
  return total
}

console.log(sum(20, 30))
console.log(sum(20, 30, 40))
```



##### this 推导

```
// this是可以被推导出来 info对象(TypeScript推导出来)
const info = {
  name: "why",
  eating() {
    console.log(this.name + " eating")
  }
}

info.eating()
```

###### 不确定的 this

```
function eating() {
   	console.log(this.name + " eating")
}

const info = {
  	name: "why",
  	eating
}

info.eating()
```

以上代码会报错，这里对于 eating 的调用来说，我们虽然将其放到了info中，通过info去调用，this 依然是指向info对象的；

但是对于 TypeScript 编译器来说，这个代码是非常不安全的，因为我们也有可能直接调用函数，或者通过别的对象来调用函数；

这个时候，通常 TypeScript 会要求我们明确的指定 this 的类型：

```
type ThisType = { name: string };

function eating(this: ThisType, message: string) {
  	console.log(this.name + " eating", message);
}

const info = {
  	name: "why",
  	eating: eating,
};

// 隐式绑定
info.eating("哈哈哈");

// 显示绑定
eating.call({name: "kobe"}, "呵呵呵")
eating.apply({name: "james"}, ["嘿嘿嘿"])
```



##### 函数重载

如果我们编写了一个add函数，希望可以对字符串和数字类型进行相加，应该如何编写呢？

###### 利用联合类型

```
function add(a1: number | string, a2: number | string) {
  	if (typeof a1 === "number" && typeof a2 === "number") {
    	return a1 + a2
  	} else if (typeof a1 === "string" && typeof a2 === "string") {
    	return a1 + a2
  	}
}

add(10, 20)
```

缺点：1.进行很多的逻辑判断(类型缩小)    2.返回值的类型依然是不能确定

###### 重载

函数的名称相同, 但是参数不同的几个函数, 就是函数的重载

```
function add(num1: number, num2: number): number; // 没函数体
function add(num1: string, num2: string): string;

// 实现函数
function add(num1: any, num2: any): any {
  if (typeof num1 === 'string' && typeof num2 === 'string') {
    return num1.length + num2.length
  }
  return num1 + num2
}

const result = add(20, 30)
const result2 = add("abc", "cba")
console.log(result)
console.log(result2)

// 在函数的重载中, 实现函数是不能直接被调用的
//add({name: "why"}, {age: 18}) // 即使实现函数参数是 any
```

###### 动手做一做

我们现在有一个需求：定义一个函数，可以传入字符串或者数组，获取它们的长度。 

```
// 实现方式一: 联合类型
function getLength(args: string | any[]) {
  return args.length
}

console.log(getLength("abc"))
console.log(getLength([123, 321, 123]))
```

```
// 实现方式二: 函数的重载
function getLength(args: string): number;
function getLength(args: any[]): number;

function getLength(args: any): number {
  return args.length
}

console.log(getLength("abc"))
console.log(getLength([123, 321, 123]))
```

建议：在可能的情况下，尽量选择使用联合类型来实现。



## 类

##### 定义

- 使用class关键字来定义一个类；
- 可以声明一些类的属性：在类的内部声明类的属性以及对应的类型
    - 如果类型没有声明，那么它们默认是any的；
    - 也可以给属性设置初始化值；
    - 在默认的strictPropertyInitialization模式下面我们的属性是必须初始化的，如果没有初始化，那么编译时就会报错；确实不希望给初始值，可以使用 name!: string语法；
- 类可以有自己的构造函数constructor，当我们通过new关键字创建一个实例时，构造函数会被调用；构造函数不需要返回任何值，默认返回当前创建出来的实例；
- 类中可以有自己的函数，定义的函数称之为方法；

```
class Person {
  name: string
  age: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }

  eating() {
    console.log(this.name + " eating")
  }
}

const p = new Person("why", 18)
console.log(p.name)
console.log(p.age)
p.eating()
```



##### 继承

使用 extends 关键字来实现继承，子类中使用 super 来访问父类。

```
class Person {
  name: string
  age: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }

  eating() {
    console.log("eating 100行")
  }
}

class Student extends Person {
  sno: number

  constructor(name: string, age: number, sno: number) {
    // super调用父类的构造器
    super(name, age)
    this.sno = sno
  }

  eating() {
    console.log("student eating")
    super.eating()
  }

  studying() {
    console.log("studying")
  }
}

const stu = new Student("why", 18, 111)
console.log(stu.name)
console.log(stu.age)
console.log(stu.sno)

stu.eating()
```



##### 成员修饰符

在TypeScript中，类的属性和方法支持三种修饰符： public、private、protected

public 修饰的是在任何地方可见、公有的属性或方法，默认编写的属性就是public的；

private 修饰的是仅在同一类中可见、私有的属性或方法；

protected 修饰的是仅在类自身及子类中可见、受保护的属性或方法；

```
class Person {
  private name: string = ""

  // 封装了两个方法, 通过方法来访问name
  getName() {
    return this.name
  }

  setName(newName) {
    this.name = newName
  }
}

const p = new Person()
console.log(p.getName())
p.setName("why")

export {}
```



##### 只读属性

```
class Person {
  // 1.只读属性是可以在构造器中赋值, 赋值之后就不可以修改
  // 2.属性本身不能进行修改, 但是如果它是对象类型, 对象中的属性是可以修改
  readonly name: string
  age?: number
  readonly friend?: Person
  constructor(name: string, friend?: Person) {
    this.name = name
    this.friend = friend
  }
}

const p = new Person("why", new Person("kobe"))
console.log(p.name)
console.log(p.friend)

// 不可以直接修改friend
// p.friend = new Person("james")
if (p.friend) {
  p.friend.age = 30
} 
```



##### getters / setters

前面一些私有属性我们是不能直接访问的，或者某些属性我们想要监听它的获取(getter)和设置(setter)的过程，这个时候我们可以使用存取器。

```
class Person {
  private _name: string
  constructor(name: string) {
    this._name = name
  }

  // 访问器 setter/getter
  // setter
  set name(newName) {
    this._name = newName
  }
  // getter
  get name() {
    return this._name
  }
}

const p = new Person("why")
p.name = "coderwhy"
console.log(p.name)
```



##### 静态成员

在类中定义的成员和方法都属于对象级别的, 在开发中, 我们有时候也需要定义类级别的成员和方法。

```
class Student {
  static time: string = "20:00"

  static attendClass() {
    console.log("去学习~")
  }
}

console.log(Student.time)
Student.attendClass()
```



##### 抽象类 abstract

在 TypeScript 中没有具体实现的方法(没有方法体)，就是抽象方法。

抽象方法，必须存在于抽象类中；抽象类是使用 abstract 声明的类；抽象类有如下的特点：

- 抽象类是不能被实例的（也就是不能通过 new 创建） 

- 抽象方法必须被子类实现，否则该类必须是一个抽象类；

```
function makeArea(shape: Shape) {
  return shape.getArea()
}


abstract class Shape {
  abstract getArea(): number
}


class Rectangle extends Shape {
  private width: number
  private height: number

  constructor(width: number, height: number) {
    super()
    this.width = width
    this.height = height
  }

  getArea() {
    return this.width * this.height
  }
}

class Circle extends Shape {
  private r: number

  constructor(r: number) {
    super()
    this.r = r
  }

  getArea() {
    return this.r * this.r * 3.14
  }
}

const rectangle = new Rectangle(20, 30)
const circle = new Circle(10)

console.log(makeArea(rectangle))
console.log(makeArea(circle))
```



##### 类的类型

类本身也是可以作为一种数据类型的：

```
class Person {
  name: string = "123"
  eating() {

  }
}

const p = new Person()

const p1: Person = {
  name: "why",
  eating() {

  }
}
```



## 接口

我们通过 type 可以用来声明一个对象类型，还可以通过接口来声明

```
type Point {
	x: number,
	y: number
}

interface Point2 {
	x: number,
	y: number
}
```

接口支持定义可选属性，只读属性

```
interface IInfoType {
  readonly name: string
  age: number
  friend?: {
    name: string
  }
}
```



##### 索引类型

以上都是对象的属性名、类型、方法都是确定的，如果遇到不确定的呢？可以通过 interface 来定义索引类型

```
interface IndexLanguage {
  [index: number]: string
}

const frontLanguage: IndexLanguage = {
  0: "HTML",
  1: "CSS",
  2: "JavaScript",
  3: "Vue"
}


interface ILanguageYear {
  [name: string]: number
}

const languageYear: ILanguageYear = {
  "C": 1972,
  "Java": 1995,
  "JavaScript": 1996,
  "TypeScript": 2014
}
```



##### 函数类型

```
// type CalcFn = (n1: number, n2: number) => number // (推荐这种写法)

interface CalcFn {
  (n1: number, n2: number): number
}

function calc(num1: number, num2: number, calcFn: CalcFn) {
  return calcFn(num1, num2)
}

const add: CalcFn = (num1, num2) => {
  return num1 + num2
}

calc(20, 30, add)
```



##### 接口继承

接口是支持多继承的（类不支持多继承）

```
interface ISwim {
  swimming: () => void
}

interface IFly {
  flying: () => void
}


interface IAction extends ISwim, IFly {

}

const action: IAction = {
  swimming() {
  },
  flying() {
  }
}
```



##### 接口实现

接口定义后，也是可以被类实现的。如果被一个类实现，那么在之后需要传入接口的地方，都可以将这个类传入，这就是面向接口开发。

```
interface ISwim {
  	swimming: () => void
}

interface IEat {
  	eating: () => void
}

class Person implements ISwim, IEat {
	swimming() {
    	console.log('Swmming')
  	}

  	eating() {
    	console.log('Eating')
  	}
}

function swim(swimmer: ISwim) {
	swimmer.swimming()
}

const p = new Person()
swim(p)
```



##### interface 和 type 的区别

- 如果是定义非对象类型，通常推荐使用 type
- 如果是定义对象类型，那么他们是有区别的：
    - interface 可以重复的对某个接口来定义属性和方法
    - 而 type 定义的是别名，别名是不能重复的；

```
interface IFoo {
  name: string
}

interface IFoo {
  age: number
}

const foo: IFoo = {
  name: "why",
  age: 18
}
```

```
// ❌ 标识符“IBar”重复
type IBar = {
  name: string
  age: number
}

type IBar = {
}
```



##### 字面量复制

TypeScript 在字面量直接赋值的过程中，为了进行类型推导会进行严格的类型限制

```
interface IPerson {
  name: string
  age: number
  height: number
}

const p: IPerson = {
    name: "why",
    age: 18,
    height: 1.88,
    address: "广州市" // ❌ 不能将类型“{ name: string; age: number; height: number; address: string; }”分配给类型“IPerson”。对象字面量只能指定已知属性，并且“address”不在类型“IPerson”中
}
```

可以将一个 变量标识符 赋值给其他的变量时，会进行 freshness 擦除操作

```
interface IPerson {
      name: string
      age: number
      height: number
}

const info = {
      name: "why",
      age: 18,
      height: 1.88,
      address: "广州市"
}

// freshness擦除
const p: IPerson = info
```



## 泛型

在定义这个函数时, 我不决定这些参数的类型，而是让调用者以参数的形式告知,我这里的函数参数应该是什么类型

```
function foo<Type>(arg: Type): Type {
  	return arg
}

// 调用
// 1. foo<string>('123')
// 2. foo('123') // 字面量可以直接被推导出来
```

传入多个类型

```
function foo<T, E, O>(arg1: T, arg2: E, arg3?: O, ...args: T[]) {

}

foo<number, string, boolean>(10, "abc", true)
```

平时在开发中我们可能会看到一些常用的名称：

- T：Type的缩写，类型
- K、V：key和value的缩写，键值对
- E：Element的缩写，元素
- O：Object的缩写，对象



##### 泛型接口

```
interface IPerson<T1 = string, T2 = number> {
  name: T1
  age: T2
}

const p: IPerson = {
  name: "why",
  age: 18
}
```



##### 泛型类

```
class Point<T> {
  x: T
  y: T
  z: T

  constructor(x: T, y: T, z: T) {
    this.x = x
    this.y = y
    this.z = y
  }
}

const p1 = new Point("1.33.2", "2.22.3", "4.22.1")
const p2 = new Point<string>("1.33.2", "2.22.3", "4.22.1")
const p3: Point<string> = new Point("1.33.2", "2.22.3", "4.22.1")
```



##### 泛型约束

有时候我们希望传入的类型有某些共性，但是这些共性可能不是在同一种类型中，比如 string 和 array 都是有 length 的，或者某些对象也是会有 length 属性的；

```
interface ILength {
  length: number
}

function getLength<T extends ILength>(arg: T) {
  return arg.length
}

getLength("abc")
getLength(["abc", "cba"])
getLength({length: 100})
```





## 其他

##### 命名空间

命名空间在 TypeScript 早期时，称之为内部模块，主要目的是将一个模块内部再进行作用域的划分，防止一些命名冲突的问题。

```
export namespace time {
  export function format(time: string) {
    return "2222-02-22"
  }

  export function foo() {

  }

  export let name: string = "abc"
}

export namespace price {
  export function format(price: number) {
    return "99.99"
  }
}
```



##### 类型查找

```
const imageEl = document.getElementById('img') as HTMLImageElement
```

大家是否有想过 HTMLImageElement类型来自哪里呢？甚至是document为什么可以有getElementById的方法呢？

其实这里就涉及到typescript对类型的管理和查找规则了。

之前编写的 typescript 文件都是 .ts 文件，这些文件最终会输出 .js 文件，也是我们通常编写代码的地方，还有另外一种文件 .d.ts 文件，它是用来做类型的声明(declare)。 它仅仅用来做类型检测，告知 typescript 我们有哪些类型。

typescript会在哪里查找我们的类型声明呢？

- 内置类型声明；

    内置类型声明是typescript自带的、帮助我们内置了JavaScript运行时的一些标准化API的声明文件；包括比如Math、Date等内置类型，也包括DOM API，比如Window、Document等；

    https://github.com/microsoft/TypeScript/tree/main/lib

- 外部定义类型声明；

    外部类型声明通常是我们使用一些库（比如第三方库）时，需要的一些类型声明，这些库通常有两种类型声明方式：

    - 在自己库中进行类型声明（编写.d.ts文件），比如axios

    - 通过社区的一个公有库 DefinitelyTyped 存放类型声明文件

        - https://github.com/DefinitelyTyped/DefinitelyTyped/

        - https://www.typescriptlang.org/dt/search?search=（查找声明安装的地址）

            比如我们安装 react 的类型声明： npm i @types/react --save-dev

- 自己定义类型声明；

    情形1: 我们使用的第三方库是一个纯的 JavaScript 库，没有对应的声明文件；比如 lodash

    情形2: 我们给自己的代码中声明一些类型，方便在其他地方直接进行使用；

    **声明变量/函数/类**

    ```
    
    declare let whyName: string
    declare let whyAge: number
    declare let whyHeight: number
    
    declare function whyFoo(): void
    
    declare class Person {
      name: string
      age: number
      constructor(name: string, age: number)
    }
    ```

    **声明模块**

    比如 lodash 模块默认不能使用的情况，可以自己来声明这个模块：

    ```
    
    declare module 'lodash' {
      export function join(arr: any[]): void
    }
    ```

    **声明文件**

    比如在开发vue的过程中，默认是不识别我们的.vue文件的，那么我们就需要对其进行文件的声明；

    比如在开发中我们使用了 jpg 这类图片文件，默认typescript也是不支持的，也需要对其进行声明；

    ```
    declare module '*.vue' {
    	import { DefineComponent } from 'vue'
    	const component: DefineComponent
    	
    	export default component
    }
    
    declare module '*.jpg' {
    	const src: string
    	export default src
    }
    
    declare module '*.jpg'
    declare module '*.jpeg'
    declare module '*.png'
    declare module '*.svg'
    declare module '*.gif'
    ```

    **声明命名空间**

    我们在 index.html 中直接引入了 jQuery，进行命名空间的声明，在main.ts中就可以使用了

    ```
    // 声明命名空间
    declare namespace $ {
      export function ajax(settings: any) void
    }
    
    // 使用
    $.ajax({
    	url: '',
    	success: function() {}
    })
    ```



##### 配置文件 tsconfig

https://www.typescriptlang.org/tsconfig

