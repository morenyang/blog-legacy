---
title: JS学习笔记:MV*都是些啥
date: 2018-05-13
tags:
foreword: 所以你觉得框架的意义是什么？
---

MV*是目前最常用的代码结构组织方式。在我们最传统的编码中，我们很容易就把数据、视图操作和业务逻辑杂糅在一起，导致代码繁杂、可阅读性降低，影响开发效率等问题。后来聪明的人们设计出了MVC、MVP、MVVM的代码组织结构，让我们能够以更高的开发效率进行开发和管理代码。今天这篇文章将简单的介绍这三种框架，以及介绍他们的相同和不同点。

## Model 和 View

在这三种模式中，M和V所指的内容是一致的—— M 代指 Model，即数据模型，用来处理程序中的数据逻辑； V 代指 View ，用来进行数据展示，可以理解成一个界面模板。

MV*模式最大的意义在于将 Model 和 View 的模块化。

## MVC

MVC 模型是架构最简单明了也是大家可能最熟悉的模型了。这个概念在1979年被提出。在这个模式中，Controller 像一个大管家，负责响应用户动作、更新 Model 中的数据、并让 View 获取到最新的数据。**（也就是说，视图获取数据、更新和绘制的逻辑都会在Controller中实现。）**

它的业务流程大致是： View 触发事件 -> Controller 收到事件并处理业务 -> 更新 Model 中的数据 -> (Controller) 依据 Model 更新 View 上的数据

在这个模式中：
- Controller 负责几乎所有的业务逻辑、数据逻辑和视图更新逻辑和细节
- 在 Controller 中 View 会接触和调用到 Model
- Model 不需要了解 Controller 和 View 中的细节 

## MVP

MVP 指的是 Model-View-Presenter (模型-视图-协调者)模型，它可以认为是MVC的一个改进版。在这个模式中，视图和模型完全解耦。View 中的每个组件都知道如何渲染从 Model 中得来的数据和响应用户的操作， Presenter 作为 Model 和 View 的中间人，负责控制 Model 修改并将最新的 Model 传递给 View 。**（换句话说，Presenter会将处理完的 Model 一股脑地扔给 View ，由 View 负责根据数据画视图。）**

它的业务流程大概是： View 触发事件并传递给 Presenter -> Presenter 更新 Model -> Presenter 将 Model 传递给 View -> View 根据 Model 渲染出界面 

在这个模式中：
- View 不会直接接触到业务中的 Model ，只会根据收到的 Model 渲染出界面
- Presenter 拥有 Model ，与 View 通过某个接口相互传递动作或数据。
- Model 和 View 相互解耦

MVP这个模式解决的问题主要是数据频繁更新时，更新界面麻烦的问题。它定义了 Presenter 和 View 之间的接口，让界面和业务逻辑可以更独立的进行开发。

## MVVM

MVVM 指的是 Model-View-ViewModel （模型-视图-视图模型）模型。在这个模式中， View 是无状态的，ViewModel 持有它的数据，并且根据这些数据实时调整和更新视图， View 和 ViewModel 直接交互。**（也可以理解成，ViewModel 实现了其_内部数据_和视图的绑定。）** 同时， ViewModel 也会监听 View 上触发的动作，并根据动作更新 Model 。

它的业务流程大致是： ViewModel 监听到 View 上的用户操作 -> ViewModel 根据操作更新数据 -> 如果有需要则 ViewModel 会更新 Model -> Model 更新后通知 ViewModel -> ViewModel 根据 Model 更新自己的数据 -> ViewModel 根据数据重新渲染 View

在这个模式中：
- View 不保存任何状态信息，也不需要了解 ViewModel 的实现细节
- View 渲染出的状态实际上与 ViewModel 中的某个类中的状态所对应
- ViewModel 持有 Model ，并且清楚业务逻辑

但是要注意的是，MVVM这个模型强调的不是 View 和 Model 的相互解耦，其实根据其实现方法也注定了他们两个不会有任何关联性，其注重的是 ViewModel 和 View 的数据绑定——也就是说它简化了业务与界面的依赖关系，同时也由命令式编程转换成了非命令式编程。

## 最后

讲真这类介绍抽象的开发模式的文章真的很难写，这里我也只是简单的介绍了一下MVC、MVP、MVVM三种模式的不同和他们面对不同问题的解决思路。在前端，目前其实还有更多组织模式——例如 Flux 的数据组织模式、Model-View-Update 的模式等等。其实他们都是为了更好的控制界面渲染、动作触发、数据分发而出现的，如果有兴趣的同学可以看看本文所参考的文章，或者自行 Google 一下。

### 参考
- [GUI 应用程序架构的十年变迁：MVC、MVP、MVVM、Unidirectional、Clean](https://zhuanlan.zhihu.com/p/26799645)
- [MVC和三层架构有何区别和联系？](https://www.zhihu.com/question/21851341/answer/20062573)
- [你对MVC、MVP、MVVM 三种组合模式分别有什么样的理解？](https://www.zhihu.com/question/20148405/answer/23813147)
- 《JavaScript 设计模式》 第38章、第39章、第40章