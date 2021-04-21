---
title: Android-开发笔记-coroutines
date: 2019-12-02 16:09:18
tags:
---
### 概念

协程是一种并发设计模式，并基于其他语言的既定概念。在Android上，协程可帮助解决两个主要问题:
* 管理长时间运行的任务
* 提供main-safety，或从主线程安全地调用网络或磁盘操作。

```
suspend fun fetchDocs() {                      // Dispatchers.Main
    val result = get("developer.android.com")  // Dispatchers.Main
    show(result)                               // Dispatchers.Main
}

suspend fun get(url: String) =                 // Dispatchers.Main
    withContext(Dispatchers.IO) {              // Dispatchers.IO (main-safety block)
        /* perform network IO here */          // Dispatchers.IO (main-safety block)
    }                                          // Dispatchers.Main
}
```

### Dispatchers

为了指定协程应该在哪里运行，Kotlin提供了三个可以使用的Dispatchers

| Dispatchers.Main                                                       | Dispatchers.IO                                         | Dispatchers.Default                                   |
|------------------------------------------------------------------------|--------------------------------------------------------|-------------------------------------------------------|
| Main thread on Android, interact with the UI and perform light work    | Optimized for disk and network IO, off the main thread | Optimized for CPU intensive work, off the main thread |
| Calling suspend functions,      Call UI functions,   Updating LiveData | Database*,   Reading/writing files,   Networking**     | Sorting a list,   Parsing JSON,   DiffUtils           |


### coroutines VS RxJava

一个处理请求的例子
```
//RxJava
interface SampleService {
    @GET("/users")
    fun getTopUsers(): Deferred<List<User>>
}
fun load() {
    disposable +=
    service.getTopUsers()
    .subscribeOn(io())
    .observeOn(mainThread())
    .subscribe(
       //
    )
}
//coroutines
interface SampleService {
    @GET("/users")
    suspend fun getTopUsers(): List<User>
}
fun load() {
        viewModelScope.launch {
            try {
                val users = service.getTopUsers()
                //
                } catch (e: Exception) {}
        }
}
```

---

从代码风格来看coroutines的写法更加简洁更像是同步的有点像javascript的async/await味道。
有以下两种方式之一启动协程：
* launch启动一个新的协程，并且不会将结果返回给调用方。 可以使用发布启动任何被认为是“即发即弃”的工作。
* async启动一个新的协程，并允许使用await的suspend函数返回结果。

ViewModel包含了内置的viewModelScope。这提供了一种在ViewModel范围内启动协程的标准方法，如上代码。


### 扩展
