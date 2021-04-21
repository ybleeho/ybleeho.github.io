---
title: 'Sequelize与async联合使用关于warning:a promise was created but was not returned from it的思考'
date: 2019-05-18 21:40:05
tags:
---

当Sequelize查询语句与async的waterfall，eachLimit等语句一起执行的时候发现一直有
```
(node:22810) Warning: a promise was created in a handler at /Users/hu/WebstormProjects/playground/server/server.js:126:55 but was not returned from it, see http://goo.gl/rRqMUw
    at Function.Promise.attempt.Promise.try (/Users/hu/WebstormProjects/playground/server/node_modules/bluebird/js/release/method.js:29:9)

```
这样的warning语句，当有eachLimit的循环多了以后，如执行cronjob的大量查询语句的时候
这些warning就变得更多了，所以决定查找这个warning的原因。

```
 async.waterfall([
        function (cb) {
             User.findOne({where: {id: 1}}).then(one=>cb(null, one)).catch(cb)
        },
        function (one, cb) {
             User.findOne({where: {id: 1}}).then(two=>cb(null ,two)).catch(cb)
        },
    ],(err,two)=>{
        if(err) {
            console.log('result error:',err)
        }
    })
```
这是一段产生此warning的一段demo代码，看似从代码上也找不出什么问题。
首先warning是在第一个find语句产生的，第二个find语句没有产生，然后我试着把第一个find后面的catch删除变成如下
```
 async.waterfall([
        function (cb) {
             User.findOne({where: {id: 1}}).then(one=>cb(null, one))
        },
        function (one, cb) {
             User.findOne({where: {id: 1}}).then(two=>cb(null ,two)).catch(cb)
        },
    ],(err,two)=>{
        if(err) {
            console.log('result error:',err)
        }
    })
```
然后发现warning也消失了。但是虽然移除了warning但是对于查询语句不进行catch也不是办法。
那是不是async的bug呢？在查找了大量的issue之后也没发现什么大问题。。。。
那是不是sequelize查询语句的问题呢？然后试着把查询语句换成如下代码
```
var promise1 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve('resolve');
    }, 300);
});
async.waterfall([
    function (cb) {
        promise1.then(one=>cb(null, one)).catch(cb)
    },
    function (one, cb) {
        promise1.then(two=>cb(null ,two)).catch(cb)
    },
],(err,two)=>{
    if(err) {
        console.log('result error:',err)
    }

})
```
果然warning消失了，也就是说是sequelize查询语句的warning。然后在sequelize里面查阅相关的
issue找到了一些线索和启发
https://github.com/sequelize/sequelize/pull/4862#issuecomment-174735567
没想到在waterfall里面上一个查询语句仅仅只有一个cb是不够的，得手动告知bluebird我要返回的东西。
没有warning后的代码
```
 async.waterfall([
        function (cb) {
             User.findOne({where: {id: 1}}).then(one=>{cb(null, one);return null;}).catch(cb)
        },
        function (one, cb) {
             User.findOne({where: {id: 1}}).then(two=>cb(null ,two)).catch(cb)
        },
    ],(err,two)=>{
        if(err) {
            console.log('result error:',err)
        }
    })
```
