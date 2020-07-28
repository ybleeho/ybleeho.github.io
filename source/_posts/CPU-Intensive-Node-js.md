---
title: CPU Intensive Node.js
date: 2019-10-05 23:04:21
tags:
---

> Node.js 单线程模式在运行CPU密集型运算时会出现阻塞这个单线程的问题

## Problem

```
app.get('/fb', function (req, res, next) {
    const {n} = req.query
    res.json({message:'ok', value: fib(n)});
})


app.get('/fb2', function (req, res, next) {
    const {n} = req.query
    console.log(req.body)
    return res.json({message:'ok', value: 111})
})


// 模拟CPU密集型运算
function fib(n) {
    if(n === 1)
        return 1
    if(n === 2)
        return 1
    else return fib(n-2) +fib(n-1)

}
```

当请求fb未返回之前，fb2的结果会因为fb请求的阻塞而不能立即返回结果


## Fork

Node.js 可以 fork一个child processes来进行CPU密集型运算而不去阻塞事件队列。fork用于执行js文件创建Node.js子进程。
而且fork方式创建的子进程与父进程之间建立了IPC通信管道，因此子进程和父进程之间可以通过send的方式发送消息。

```
//index.js

const {fork} = require('child_process')

app.get('/fb', function (req, res, next) {
    const {n} = req.query
    console.log(req.body)
    const worker = fork('./worker')

    worker.on('message', ({ value }) => {
        res.json({message:'ok', value});
        worker.kill();
    });
    worker.send({n});
})

app.get('/fb2', function (req, res, next) {
    const {n} = req.query
    console.log(req.body)
    return res.json({message:'ok', value: 111})
})





// worker.js
const _ = require('lodash')
process.on('message', ({ n }) => {

    process.send({ value: fib(_.toInteger(n)) });
});

function fib(n) {
    if(n === 1)
        return 1
    if(n === 2)
        return 1
    else return fib(n-2) +fib(n-1)

}

```

* 注：fork方式创建的子进程与父进程是完全独立的，它拥有单独的内存，单独的V8实例，因此当请求很频繁时，并不推荐使用fork创建很多的Node.js子进程

## Kue
