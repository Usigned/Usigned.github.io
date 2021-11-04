---
title: What the fuck is Callbacks?
tags: others
---

之前听说过回调函数(callbacks)，但是一直没理解是个啥？结果在programming language课上又看到了，这次准备彻底理清一下什么是他🐎的回调函数。

以下截取一段课程内对回调函数的描述的翻译：

> The next common idiom we consider is implementing a library that detects when “events” occur and informs clients that have previously “registered” their interest in hearing about events. Clients can register their interest by providing a “callback” — a function that gets called when the event occurs. 
>
> 一个library，当一个客户注册过的事件发生，其能检测到。客户可以注册“回调函数”，当事件发生时这个函数会被执行。

看起来`callbacks`的具体作用就是一个**触发器函数**的实现方式。

所谓的回调函数，并不是指函数本身形式有什么特别之处，而是指函数是被调用的方式的是“回调的”。

一般来说程序定义的函数都由程序本身调用，而“回调函数”由程序定义却传给外部当事件触发时调用（所以rpc也是回调？）。调用时，但由于闭包的存在，回调函数能使用程序定义的变量。
