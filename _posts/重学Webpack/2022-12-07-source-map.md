---
layout:     post
title:     source-map
subtitle:  
date:       2022-12-07
author:     
header-img: 
catalog: true
tags:
  - 重学 webpack
typora-root-url: ..
---

## 为什么会有 source-map ?

真实跑在浏览器和我们编写的代码是有差异的

- ES6转换成ES5，TypeScript转换成 JavaScript
- 对应的代码行号，列号阿兹编译后不一致
- 代码进行了丑化压缩，编码命名被修改

而当代码报错时，需要调试！！

## source-map

从已`转换的代码`，映射到`原始的源文件`。使浏览器可以`重构原始源`并在调试器中`显示重建的原始源`

## source-map 设置

```
module.exports = {
	devtool: 'source-map' | 'eval-source-map'
}
```

### false none eval

不生成 source-map。

false：不使用 source-map, 就是没有任何和 source-map 相关的内容。

none：production 模式下的默认值，不生成 source-map。

eval: developent 模式模式下的默认值，不生成 source-map。但是它会在eval执行的代码中，添加 //# sourceURL=；它会被浏览器在执行时解析，并且在调试面板中生成对应的一些文件目录，方便我们调试代码；

### source-map

生成一个独立的 source-map 文件，并且在 bundle 文件中有一个注释，指向 source-map 文件；bundle文件中有如下的注释：

```
//# sourceMappingURL=bundle.js.map
```

开发工具会根据这个注释找到source-map文件，并且解析；

### eval-source-map

会生成 sourcemap，但是source-map是 以DataUrl 添加到 eval 函数的后面。

<img src="/../img/postImage/image-20230611113627533.png" alt="image-20230611113627533" style="zoom:50%;" />

### inline-source-map

会生成 sourcemap，但是 source-map 是以 DataUrl 添加到 bundle 文件的后面

<img src="/../img/postImage/image-20230611113716034.png" alt="image-20230611113716034" style="zoom: 50%;" />

### cheap-source-map

会生成 sourcemap，但是会更加高效一些（cheap 低开销），因为它没有生成列映射（Column Mapping）

![image-20230611113915116](/../img/postImage/image-20230611113915116.png)

### cheap-module-source-map

会生成 sourcemap，类似于 cheap-source-map，但是对`源自loader的sourcemap处理会更好`(其实是如果 loader 对我们的源码进行了特殊的处理，比如 babel； 比 cheap-source-map 更接近源代码)。

<img src="/../img/postImage/image-20230611114204389.png" alt="image-20230611114204389" style="zoom:50%;" />

### hidden-source-map

会生成 sourcemap，但是不会对 source-map 文件进行引用；相当于删除了打包文件中对 sourcemap 的引用注释；可以手动添加，那么sourcemap就会生效。

### nosources-source-map

会生成 sourcemap，但是生成的 sourcemap 只有错误信息的提示，不会生成源代码文件；

### 多值设置

webpack 提供 26 个值可以进行组合配置。组合的规则如下：

Inline-|hidden-|eval: 三个值时三选一

nosources: 可选值

cheap 可选值，并且可以跟随 module 的值

```
[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map
```

**开发阶段**：推荐使用 source-map 或者 cheap-module-source-map

**测试阶段**：推荐使用 source-map 或者 cheap-module-source-map

**发布阶段**：false