---
layout:     post
title:     Placeholders
subtitle:  
date:       2022-12-03
author:     
header-img: 
catalog: true
tags:
  - 重学 webpack
typora-root-url: ..
---

有时候希望打包后的文件名称按照一定的规则显示，比如说：保留原来的`文件名`、`拓展名`、为了防止重复，包含一个`hash`值。这个时候可以使用 [PlaceHolders](https://webpack.js.org/loaders/file-loader/#placeholders)来完成，常见 placeholder 如下：

- [ext]: 处理文件的拓展名

- [name]: 处理文件的名称

- [hash]: 文件的内容，使用MD4的散列函数处理，生成一个128位的	"hash 值"

- [contentHash]: 同上

- [hash:<length>]: 截图hash的长度，默认32个字符太长

- [Path]: 文件相对于webpack配置文件的路径

用法如下：

```
{
	test: /\.(png|jpe?g)$/i,
	use: {
		loader: 'file-loader',
		options: {
			name: 'img/[name].[hash:8].[ext]',
			outputPath: 'img' // 指定输出文件夹
		}
	}
}
```

