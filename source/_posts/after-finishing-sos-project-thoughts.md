---
title: thoughts about sos project
---

## Jwts vs Sessions

### 1.可扩展性
session是有状态的，token是无状态的，所以如果多台服务器上的应用，单点登录，基于token 的验证是有优势的。
构建但服务器应用，不需要RESTful API的，那么session也不错。
### 2.安全性
jwt最好使用https
### 3.RESTful
无状态，token更契合RESTful
### 4.性能
当数据少时没多大差别，但一旦claim多，开销比session大得多。所以session是有优势的。
（https://scotch.io/bar-talk/why-jwts-suck-as-session-tokens ）
If we store the ID in a cookie, the size is 6 bytes. If store the ID in a JWT ,the size has now inflated to 304 bytes.

### 5.实效性

session是可以手动删除的，这也是比token的优势。

解决办法：token存入数据库，可以实现token实效的功能，比如只能有一个用于登陆的状态，那么把token存入数据库，每次登陆更新token。


### 参考链接：
https://cnodejs.org/topic/5b568ec12860af042a217822

https://juejin.im/post/5a437441f265da43294e54c3

https://scotch.io/bar-talk/why-jwts-suck-as-session-tokens （主要描述token的弊端）

---

## async Make errors in callbacks throw globally

The wrapAsync function will wrap a function and return promise.
The callback will be executed as part of the promise’s .then() method.
This means that any error thrown from that method will be silenced, and lead to a “unhandled rejection”.
The only way to handle it is use try catch?
