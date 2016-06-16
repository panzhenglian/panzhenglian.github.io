---
title: Android为什么要避免使用 get/set
date: 2016-06-05 19:44:21
tags: Android
---

> 原文链接：https://developer.android.com/training/articles/perf-tips.html#GettersSetters

	
在像C++这类语言时很常见的做法是使用getter方法​​（i = getCount将（）），而不是直接访问字段（i = mCount）。这是C++中一个很好的习惯，如C＃和​​Java等面向对象的语言也是如此，因为编译器通常可以内联访问，如果需要限制或调试现场访问，可以随时添加代码。

然而，这是在Android上一个坏主意。虚拟方法调用很昂贵，远远超过了实例字段查找。遵循共同的面向对象的编程实践，具有getter和setter的公共接口这是合理的，但在一个类中，你应该总是直接访问字段。

如果没有JIT(just in time compiler)，直接访问字段大约是使用getter的3倍。在JIT的情况下（直接访问字段效率更高），大约是getter的7倍。

需要注意的是，如果你使用ProGuard，直接访问你更将受益，因为ProGuard可以内联访问。


