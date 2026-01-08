---
draft: false
create_data: 2026-01-08 16:37
---
# 前言

我们在打包的时候，经常能够看见产物中有 `cjs`和 `esm`；而平时也能听到讲 `commonjs` 和 `es module`。

我们在使用 `vue`、`react` 这样的框架开发页面的时候，也会发现和写 Nodejs 的时候会有一些区别；有时候用 `import + export default`、有时候用 `module.exports + require`。这到底是为什么，他们有什么区别，以及为什么这样设计？

# 背景介绍

首先明确 `cjs` 就是 `commonjs`，`esm` 就是 `es module`；它们都是模块化的一种方案。

那么不禁要问：模块化解决什么问题呢？

在之前没有模块化的前端开发中，我们一般是这样组织代码。

```jsx
<html>
	<head>
		<script src="a.js" />
		<script src="b.js" />
	</head>
<html/>
```

这样产生的问题主要是：

1. 全局变量污染。在代码逻辑的膨胀下，`a` 与 `b` 的全局变量很难做到命名不冲突；而在多人合作开发中，就更显得捉襟见肘。
2. 无法很好地解决循环依赖的问题。若 `a.js` 引用了 `b.js` 中的 `fun1`，同时 `fun1` 又引用了 `a.js` 中的 `fun2` 。

比如以下代码：

```jsx
// a.js
function fun1() {
	console.log("this is fun1");
	fun2();
}

// b.js
function fun2() {
	console.log("this is fun2");
	fun1();
}

// index.html
<html>
	<head>
		<script src="a.js" />
		<script src="b.js" />
	</head>
<html/>
```

它的执行结果是什么呢？答案是：会报错， `fun2 is not defined`

我们都知道 js 是按顺序执行的，并且每个js文件的执行是同步的，在解析执行 `a.js` 的时候，`b.js` 还没执行，所以此时 `fun2`还没有定义。

- 这里有两个问题需要说明一下：
    
    ## JS 是同步执行的
    
    这句话并不完全准确，在`html5`中，`script`新增了 `async` 属性，可以支持 js 文件异步执行。
    
    ## 函数提升
    
    你可能会有这样的疑问：“函数提升就是为了解决循环引用的呀，为什么在这里`fun2`没有被定义呢？”
    
    对这个问题的解释是：提升的范围，是一段js或者一个js文件。也就是说如果 `fun1` 和 `fun2` 均定义在 a.js 中，那么是符合函数提升的，不会报错。
    
    ![Untitled](Untitled.png)
    
    但在这个例子中，fun1 与 fun2 在不同的js文件中。
    

## 问题

搞清楚了模块化的背景，那么我们就带着问题去看这两个解决方案：

- 怎么解决不污染全局变量的问题？
- 怎么解决依赖问题的？
- 它们的实现有什么区别吗？为什么？

# CommonJS

## 基本使用

commonjs 定义如下：

- 每个js文件都是一个独立的模块，可以导出需要向外部暴露的变量
- 模块中，内置了 `require`、`exports`、`module` 用来导入和导出。其中 `exports` 是 `module.export` 的引用。

使用 `module.exports` 导出。

```jsx
// user.js
module.exports = {
	name: "iimT",
	speak: () => {
		console.log('hello');
	}
};
// 或
exports.name = "iimT";
```

使用 `require`导入

```jsx
// main.js
const user = require("./user")

console.log(user); // { name: iimT, speak: native function }
```

## 模块化

在如何使用中已经能够看到，每个模块(*即文件，后续不再赘述*)是相互隔离的，他们之间不会存在变量污染。同时导出的值都会被收集到一起。

导出和导入是如何实现的呢？弄清楚这个问题，可以帮助我们理解它和 ES Module 的区别，和一些不符合预期的场景。

## 实现

以 [Nodejs 的实现](https://sourcegraph.com/github.com/nodejs/node@main/-/blob/lib/internal/modules/cjs/loader.js?L165)为例，先看看Module是如何定义的。

![Untitled](Untitled%201.png)

可以看到一个模块，有`id`、`path`、`loaded` 等属性。并且在`Module`上还定义了一个`wrap` 函数：

![Untitled](Untitled%202.png)

再看看`wrap`函数是如何实现的：

![Untitled](Untitled%203.png)

而这个`wrap`，最终被赋值给 `Module._compile`

![Untitled](Untitled%204.png)

到这里大概就明白了。Nodejs将模块内的代码，最终编译成了一个函数，比如上面的模块 `user.js` 简略后长这样：

```jsx
(function (exports, require, module, __filename, __dirname) {
	module.exports = {
		name: "iimT",
		speak: () => {
			console.log('hello');
		}
	}
})
```

而传入的 `exports = module.exports` ，`module` 是当前模块。那么这个 `_compile`什么时候被调用呢？

来看看是如何 `require`的，`require`函数的定义如下：

![Untitled](Untitled%205.png)

它返回了 `Module._load(id)`，再来看看 `_load` 的定义：

![Untitled](Untitled%206.png)

它 `new` 了一个 `Module`，然后调用了 `load()` ，再看看 `load`

![Untitled](Untitled%207.png)

它调用了 `_extension[extension]` ，然后将 `loaded` 设置为了 `true` ；这里的 `extension` 就是文件的后缀，也就是调用了 `_extension["js"]`

![Untitled](Untitled%208.png)

诶，看到上面的 `_compile` 了。至此，整个链条也理清楚了：

1. `require` 调用 `_load`
2. `_load` 调用 `load`
3. `load` 调用 `extension["js"]`
4. `extension["js"]` 调用 `_compile`，传入 js 文件内容
5. `_compile` ，调用 `wrap` ，返回了一个包装函数，将创建的 `Module` 的 `export`、`module.exports`、`require` 等通过函数参数的方式注入到模块中。

## 如何查找导入的文件

链条弄清楚了，再来看看，当 `require(id)`的时候，它是怎么找到这个 `id` 的。这里直接给结果

1. 先判断是否为内置模块，若是，直接返回该模块

![Untitled](Untitled%209.png)

1. 若 `id` 为 `./` 或 `../` 开头的，那么按照以下规则查找
    1. 根据相对路径，确定绝对路径 `P`
    2. 将 `P` 看做文件，查找 `p`、`p.js`、`p.json`、`p.node` 若查到直接返回
    3. 将 `P` 看做目录，查找目录下的 `package.json` 中的 `main`指向的文件、`index.js` 、`index.json`、`index.node`
2. 递归地向上查找 `node_modules`，直到根目录

## 依赖管理

那么循环依赖的问题时如何解决的呢？如果同一个模块，在其他多个模块中被导入，会执行几次？

这里可以看看 require 中的源码，可以发现引入了一个 `_cache`，并且在`_load` 函数中，会先设置 `_cache` 再 `load`。并且对于命中 cache 的，会优先返回 cache 的 module。

![Untitled](Untitled%2010.png)

首先可以回答，同一模块被多次导入的问题，一定会仅执行一次，因为其他时候会直接返回 cache。

然后再来看循环依赖的问题：

首先，在调用模块中的方法时，必须先导入模块，这保证了被调用模块一定被定义。

而在循环依赖的场景下，由于 `cache` 早于 `load` 的原因，不会循环导入与执行。比如：

A被导入，在A的 `load` 中，导入的 B 又去 `require A`，此时 A 的`cache`已经存在了；那么就会命中 A 的 `cache`，不会再次 `load A`。

## 动态加载

根据 `require` 的实现方式，可以知道 `require` 是一个运行时的操作，也就是在执行到 `require` 的时候去通过 `wrap` 函数导入。

那么我们也就可以在任何位置去 `require`，所以 `commonjs` 是支持动态导入的。

## 拷贝 or 引用

由于 wrap 函数和 cache 的存在，我们可以得出结论，导出的模块属性：

- 若属性为基本类型，那么导出的是值
- 若属性为引用，则导出的是引用。

也就是说，对于模块

```jsx
module.exports = {
	num: 100,
	obj: {
		name: "iimT"
	}
}
```

导出的 num 是一个值的拷贝；导出的 obj 是一个值的引用，即各个模块使用的 obj 指向同一个对象。

# ES Module

从 es6 开始，Javascript 实现了自己的模块化规范。使用关键字 `import` 来进行导入。

## 基本使用

```jsx
// a.js
export default function fun() {
	console.log("this is a.fun")
}
export const num = 1;
// index.js
import fun, { num } from 'a'

fun(); // this is a.fun
console.log(num) // 1
```

使用 `export default` 和 `export` 关键字进行导出，`import`进行导入，它们有较多组合，这里不在赘述。

## 模块化

首先esm的模块化是静态的，（这里先不讨论 `import()` 函数），即`import` 导入模块是在编译时进行的，被 import 的模块会被提升到代码的最上面执行。并且会提前进行分析，对于一个模块仅导入一次。

那么它是如何解决循环依赖的问题呢？

在编译时，被导入的模块已经被分析依赖，并将导出提升到最上层 。例如：

```jsx
// a.js
import b from 'b';

b.fun2();
export function fun1() {
	b.fun2()
}

// b.js
import a from 'a';

a.fun1();
export function fun2() {
	a.fun1()
}

// index.js
import a from 'a'
```

在执行时是这样的：

- index 导入 a
- a 中发现依赖b，导入b
- b 中发现依赖 a 已经被导入，执行 a.fun1()，此时fun1() 已经被定义
- 回到 a 执行 b.fun2()
- 回到 index

可以发现，esm解决循环依赖的思路其实和函数提升是一样的，先将定义提升到最顶部，然后再调用。

## 拷贝 or 引用

esm 是静态导入的，也就是这一段代码被放在最上面执行过，并且仅执行一次。

考虑这样的情况：

```jsx
// num.js
export let num = 1;
export function add() {
	num++;
}

// a.js
import { num } from "num"

// b.js
import { num } from "num"
```

a 与 b中的`num`，是同一个变量吗？答案是肯定的，也就是说，若 add 调用了，a 与 b 中取到的都是 2。

这与 commonjs 是不一样的，**commonjs 导出的基本类型是拷贝的**。

方便进行区分一下，概括来说，他们的区别相当于下面两段代码：

```jsx
// commonjs
// num.js
const moduleA = {
	num: 1
}
// a.js
const { num } = require("num")
// b.js
const { num } = require("num")

// esm
// num.js
let num = 1;

// a.js，num 已经被定义
console.log(num)
// b.js，num已经被定义
console.log(num)

```

其区别其实就是，commonjs中`num`是一个解构对象出来的值；而 esm 相当于在代码的最前面，定义好 `num`。

但是esm导出的值，都是 read-only 的，也就是说外部模块是不能直接修改模块内的值的。

## tree shaking

由于 esm 静态导入的实现，使得它可以在编译阶段去分析依赖，来判断哪些 module 是不需要的，最终在编译出的代码中不包含这些不需要的代码。

这就是 terr shaking

# 总结

## commonjs

- 运行时导入，可以动态导入
- 导入的基本类型是值的拷贝，导入的引用类型是引用。
- 通过cache和先缓存后load的方式，解决循环依赖的问题。

## esm

- 编译时导入，静态导入
- 导入的基本类型是引用，并且是只读的；导入的引用类型是引用
- 通过类似函数提升的方式，通过将依赖全部前置，和依赖分析去重，解决循环依赖的问题
- 由于静态导入，可以在编译时进行 tree shaking