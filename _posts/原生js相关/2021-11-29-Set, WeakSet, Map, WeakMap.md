---
layout:     post
title:      Set,WeakSet,Map,WeakMap
subtitle:  
date:       2021-11-29
author:     
header-img: 
catalog: true
tags:
    - 原生 Javascript 相关
typora-root-url: ..

---

# Set, WeakSet, Map, WeakMap

## Set

`Set`对象允许存储任何类型的唯一值，不论是原始值或者是对象引用。

## WeakSet

weakSet中只能存放对象类型，不能存放基本数据类型

weakSet对元素的引用只是弱引用，如果没有其他引用对这个对象进行引用，那么GC可以对该对象进行回收。

```
let obj = { a: 1, b: 2 }
let weakSet = new WeakSet()
let set = new Set()
weakSet.add(obj)
set.add(obj)
// 清空引用
obj = null
console.log(weakSet) // WeakSet {}
console.log(set) // Set(1) {{…}}
```

因为WeakSet只是对对象的弱引用，如果遍历获取到其中的元素，那么有可能会造成对象不难正常的销毁，所以存储到weakset中的对象是`不可遍历`的。

WeakSet的应用场景

```
const oBtn1 = document.querySelector(".btn1");
const oBtn2 = document.querySelector(".btn2");

function handleBtn1Click() {

}
function handleBtn2Click() {

}
oBtn1.addEventListener("click", handleBtn1Click, false);
oBtn2.addEventListener("click", handleBtn2Click, false);

//如果此时我需要删除这两个按钮元素, 但是他俩绑定了事件处理函数，事件处理函数没有销毁，还存在内存中
oBtn1.remove();
oBtn2.remove();
// 只能通过手动销毁
handleBtn1Click = null;
handleBtn2Click = null;
```

```
const oBtn1 = document.querySelector(".btn1");
const oBtn2 = document.querySelector(".btn2");

function handleBtn1Click() {

}
function handleBtn2Click() {

}

const map = new WeakMap()
//WeakMap的键名为弱引用，如果别人吧oBtn1或者oBtn2删除了，那么WeakMap里面对应的键名会被回收掉，键名回收掉之后键值也会回收掉
map.set(oBtn1, handleBtn1Click)
map.set(oBtn2 handleBtn2Click)
oBtn1.addEventListener("click", map.get(oBtn1), false);
oBtn2.addEventListener("click",  map.get(oBtn2), false);
oBtn1.remove();
oBtn2.remove();
```

> 方便GC自动回收

## Map

Map用于存储映射关系。

相对于使用对象来存储映射关系，Map可以使用对象作为key指，但是对象存储映射关系只能用字符串（ES6中新增了Symbol）

```
const obj1 = { name: "why" }
const obj2 = { name: "kobe" }
const info = {
  [obj1]: "aaa",
  [obj2]: "bbb"
}

console.log(info) // {[Object Object]: "bbb"} 因为对象会被转成字符串就会变成[Object Object]
```

而Map可以使用任意类型作为key，当需要使用对象作为key值的时候，就可以使用Map

```
const map2 = new Map([[obj1, "aaa"], [obj2, "bbb"], [2, "ddd"]])
console.log(map2)
// 输出
Map(3){
	{
		name: "why"
	} => "aaa",
	{
		name: "kobe"
	} => "bbb",
	2 => 'ddd'
}
```

## WeakMap

WeakMap也是以键值对的形式存在的，weakMap无法遍历。

WeakMap区别于Map

1. WeakMap的key只能使用对象，不能接受其他的类型作为key

2. weakMap对key对象的引用只是弱引用，如果没有其他引用对这个对象进行引用，那么GC可以对该对象进行回收。

区别于WeakSet, WeakMap是值，而WeakMap的弱引用只得是key。

## 总结

### Map和Object对比

|        | Key                              |
| ------ | -------------------------------- |
| Object | 无序，只能是字符串和Symbol       |
| Map    | 以插入的顺序返回键值，**任意值** |

### Map和Set方法

#### Map

**Map.prototype.clear()**： `clear()`方法会移除Map对象中的所有元素。

**Map.prototype.delete()**： `delete`方法用于移除 `Map` 对象中指定的元素。map.delete(key)

**Map.prototype.entries()**： `entries() ` 方法返回一个新的包含 `[key, value]` 对的 `Iterator` 对象，返回的迭代器的迭代顺序与 `Map` 对象的插入顺序相同。

```
const map = new Map();
map.set("name", "danceli");
map.set({name: "danceli"}, "12");
const iter = map.entries()
console.log(iter.next()) //{ value: ['name', 'danceli'] }
```

**Map.prototype.forEach**： 会对该方法按照插入顺序进行key，value的遍历

```
function logMapEle(value, key, map) {
    console.log(`map${key} => ${value}`);
}
new Map([["name", "li"], [NaN, "nan"]]).forEach(logMapEle)
```

**Map.prototype.get** ` get()`： 方法返回某个 `Map` 对象中的一个指定元素。

**Map.prototype.has(key)**： 用来检测是否存在指定元素的键值

**Map.prototype.keys()**： ` keys()` 返回一个引用的 `Iterator` 对象。它包含按照顺序插入 `Map` 对象中每个元素的key值。

**Map.prototype.values()** ：` values()` 返回一个引用的 `Iterator` 对象。它包含按照顺序插入 `Map` 对象中每个元素的value值。

**Map.prototype.set()**： ` set()` 方法为 `Map` 对象添加或更新一个指定了键（`key`）和值（`value`）的（新）键值对。

#### Set

**Set.prototype.size()**: 返回 Set 对象中的值的个数.

**Set.prototype.clear()**: 移除`Set`对象内的所有元素。

**Set.prototype.delete(value)**: 移除值为 `value` 的元素，并返回一个布尔值来表示是否移除成功。`Set.prototype.has(value)` 会在此之后返回 `false`。

**Set.prototype.add()**: `add()` 方法为`Set对象`添加值。

**Set.prototype.entries()**: 返回一个新的迭代器对象，该对象包含 `Set` 对象中的按插入顺序排列的所有元素的值的 `[value, value]` 数组。为了使这个方法和 `Map` 对象保持相似，每个值的键和值相等。

**Set.prototype.has(value)**: 返回一个布尔值，表示该值在 `Set` 中存在与否。

**Set.prototype.keys()** / **Set.prototype.values()**: 返回一个新的迭代器对象，该对象包含 `Set` 对象中的按插入顺序排列的所有元素的值，键和值在这里是一样的。

**Map.prototype.forEach**

> 总结：除了添加元素的方法，set是add(), map是set()，其余的方法都大概一致。





