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

## Symbol

ES5的**对象属性名**是字符串，容易造成属性名的冲突。例如，想为其他人的对象添加新的方法时，新方法的名字可能与现有方法冲突。

Symbol的引入是为了解决这个问题，它是第七种数据类型，前六种是`undefined`,`null`,`Bollean`,`String`,`Number`,`Object`

### 定义

Symbol值通过`Symbol`函数生成。

`Symbol ` 函数可以接受一个字符串或者一个对象（此时调用该对象的toString函数）作为参数，或者空，作为描述（`Symbol.prototype.description`），用以**区分**Symbol的值。但是同参数的Symbol仍然是不相等的

```javascript
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();

s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2 // false
```

可以通过ES2019提供的属性`desription`返回描述字符串

```javascript
const sym = Symbol('foo');

sym.description // "foo"
```

### 使用

1. 由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为**标识符**，用于对象的属性名，就能保证不会出现同名的属性。

```javascript
let mySymbol = Symbol();

// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
let a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });
```

注意，Symbol 值作为对象属性名时，**不能用点运算符**。因为点运算符后面总是字符串，所以不会读取`mySymbol`作为标识名所指代的那个值，导致`a`的属性名实际上是一个字符串，而不是一个 Symbol 值。

2. Symbol 类型还可以用于定义一组常量，保证这组常量的值都是不相等的。

```javascript
const COLOR_RED    = Symbol();
const COLOR_GREEN  = Symbol();

function getComplement(color) {
  switch (color) {
    case COLOR_RED:
      return COLOR_GREEN;
    case COLOR_GREEN:
      return COLOR_RED;
    default:
      throw new Error('Undefined color');
    }
}
```

### 遍历

Symbol 作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。

但它不是私有属性，可以通过专门的`Object.getOwnPropertySymbols()`方法，返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

```javascript
const obj = {};
const foo = Symbol('foo');

obj[foo] = 'bar';

for (let i in obj) {
  console.log(i); // 无输出
}

Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [Symbol(foo)]
```

另一个新的 API，`Reflect.ownKeys()`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。

```javascript
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};

Reflect.ownKeys(obj)
//  ["enum", "nonEnum", Symbol(my_key)]
```

由于以 Symbol 值作为键名，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。

### 重复使用

有时，我们希望重新使用同一个 Symbol 值，`Symbol.for()`方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其注册到**全局**。

比如，如果你调用`Symbol.for("cat")`30 次，每次都会返回同一个 Symbol 值，但是调用`Symbol("cat")`30 次，会返回 30 个不同的 Symbol 值。

`Symbol.keyFor()`方法返回一个已登记的 Symbol 类型值的`key`。

```javascript
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"

let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined
```

`Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。所以上例第二个返回`undefined`

除了定义自己使用的 Symbol 值以外，ES6还有[11 个内置的 Symbol 值](https://es6.ruanyifeng.com/#docs/symbol#%E5%86%85%E7%BD%AE%E7%9A%84-Symbol-%E5%80%BC)

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

## Set & Map

### Set

ES6提供了**新的**数据结构Set

构造函数`Set()`可以接受空的参数列表，也可以接受一个数组作为参数。可用于数组去重

```JavaScript
// 数组去重
[...new Set(array)]
// 字符串去重
[...new Set('ababbc')].join('')
```

`Set`转成数组：`Array.from(new Set())`

**遍历**

Set 结构的实例有四个遍历方法，可以用于遍历成员。

- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员

前三者返回遍历器对象，可以用`for-of`遍历

`...`的内部实现是`for-of` 因此可以用于`Set`

取交并

```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// （a 相对于 b 的）差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

### WeakSet

与weakset的区别：

- 成员只能是对象，而不是其他值
- 对象只能是弱引用，即垃圾回收机制不考虑weakset对该对象的引用

因此，WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。

由于上面这个特点，WeakSet 的成员是不适合引用的，因为它会随时消失。另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。

这些特点同样适用于本章后面要介绍的 WeakMap 结构。

WeakSet **不能遍历**，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

### Map

js对象本质上是键值对的集合（hash结构），但只能用**字符串**当键，这给它的使用带来 了很大的限制。

为了解决这个问题，ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。是一种更完善的hash结构实现。

**构造函数**

构造函数接受任何**具有Iterator**、且成员为**双元素的数组**的数据结构作为参数。

也就是说，`set`和`map`都可以用来生成新的`map`

构造函数实际执行的是下面的操作：

```javascript
const items = [
  ['name', '张三'],
  ['title', 'Author']
];

const map = new Map();

items.forEach(
  ([key, value]) => map.set(key, value)
);
```

如果对一个键重复赋值，后面的值将覆盖前面的；

如果读取一个未知的键，则返回`undefined`

注意，只有对**同一个对象**的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```javascript
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
```

两个`['a']`是不同的数组实例，地址不一样。

由此可见，Map的键实际上跟**内存地址**是绑定的，这样解决了同名属性碰撞问题。

**遍历**

- `Map.prototype.keys()`：返回键名的遍历器。
- `Map.prototype.values()`：返回键值的遍历器。
- `Map.prototype.entries()`：返回所有成员的遍历器。
- `Map.prototype.forEach()`：遍历 Map 的所有成员。

需要特别注意的是，Map 的遍历顺序就是**插入顺序**

结合数组的`map`方法、`filter`方法，可以实现 Map 的遍历和过滤（Map 本身没有`map`和`filter`方法）。

```javascript
const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
    );
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```

此外，Map 还有一个`forEach`方法，与数组的`forEach`方法类似，也可以实现遍历。

```javascript
map.forEach(function(value, key, map) {
  console.log("Key: %s, Value: %s", key, value);
});
```

`forEach`方法还可以接受第二个参数，用来绑定`this`。

```javascript
const reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};

map.forEach(function(value, key, map) {
  this.report(key, value);
}, reporter);
```

上面代码中，`forEach`方法的回调函数的`this`，就指向`reporter`。

**map转换为对象**

```javascript
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}
```

**对象转换为map**

```javascript
let obj = {"a":1, "b":2};
let map = new Map(Object.entries(obj));
```

**map转换为json**

如果map的键名都是字符串：转换成对象json

```javascript
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}'
```

如果map的键名有非字符串：转换为数组json

```javascript
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}

let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'
```

### WeakMap

与WeakSet类似：

首先，`WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。

其次，`WeakMap`的键名所指向的对象，不计入垃圾回收机制。

`WeakMap`的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。请看下面的例子。

```javascript
const e1 = document.getElementById('foo');
const e2 = document.getElementById('bar');
const arr = [
  [e1, 'foo 元素'],
  [e2, 'bar 元素'],
];
```

上面代码中，`e1`和`e2`是两个对象，我们通过`arr`数组对这两个对象添加一些文字说明。这就形成了`arr`对`e1`和`e2`的引用。

一旦不再需要这两个对象，我们就必须手动删除这个引用，否则垃圾回收机制就不会释放`e1`和`e2`占用的内存。

```javascript
// 不需要 e1 和 e2 的时候
// 必须手动删除引用
arr [0] = null;
arr [1] = null;
```

上面这样的写法显然很不方便。一旦忘了写，就会造成内存泄露。

基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景就是上面的这个：在网页的 DOM 元素上添加数据，就可以使用`WeakMap`结构。当该 DOM 元素被清除，其所对应的`WeakMap`记录就会自动被移除。

与WeakSet相同，只用四个方法可用：`get()`、`set()`、`has()`、`delete()`

### WeakRef

上述WeakSet和WeakMap都是基于弱引用的数据结构，ES2021更进一步，提供了WeakRef对象，用于直接创建对象的弱引用。

WeakRef 实例对象有一个`deref()`方法，如果原始对象存在，该方法返回原始对象；如果原始对象已经被垃圾回收机制清除，该方法返回`undefined`。

弱引用对象的一大用处，就是作为缓存，未被清除时可以从缓存取值，一旦清除缓存就自动失效。

```javascript
function makeWeakCached(f) {
  const cache = new Map();
  return key => {
    const ref = cache.get(key);
    if (ref) {
      const cached = ref.deref();
      if (cached !== undefined) return cached;
    }

    const fresh = f(key);
    cache.set(key, new WeakRef(fresh));
    return fresh;
  };
}

const getImageCached = makeWeakCached(getImage);
```

上面示例中，`makeWeakCached()`用于建立一个缓存，缓存里面保存对原始文件的弱引用。

一旦使用`WeakRef()`创建了原始对象的弱引用，那么在本轮事件循环（event loop），原始对象肯定不会被清除，只会在后面的事件循环才会被清除。

