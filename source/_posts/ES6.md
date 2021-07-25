---
title: ES6学习笔记
date: 2021-02-22 17:00:51
tags:
- Javascript
- ES6
categories:
- 编程语言
---
参考阮一峰老师 [《ES6标准入门》](https://es6.ruanyifeng.com/#README)

<!--more-->

## ES6和ECMAScript2015

因为在2011年发布完5.1后，ECMA发现，6.0版本引入语法功能太多，而且很多组织和个人不断提交新功能，因此不可能在同一个版本里包括所有将要引入的功能。**常规的做法**是：不断发布小版本，6.1，6.2，6.3等等

**但是**标准制定者想要指定一个常规流程：任何人在任何时候，都可以提交新语法提案，标准委员会每个月开一次会，讨论哪些可以被接受；也就是说，版本升级每个月一次；每年6月份发布一个正式版本作为当年的正式版本，接下来就按照上述方法改动，直到下一年。因此，就不需要之前的版本号，直接用年号标记版本就可以了。

因此，ES6现在指的是**2015年后发布的所有JS版本**，包括ESMA2015，2016，2017等。

## let & const

ES6新增`let`命令，它的用法类似于`var`，但是所声明的变量，只在`let`命令所在的**代码块内**有效，没有变量提升。

> 变量提升：`var`命令会发生“变量提升”现象，即变量可以在声明之前使用，值为`undefined`。

#### 暂时性死区

> ES6 明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
>
> 总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就**已经存在**了，但是**不可获取**，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

#### 不允许重复声明

`let`不允许在相同作用域内，重复声明同一个变量。

#### 块级作用域

fix了ES5中只有全局和函数作用域而没有块级作用域带来的不合理场景。

例如：

```javascript
// 内层变量覆盖外层变量：
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = 'hello world';
  }
}

f(); // undefined
```

以及

```javascript
// 循环变量泄露为全局变量
var s = 'hello';

for (var i = 0; i < s.length; i++) {
  console.log(s[i]);
}

console.log(i); // 5	
```

ES6 的块级作用域**必须有大括号**，如果没有大括号，JavaScript 引擎就认为不存在块级作用域。

#### const

const保证的是：const变量指向内存中的数据不得改动。

因此对于简单类型有效，对于复杂类型（对象，数组）仅能保证指针不变。

```javascript
const foo = {}

foo.prop = 123;
// ok
foo = {};
// TypeError
```

可以用`Object.freeze()`将对象冻结，使其不可添加新属性。

要将属性也冻结，可以递归调用Object.freeze:

```javascript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

## 变量解构赋值

类似于Python的解构赋值

可以这样

```javascript
let [a, b, c] = [1, 2, 3]
```

这样

```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
```

以及这样

```javascript
let [head, ...tail] = [1, 2, 3, 4];
```

**前提：右边是可遍历结构（有Iterator）**；只要等号右边的值不是对象或数组，就先将其转为对象。（null/undefined不能转化为对象）

允许指定默认值：

```javascript
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```

注意，当且仅当右边变量**严格等于undefined**，默认值才生效。null不是undefined。

对象解构赋值时要求**属性名严格相等**

## Promise

> 1. 对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。
>
> 2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对`Promise`对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。
>
>    有了`Promise`对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，`Promise`对象提供统一的接口，使得控制异步操作更加容易。
>
>    `Promise`也有一些缺点。首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。第三，当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

## async & await

`async`函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖。

```javascript
const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
<==>
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

