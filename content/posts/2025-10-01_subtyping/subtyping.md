---
draft: false
date: 2025-10-01T22:36:07+08:00
title: Subtyping
slug:
description:
categories: []
tags: []
---

# Primitive Terms

>If you are expecting from me a furniture, then a table will work.

-[Inheritance vs. Subtyping (webinar #24)](https://www.youtube.com/live/DjrA7_Uymok?si=tjFGKnDtHzg5h2Z2&t=263)

# LSP

The **Liskov substitution principle** (**[LSP](https://en.wikipedia.org/wiki/Liskov_substitution_principle)**) is a particular definition of a [subtyping](https://en.wikipedia.org/wiki/Subtyping "Subtyping") relation, called [strong behavioral subtyping](https://en.wikipedia.org/wiki/Behavioral_subtyping "Behavioral subtyping").

在 **Liskov Substitution Principle (LSP)** 下，子类型关系不是由具体实现决定的，而是由 **约定的属性和契约** 决定的。在具体实现不变时，通过改变约定的属性和契约，就可以改变 subtyping 关系。

# In Code

到具体实现上，有些要注意的地方（传参、type-casting, etc.)

>Let $S$ and $T$ be two types. We say that $T$ is a _subtype_ of $S$ if _a piece of code written for variables of type $S$ can also safely be used on variables of type_ $T$ .

-[CS2030S Unit 2 Lecture Note](https://nus-cs2030s.github.io/2526-s1/notes/02-type.html#primitive-types-in-java)

这里的 "written for", "safely be used on" 定义太模糊了。不如 ChatGPT：

>Java 或任何面向对象语言里讨论 **subtyping（子类型关系）** 时，重点在于 **类型的抽象属性**，而不是具体的代码实现。换句话说，子类型关系关注的是：
>
>1. **值域**（可能的取值集合）
>
>2. **允许的操作**（能对该类型安全地执行哪些操作）
 >  
>3. **接口契约**（约定行为，方法签名和返回类型）
>
>它并不考虑具体方法内部是怎么实现的，也不看成员变量的实际存储方式或算法逻辑。
>
>比如：
>
>```Java
>class Animal { void eat() {} }
>class Dog extends Animal { void eat() {} }
>```
>
>从 **subtyping** 的角度看，Dog <: Animal，因为任何操作可以安全地应用于 Animal，也可以_安全地用于_ Dog。你不需要关心 Dog.eat() 具体做了什么，只要类型接口满足要求即可。
>
>可以把它理解成 **“类型是一种抽象契约，而不是实现细节”**

具体地说，

> 你为父类型写的代码可以在子类型的实例上运行，而**不会引起类型错误（从代码运行的角度）或违反类型契约（从抽象属性的角度）**。

# 总结

Subtyping relationship 主要还是一个抽象概念。Semantic(语义的) rather than syntactic(语句的).