大致的看了下相关介绍，主要可以参考以下两个链接学习，

[ReactiveX/RxJava文档中文版](https://mcxiaoke.gitbooks.io/rxdocs/content/Observables.html)
本文介绍ReactiveX标准规定的一些结构及操作，先把这些概念以及模型读懂之后，再使用库就比较方便。

[RxJava2-Android-Samples](https://github.com/amitshekhariitbhu/RxJava2-Android-Samples) 基于Android的程序示例

暂未深入学习，只了解基本的概念，原因有二：  
1. 不太明确工作中哪些场景需要使用这种响应式编程，  
2. 此外，一个jar包就大于1M，对一个Android apk来说，有点沉重。

## ReactiveX


ReactiveX是Reactive Extensions的缩写，一般简写为Rx。Rx是一个编程模型，目标是提供一致的编程接口，帮助开发者更方便的处理异步数据流


-- **什么是ReactiveX**

微软给的定义是，Rx是一个函数库，让开发者可以利用可观察序列和LINQ风格查询操作符来编写异步和基于事件的程序，使用Rx，开发者可以用Observables表示异步数据流，用LINQ操作符查询异步数据流， 用Schedulers参数化异步数据流的并发处理，Rx可以这样定义：Rx = Observables + LINQ + Schedulers。

ReactiveX.io给的定义是，Rx是一个使用可观察数据流进行异步编程的编程接口，ReactiveX结合了观察者模式、迭代器模式和函数式编程的精华。

## 概述

简洁的异步

- 优点：

## 用法


RxJava 2 features several base classes you can discover operators on:

- io.reactivex.Flowable : 0..N flows, supporting Reactive-Streams and backpressure
- io.reactivex.Observable: 0..N flows, no backpressure
- io.reactivex.Single: a flow of exactly 1 item or an error
- io.reactivex.Completable: a flow without items but only a completion or error signal
- io.reactivex.Maybe: a flow with no items, exactly one item or an error

## 接口介绍

## 原理

RxJava 的异步实现，是通过一种扩展的观察者模式来实现的。



## 比较其他AsyncTask 和Handler