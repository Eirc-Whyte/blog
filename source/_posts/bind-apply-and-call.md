---
title: 'bind,apply and call'
date: 2021-03-09 19:54:24
tags: 
- Javascript
- ES6
categories:
- 工程开发
---

Apply与Call的用途几乎相同，用于明确this指向以及方法借用等场景；Bind用来指定this指向或Currying functions。

<!--more-->

## Bind（）

```javascript
function.bind(thisArg[, arg1[, arg2[, ...]]])
```

#### **`thisArg`**

调用绑定函数时作为 `this` 参数传递给目标函数的值。

如果使用[`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)运算符构造绑定函数，则忽略该值。当使用 `bind` 在 `setTimeout` 中创建一个函数（作为回调提供）时，作为 `thisArg` 传递的任何原始值都将转换为 `object`。如果 `bind` 函数的参数列表为空，或者`thisArg`是`null`或`undefined`，执行作用域的 `this` 将被视为新函数的 `thisArg`。

#### `arg1, arg2, ...`

当目标函数被调用时，被预置入绑定函数的参数列表中的参数。

`bind()` 方法创建一个**新的函数**，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

### 绑定函数

将对象的函数与对象绑定，防止在其他环境中（例如window）丢失原本对象。

或者实现`thisArg`对象对某个其他函数的**借用**。

```javascript
this.x = 9;    // 在浏览器中，this 指向全局的 "window" 对象
var module = {
  x: 81,
  getX: function() { return this.x; }
};

module.getX(); // 81

var retrieveX = module.getX;
retrieveX();
// 返回 9 - 因为函数是在全局作用域中调用的

// 创建一个新函数，把 'this' 绑定到 module 对象
// 新手可能会将全局变量 x 与 module 的属性 x 混淆
var boundGetX = retrieveX.bind(module);
boundGetX(); // 81
```

### 偏函数

`bind()` 的另一个最简单的用法是，利用thisArg后的参数arg1,arg2...使一个函数拥有若干**预设的初始参数**。

### Curry

Currying 是这么一种**机制**，它将一个接收**多个参数**的函数，拆分成多个接收**单个参数**的函数。可以视为一种特殊的偏函数。

例如将简单函数add柯里化：

```javascript
function add (a, b) {
  return a + b;
}
add(3, 4); // returns 7
// currying===>
function add (a) {
  return function (b) {
    return a + b;
  }
}
// bind ==>
var addCurry = add.bind(null,10);
add(12)
// 22
```

more over, 将一般化函数进行 Currying 化：

```javascript
function curry(f) {
  return function currify() {
    const args = Array.prototype.slice.call(arguments);
    return args.length >= f.length ?     // f.length是f的参数个数
      f.apply(null, args) :
      currify.bind(null, ...args)
  }
}
```

实用价值：

**函数的组合**

两个函数的组合`f(g(x))`

```javascript
var compose = function(f,g) {
  return function(x) {
    return f(g(x));
  };
};
```

通过上面的讨论我们知道，任意函数都可经过 Currying 化处理后变成多个只接收单个入参的函数。这就为函数的组合提供了基础。

```javascript
const compose = fn1 => fn2 => input => fn1(fn2(input));
const myFn = compose(f)(g);
```

更一般化的组合：

```javascript
const pipe = (...fns) => input => fns.reduce((mem,fn)=> fn(mem),input)

const double = x => x*2
const addOne = x => x + 1
const square = x => x*x
// 右倾管道
pipe(square, double, addOne)(2)
```

### Apply and Call

当我们使用 apply 或者 call 时, 传入的第一个参数为**目标函数**中 this 指向的对象。

apply 和 call 的用法几乎相同, 唯一的差别在于当函数需要传递多个变量时, apply 可以接受一个**数组**作为参数输入, call 则是接受一系列的**单独变量.**

除此外, 在 ES6 的箭头函数下, call 和 apply 均**失效**, 对于箭头函数来说:

- 函数体内的 this 对象, 就是定义时所在的对象, 而不是使用时所在的对象;
- 不可以当作构造函数, 也就是说不可以使用 new 命令, 否则会抛出一个错误;
- 不可以使用 arguments 对象, 该对象在函数体内不存在. 如果要用, 可以用 Rest 参数代替;
- 不可以使用 yield 命令, 因此箭头函数不能用作 Generator 函数;

既然提到箭头函数，那么——

### 箭头函数

引入箭头函数有两个方面的作用：更简短的函数并且不绑定`this`。

在箭头函数出现之前，每一个新函数根据它是被如何调用的来定义这个函数的this值：

- 如果是该函数是一个构造函数，this指针指向一个新的对象
- 在严格模式下的函数调用下，this指向undefined
- 如果是该函数是一个对象的方法，则它的this指针指向这个对象
- `setInterval`、`setTimeout`这类函数的`this`值默认都是`window`

箭头函数**不会创建自己的**`this`,它只会从自己的作用域链的上一层**继承**`this`。

由于**箭头函数没有自己的this**，对箭头函数实用call和apply均失效，但是**可以bind**

箭头函数不绑定arguments对象，因此如果要用arguments，使用剩余参数`(...args)`是更好的选择

没有prototype、不能使用new和yield，但是可以使用闭包。

### Ployfill

bind实现方法：

```javascript
// Does not work with `new (funcA.bind(thisArg, args))`
if (!Function.prototype.bind) (function(){
  var slice = Array.prototype.slice;
  Function.prototype.bind = function() {
    var thatFunc = this, thatArg = arguments[0];
    var args = slice.call(arguments, 1);
    if (typeof thatFunc !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - ' +
             'what is trying to be bound is not callable');
    }
    return function(){
      var funcArgs = args.concat(slice.call(arguments))
      return thatFunc.apply(thatArg, funcArgs);
    };
  };
})();
```

apply实现方法：

```javascript
Function.prototype.myApply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window
  context.fn = this
  let result
  // 处理参数和 call 有区别
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}
```

