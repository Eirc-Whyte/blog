---
title: Effective Cpp 学习笔记
date: 2021-07-23 23:18:26
tags: 
- c++
categories:
- 编程语言
---

> 学习程序语言根本大法是一回事，学习如何以某种语言设计并实现高效程序则是另一回事。这种说法对C++尤其适用。



<!--more-->

## Chapter 0 术语

**声明式**：告诉编译器某个东西的名称和类型，但略去细节。

```` c++
extern int x; // 对象声明
std::size_t numDigits(int number); // 函数声明
class Widget; // 类声明

template<typename T> 
class GraphNode; // 模板声明
````

每个函数的声明揭示其签名式(signature)，也是**参数**和**返回类型**

**定义式**：提供给编译器声明式遗漏的细节；

- for object：编译器为此对象分配内存的位置；
- for function | template：提供代码本体；
- for class | template：列出成员；

**初始化**：“给予对象初值”的过程。

关键字`explicit`可以被用在自定义类型的构造函数前，可以阻止构造函数被用来执行隐式类型转换（但仍可进行显示类型转换）

```c++
Class B{
public:
	explicit B(int x = 0, bool b = true);
}
// --------
void doSomething(B object);

doSomething(28); // ❌ 不可以进行隐式类型转换
doSomething(B(28)); // ✔ 可以显式类型转换
```

## Chapter 1 View C++ as a federation of languages

一开始，c++只是c加上一些面向对象的特征，所以最初叫"C with Classes"

今天的C++已经是个多重泛型编程语言*(multiparadigm programming language)*

最简单的方法是将C++视为一个由相关语言组成的***Federation*而非单一语言**，在每个次语言中，各种守则都倾向于简单、直观易懂。

一共四个：

1. **C**：说到底C++仍是以C为基础的语言，但也反映出C语言的局限性：没有模板、异常、重载等，**面向过程**。
2. **Object-Oriented C++**：这部分是C with Class追求的。引入了类的概念、封装、继承、多态、虚函数（动态绑定）等。
3. **Template C++**：这部分是C++的泛型编程部分。由于templates威力强大，他们带来崭新的编程范型“模板元编程”
4. **STL**：template程序库

## Chapter 2 Prefer consts, enums, and inline to #defines

原则：以编译器替换预处理器，因为`#define`不被视为语言的一部分，因此它定义的内容不会进入符号表，因此错误信息中也不会有记录。

const/enums替换define中两种值得提及的情况：

1. **定义常量指针**

   `const char* const authorName = "Scott Meyers";`

   因为常量定义通常被放在头文件里，因此可能会被多个文件访问到，所以有必要将指针（而不是指向的内容）声明为const；若还要将指向的内容也定义为`const`，那么需要写两次`const`。

2. **定义`class`专属常量**

   为了将常量作用域限制于`class`内部，必须让他作为一个成员；

   而为了保证此常量最多只有一份**实体**，必须让成为一个`static`成员

```c++
class GamePlayer{
private:
	static const int NumTurns = 5; // 常量声明
	int scores[NumTurns]; // 使用常量
	...
};
```

​		在这里`NumTurns` 是一个声明而非定义式。有可能不被旧编译器支持，他们不允许static成员在其声明式上获得初值，`in-class` 初值设定只允许对整数常量进行（包括int、char、bool），那么可以：

```c++
class CostEstimate{
private:
	static const double FudgeFactor; // 声明位于头文件内
	...
};
const double
	CostEstimate::FudgeFator = 1.35; // 定义位于实现文件内
```

**enum hack**

如果你在class编译期间一定需要一个class常量值， 例如`GamePlayer`中编译器一定坚持必须在编译期间就知道数组的大小，可以采用**enum hack**方法：

```c++
class GamePlayer{
private:
	enum{ NumTurns = 5};
	int scores[NumTurns]; // 使用常量
	...
};
```

1. `enum hack`的行为某些方面比较像`#define`而不是`const`，例如取一个const的地址是合法的，但是取一个`enum`/`#define`的地址通常不合法
2. `enum hack`是模板元编程的基础技术（见C48）

inline替换define：

```c++
#define CALL_WITH_MAX(a,b) f((a) > (b) ? (a) : (b))

int a = 5, b = 0;
CALL_WITH_MAX(++a, b); // a = ?
CALL_WITH_MAX(++a, b+10); // a = ?
```

改成具有可预测行为和类型安全的`template inline`函数：

```c++
template<typename T>
inline void callWithMax(const &T a, const &T b){
	f(a > b ? a : b);
}
```

## Chapter 3 Use const whenever possible

`const` 出现在星号`*`左边时，表示**被指向的对象**是常量，如果出现在`*`右边，则说明**指针自身**是常量，如果出现在两边，则表示被指对象和指针都是常量；

所以下面两种写法是一样的：

```c++
void f1(const Widget* pw);
void f2(Widget const * pw);
```

const成员函数，让函数返回一个常量值，可以有效避免诸如`if( (a*b) = c )` 这种错误（编译器报错）

non-const成员调用const成员函数并转型可以避免代码重复

## Chapter 4 Make sure that objects are initialized before they're used

- 内置类型：手动完成：

```
int x = 0;
const char* text = "A C-style string";

double d;
cin >> d;
```

- 非内置类型：构造函数——确保每一个构造函数都将对象的每一个成员初始化。注意，C++规定，对象的成员变量的初始化动作发生在**进入构造函数本体之前**，即**初始化列表**中；
- 父类总是先于子类被初始化；class成员变量以其**声明次序**被初始化（即使他们在初始化列表中的位置可能不同）
- **non-local static对象的初始化：**

函数内的static对象称为local static对象，其他（包括global、定义在namespace中、classes中、file作用域内被声明为static的对象）称为non-local static对象。

如果一个编译文件里的某个non-local static对象初始化动作使用了另一个文件中的non-local static对象，此时他用到的这个对象可能尚未被初始化。

解决方法：将non-local static对象放进它的专属函数中，也就是**将non-local static替换为local static对象**，因为C++保证：**函数内的local static对象会在“该函数被调用期间，首次遇上该对象的定义式”时被初始化。**这也是单例模式（Singleton）的一个常见实现手法。

## Chapter 5 Know what functions C++ silently writes and call

C++中一个空类默认包含：

- 空的构造函数
- copy构造函数
- non-virtual析构函数
- copy assignment操作符（=）

只有当这些函数被调用时，他们才会被编译器创建出来

```c++
Empty el; // 默认构造函数
Empty e2(e1); // copy构造函数
e2 = e1; // assignment操作符
// 析构函数
```

默认的copy行为是将对象的所有非static变量拷贝到目标对象。

## Chapter 6 Explicitly disallow the use of compiler-generated functions u do not want

- 阻止copy行为：**声明copy函数为private但不实现**
- 进一步：定义一个`Uncopyable`基类，并将有“阻止copy行为”需求的类作为它的private派生类

```c++
class Uncopyale{
	protected:
		Uncopyable(){}
		~Uncopyable(){}
	private:
		Uncopyable(const Uncopyable&);
		Uncopyable& opeartor= (const Uncopyable&);
};

class HomeForSale: private Uncopyalbe{
    ///...
};
```

## Chapter 7 Declare destructors virtual in polymorphic base classes

- 通过为基类声明virtual析构函数，来让**base class指针控制的derived对象**正确销毁。
- 当class不被用来作为base class时，不要用virtual析构函数。因为virtual导致**在class中增加vptr指针指向vtbl，导致占用空间增大**
- 只有当class内**至少含有一个virtual函数**，才为他添加virtual析构函数
- 任何**带多态性质的base class**，应该为其声明一个virtual析构函数

## Chapter 8  Prevent exception from leaving destructors

- 不要让异常跑出析构函数
- 可能会引发两个未被处理的异常同时存在，例如：

```c++
void doSomething(){
    vector<Widget> v; 
    ... 
    // 假设Widget析构会抛出异常，那么此时会有多个异常被抛出，导致不确定行为
}

```

- 两个方法处理该问题：
  - 在析构函数中处理异常，遇到异常就在catch中调用`abort()`结束程序，“抢先制不确定行为于死地”；
  - “吞下”异常；在catch中记录该次调用失败。后续可以交由用户在普通函数中进行处理。

## Chapter 9 Never call virtual functions during construction or destruction

- derived class对象的base class构造期间调用的virtual函数是base class中的virtual函数，因为此时derived class的成员变量还没有被构造；此时virtual被调用的实际行为并不符合virtual函数被期望的行为（实现多态），但是编译器不会报错。

- 相同的道理适用于析构函数，一旦derived class的析构函数开始执行，其对象内所有的成员变量**都应视为未定义值**。

- 解决方法：

  - 将要调用的函数声明为non-virtual；
  - 从derived class的构造列表中传值给base class的构造函数
  - 使用private static函数生成要传的值，比较可读，也保证了使用的变量在使用时已经被初始化完毕（因为仅能使用static变量）

  

## Chapter 10 Have `operator=` return a reference to *this

- 字面意思，令赋值操作符返回一个指向当前对象的引用

```c++
Widget& operator=(const Widget& ths){
	...
	return *this;
}
```

以实现类似于`x = y = z = 15` 的操作

## Chapter 11 Handle assignment to self in `operator=`

- 当出现`a[i] = a[j]`且 `i == j`，即自我赋值时，可能会出现不安全现象：

```c++
Widget& Widget::Operator= (const Widget& rhs){
	delete pb;
	pb = new Bitmap(*rhs.pb); // 这里，&rhs可能是this，此时pb已经被释放了
	return *this;
}
```

- 解决方法：

  - identity test 证同测试：`if(this == &rhs) return *this;` 缺点：引入了新的控制流分支，降低执行速度；
  - 另外一个思路是，通过“消除异常安全性”同时保证“自我赋值安全性”——在编写代码时就考虑到`&rhs`是否可能是`this`这个问题，从而正确安排代码顺序；
  - 采用**Chapter 29**提到的**copy and swap**技术：

  ```c++
  Widget& Widget::Operator= (const Widget rhs){ // 传入副本
  	swap(rhs); // 将副本与当前对象进行数据交换
  	return *this;
  }
  ```

## Chapter 12 Copy all part of an object

> 如果你声明自己的*copying*函数，意思就是告诉编译器你并不喜欢默认实现中的某些行为。编译器仿佛被冒犯似的，会以一种奇怪的方式回敬：当你的实现代码几乎必然出错时却不告诉你：)

- *copying*函数 == *copy*构造函数 + *copy assignment*操作符
- 当自定义*copying*函数时，如果缺少成员变量，编译器不会报错；因此注意要**复制所有local成员变量**
- 尤其是当编写derived class的*copying*函数时，必须也**复制其base class的成分（通过调回base class的*copying*函数）**
- 如果copy构造函数和copy assignment操作符有相近的代码，那么就可以从中抽出一个函数给二者调用。

