---
title: Redux vs Mobx比较
date: 2019-06-07 09:39:23
tags:
---

### 1. Community
<img src="/images/redux-mobx.png"  width="800" height="400">
<img src="/images/stats.png">

### 2. Style

| Redux                  | Mobx                                   |
|------------------------|----------------------------------------|
| Functional Programming | Object-oriented + Reactive programming |
| 大量模版文件和代码     | 少量模版文件和代码                     |
| 需要处理一步的中间件如thunk,saga     | 不需要thunk                     |

### 3.Management

| Redux                          | Mobx                         |
|--------------------------------|------------------------------|
| 通常单一store                  | 多个store                    |
| 对象形式存储数据               | 可观察对象                   |
| 状态对象不可变只读             | 状态对象可变重写             |
| react-redux(Provider，connect) | mobx-react(Provider, inject) |
