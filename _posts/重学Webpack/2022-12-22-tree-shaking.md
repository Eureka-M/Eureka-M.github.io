---
layout:     post
title:     tree-shaking
subtitle:  
date:       2022-12-22
author:     
header-img: 
catalog: true
tags:
  - 重学 webpack
typora-root-url: ..
---

## 定义

Tree Shaking是一个术语，在计算机中表示消除死代码（dead_code）

Tree-shaking的本质是消除无用的js代码。无用代码消除在广泛存在于传统的编程语言编译器中，编译器可以判断出某些代码根本不影响输出，然后消除这些代码，这个称之为DCE（dead code elimination）。

Tree-shaking 和传统的 DCE 的方法又不太一样，传统的 DCE 消灭不可能执行的代码，而 Tree-shaking 更关注消除没有用到的代码。

## DEC

- 代码不会被执行，不可到达
- 代码执行的结果不会被用到
- 代码只会影响死变量（只写不读）

传统编译型的语言中，都是由编译器将 Dead Code 从 AST（抽象语法树）中删除。在 javascript 中，是由代码压缩优化工具 uglify 来完成的。

## Tree-shaking

tree-shaking 的消除原理是依赖于 ES6 的模块特性：

- 只能作为模块的顶层出现（不嵌套在条件语句中）
- import 的模块名只能是字符串常量（只对文件进行字符串读取）
- 导入和导出语句没有动态部分

意味着，依赖关系是确定的，和运行时的状态无关，可以进行可靠的静态分析，然后进行消除。

所谓静态分析就是不执行代码，从字面量上对代码进行分析，ES6之前的模块化，比如我们可以动态 require 一个模块，只有执行后才知道引用的什么模块，这个就不能通过静态分析去做优化。这也是为什么 rollup 和 webpack 2 都要用 ES6 module syntax 才能 tree-shaking。

### 局限性

tree-shaking 对于无用代码，无用模块的消除，都是有限的，有条件的。

tree-shaking对函数效果较好，但无用的类不能消除。举个🌰。

```
function Menu() {
}

Menu.prototype.show = function() {
}

// 扩展 Array
Array.prototype.unique = function() {
    // 将 array 中的重复元素去除
}

export default Menu;
```

删除里 menu.js，那对 Array 的扩展也会被删除，就会影响功能。这里的对 Array 的扩展，就是一个 sideEffect 副作用。

**sideEffect**(副作用) 的定义是，在导入时会执行特殊行为的代码，而不是仅仅暴露一个 export 或多个 export。

对于有副作用的 export ，tree-shaking 不会移除这段代码。

```
// package.json
{
  "name": "your-project",
  "sideEffects": false
}
```

`"sideEffects": false`可以强制标识该包模块不存在副作用，那么不管它是否真的有副作用，只要它没有被引用到，整个 模块/包 都会被完整的移除。

### 原理

Tree Shaking 在去除代码冗余的过程中，程序会从入口文件出发，扫描所有的模块依赖，以及模块的子依赖，然后将它们链接起来形成一个 “抽象语法树” (AST)。随后，运行所有代码，查看哪些代码是用到过的，做好标记。最后，再将 “抽象语法树” 中没有用到的代码 “摇落”。经历这样一个过程后，就去除了没有用到的代码。

![image-20230611175906988](/../img/postImage/image-20230611175906988.png)

### webpack tree-shaking 配置

#### sideEffects

如上。pakeage.json文件添加`sideEffects`配置。"sideEffects" 属性是一个数组，其中包含需要保留的文件或模块的路径。

```
{
      "name": "webpack-app",
      "version": "1.0.0",
      "description": "webpack app description",
      "main": "",
      "sideEffects": ["./src/some-side-effectful-file.js"]
}
```

在这个例子中，"./src/some-side-effectful-file.js" 和所有的 ".css" 文件都被认为是有副作用的，不应该被 tree-shaking 消除。

##### .babelrc

在使用 babel 编译时，默认会将模块编译成 CommonJS，将`modules`设置为`false`，避免将 ES Module 模块类型转换

```
{
  "presets": [
    [
        "es2015",
        {
            "modules": false
        }
    ]
  ]
}
```

##### Pure 标注纯函数调用

告诉 webpack 一个函数调用是无副作用的，只要通过 `/*#__PURE__*/` 注释。它可以被放到函数调用之前，用来标记它们是无副作用的(pure)。传到函数中的入参是无法被刚才的注释所标记，需要单独每一个标记才可以。如果一个没被使用的变量定义的初始值被认为是无副作用的（pure），它会被标记为死代码，不会被执行且会被压缩工具清除掉。当 [`optimization.innerGraph`](https://webpack.docschina.org/configuration/optimization/#optimizationinnergraph) 被设置成 `true` 时这个行为会被启用。

```
/*#__PURE__*/ double(55);
```

