---
layout:     post
title:      React Hooks
subtitle:  
date:       2024-01-15
author:     
header-img: 
catalog: true
tags:
    - React
typora-root-url: ..

---

## useMemo & useCallback

允许你在多次渲染中缓存值 / 函数的 React Hook。

使用它们有三个原因：

**1. 防止不必要的 effect**

如果一个值被 useEffect 依赖，那它可能需要被缓存，这样可以避免重复执行 effect。

```
const Component = () => {
  // 在 re-renders 之间缓存 a 的引用
  const a = useMemo(() => ({ test: 1 }), [])

  useEffect(() => {
    // 只有当 a 的值变化时，这里才会被触发
    doSomething()
  }, [a])
}
```

```
const Component = () => {
  // 在 re-renders 之间缓存 fetch 函数
  const fetch = useCallback(() => {
    console.log('fetch some data here')
  }, [])

  useEffect(() => {
    // 仅 fetch函数的值被改变时，这里才会被触发
    fetch()
  }, [fetch])
}
```

**2. 防止不必要的 re-render**

我们知道，组件 re-render 的原因有：

**a**. 当本身的 props 或 state 改变时

**b**. Context value 改变时，使用该值的组件会 re-render。

**c**. 当父组件重新渲染时，它所有的子组件都会 re-render，形成一条 re-render 链

所以下面这个例子，ProductPage 的 productId, referrer, theme 变化时，会导致 ShippingForm 重新渲染。

```
function ProductPage({ productId, referrer, theme }) {
  // 每当 theme 改变时，都会生成一个不同的函数
  function handleSubmit(orderDetails) {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }
  
  return (
    <div className={theme}>
      {/* 这将导致 ShippingForm props 永远都不会是相同的，并且每次它都会重新渲染 */}
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}
```

那么如何才能使得 ShippingForm 尽可能不被重复渲染呢？

1. 所有的 props 缓存：也就是给 handleSubmit 加上 useCallback
2. 组件本身必须缓存，否则 useCallback 就是无效使用（参照 **c**）

```
import { memo } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  // ...
});

function ProductPage({ productId, referrer, theme }) {
  // 在多次渲染中缓存函数
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]); // 只要这些依赖没有改变

  return (
    <div className={theme}>
      {/* ShippingForm 就会收到同样的 props 并且跳过重新渲染 */}
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}
```

**3. 跳过代价昂贵的重新计算**

在组件的顶层调用 `useMemo` 来缓存每次重新渲染都需要计算的结果

```
import { useMemo } from 'react';

function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  // ...
}
```



## useLayoutEffect

`useLayoutEffect` 是 `useEffect`的一个版本，在浏览器重新绘制屏幕之前触发。

- `useLayoutEffect` 内部的代码和所有计划的状态更新阻塞了浏览器重新绘制屏幕。如果过度使用，这会使你的应用程序变慢。如果可能的话，尽量选择 [`useEffect`](https://react.docschina.org/reference/react/useEffect)。
- `useLayoutEffect` 在 DOM 结构更新后，渲染前执行，在渲染时是同步执行。`useEffect` 函数会在组件渲染到屏幕之后执行。在渲染时是异步执行，并且要等到浏览器将所有变化渲染到屏幕后才会被执行。

为了更好理解，看下面代码，这段代码表示点击按钮会将 count 设置为 0，useEffect 里面会判断 count 为 0 时给 count 赋值一个随机数。

```
function App () {
	const [count, setCount] = useState(0)
	
	useEffect(() => {
		if (count === 0) {
			setCount(Math.random())
		}
	}, [count])
	
	//useLayoutEffect(() => {
		//if (count === 0) {
			//setCount(Math.random())
		//}
	//}, [count])
	
	return (
		<div className="app">
			<button onClick={() => setCount(0)}>{ count }</button>
		</div>
	)
}
```

对于 useEffect 来说，过程如下：

1. click setState 
2. vDom -> Dom，渲染
3. 执行 useEffect 回调 setState
4. dom 对比，渲染

而对于 useLayoutEffect，

1. click setState 
2. vDom -> Dom
3. 执行 useLayoutEffect 回调 setState
4. vDom -> Dom，渲染

由此可见，useLayoutEffect 它只渲染了一次，相当于做了对渲染操作做了一次防抖，改善页面闪烁的问题。



## useImperativeHandle

先看 forwardRef

#### forwardRef

父组件想要获取子组件内部的一个元素，就要向子组件传递 ref。ref 属性是 React 的特殊属性，不能直接在子组件上作为属性使用，需要使用 forwardRef。

```
function InputChild() {
	return <input defaultValue="dom" type="text" ref={}/>
}

function App() {
	const inputRef = useRef(null)
	
	const fucus = () => {
		inputRef.current?.focus()
	}
	
	return (
		<>
			<InputChild ref={inputRef} />
			{/* <input defaultValue="dom" type="text" ref={inputRef} /> */}
			<button onClick={focus}></button>
		</>
	)
}
```

![image-20240115154741635](/../img/postImage/image-20240115154741635.png)

```
const InputChild = forwardRef(() {
	return <input defaultValue="dom" type="text" ref={}/>
})

function App() {
	const inputRef = useRef(null)
	
	const fucus = () => {
		inputRef.current?.focus()
	}
	
	return (
		<>
			<InputChild ref={inputRef} />
			{/* <input defaultValue="dom" type="text" ref={inputRef} /> */}
			<button onClick={focus}></button>
		</>
	)
}
```



对于 useImperativeHandle，它的用途是父组件获取子组件的数据或调用子组件中的函数

`useImperativeHandle(ref, createHandle, dependencies?)` 

```
import { forwardRef, useRef, useImperativeHandle } from 'react';

const InputChild = forwardRef((props, ref) => {
  const inputRef = useRef(null)

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus()
      }
    }
  }, [])

  return <input {...props} ref={inputRef} />
})

function App() {
	const inputRef = useRef(null)
	
	return (
		<>
			<InputChild ref={inputRef} />
			{/* <input defaultValue="dom" type="text" ref={inputRef} /> */}
			<button onClick={inputRef.current.focus}></button>
		</>
	)
}
```



## useTransition

`useTransition` 更新过程是可中断的，渲染引擎不会长时间被阻塞，用户可以及时得到响应；不需要开发人员去做额外的考虑，整个优化过程由 React 和 浏览器完成。

使用如下：

```
const [isPending, startTransition] = useTransition();
```

isPending:

- true：表示 transition 更新还未被处理，此时我们可以显示一个中间状态来优化用户体验
- false: 表示 transition 更新被处理，此时可以显示实际需要的内容

startTransition：函数，包裹计算量大的逻辑，降低它的优先级，减少重复渲染次数

具体表现我们可以看下面这个例子：输入框绑定 handleChange，设置 inputVal，dom 渲染出 9999 个 div 内容为 inputVal

```
function App() {
	const [inputVal, setInputVal] = useState('')
	
	const handleChange = (e) => {
		setInputVal(e.target.value)
	}
	
	return (
		<div>
			<input onChange={handleChange} />
			{
				<div>
				{
					Array(9999).fill().map((n, i) => <div key={i}>{ inputVal }</div>)
				}
				</div>
			}
		</div>
	)
}
```

打开浏览器，可以看出，整个渲染是比较慢的，需要几秒钟之后才会显示出来。使用一下 useTransition 再看看

```
function App() {
	const [inputVal, setInputVal] = useState('')
	const [isPending, startTransition] = useTransition()
	
	const handleChange = (e) => {
		startTransition(() => {
			setInputVal(e.target.value)
		})
	}
	
	return (
		<div>
			<input onChange={handleChange} />
			<div>{ isPending ? 'loading...' : '加载完成' }</div>
			{
				<div>
				{
					Array(9999).fill().map((n, i) => <div key={i}>{ inputVal }</div>)
				}
				</div>
			}
		</div>
	)
}
```

再次看看，发现会快了很多！
