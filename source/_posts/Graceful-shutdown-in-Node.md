---
title: Graceful shutdown in Node
date: 2020-06-25 15:09:33
tags:
---

### Graceful shutdown

Before when you deploy a new version of your application with process manager or just want to shut down the server with <Ctrl>+C , you need some steps first. Stop acceping new requests, finish all ongoing requests, and clean up the resources it used, including db connections , file locks, redis and so on. Graceful shutdown means when all your requests to the server is respond and not any data processing work left.

### How do I shutdown

1. Handle process kill signal

2. Stop new requests form client

3. Close all data process

4. Exit from process

Fisrt, we need handle the signal to the application that wants to shutdown.  Common [signals]([https://nodejs.org/api/process.html](https://nodejs.org/api/process.html) have **SIGTERM**, **SIGINT** and so on. A **signal** is an asynchronous notification sent to a **process** or to a specific thread to notify an event that occurred. 'SIGINT' generated with <Ctrl>+C in the terminal.The 'SIGTERM' signal is a generic signal used to cause program termination.

```js
process.on('SIGTERM', () => {
  console.info('SIGTERM signal received.');
});
process.on('SIGINT', () => {
  console.info('SIGTERM signal received.');
});

//other events
process.on('exit', ()=> {});
process.on('uncaughtException', ()=> {});
process.on('unhandledRejection', ()=> {});
process.on('rejectionHandled',()=> {});
```

Second,  stop new requests from client. It can be done using [server.close]([https://nodejs.org/api/http.html?#http_server_close_callback](https://nodejs.org/api/http.html?#http_server_close_callback) function

```js
process.on('SIGTERM', () => {
  console.info('SIGTERM signal received.');
  server.close(() => {
    console.log('Http server closed.');
  });
});
```

Third, close all data process

```js
process.on('SIGTERM', () => {
  console.info('SIGTERM signal received.');
  server.close(() => {
    console.log('Http server closed.');
    mongoose.connection.close(false, () => {
      console.log('MongoDb connection closed.');
    });
  });
});
```

Last, close all data process.As we know NodeJS will exit when EventLoop queue is empty and nothing left to do. But sometimes your application can have more functions and will not exit automatically, in this point comes our last work to do.  We need to exit from process using process.exit function.

```js
process.on('SIGTERM', () => {
  console.info('SIGTERM signal received.');
  server.close(() => {
    console.log('Http server closed.');
    // boolean means [force], see in mongoose doc
    mongoose.connection.close(false, () => {
      console.log('MongoDb connection closed.');
      process.exit(0);
    });
  });
});
```



### Reference

[https://hackernoon.com/graceful-shutdown-in-nodejs-2f8f59d1c357](https://hackernoon.com/graceful-shutdown-in-nodejs-2f8f59d1c357)

[https://nodejs.org/api/process.html](https://nodejs.org/api/process.html)

[https://expressjs.com/en/advanced/healthcheck-graceful-shutdown.html](https://expressjs.com/en/advanced/healthcheck-graceful-shutdown.html)

[https://pm2.keymetrics.io/docs/usage/signals-clean-restart/](https://pm2.keymetrics.io/docs/usage/signals-clean-restart/)


