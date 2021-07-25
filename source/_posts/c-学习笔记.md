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

初始化：“给予对象初值”的过程。关键字`explicit`可以被用在自定义类型的构造函数前，可以阻止构造函数被用来执行隐式类型转换（但仍可进行显示类型转换）

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
4. **STL**：
