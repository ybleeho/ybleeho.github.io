---
title: Android-开发笔记-Kodein
date: 2019-10-03 20:57:28
tags:
---

> Kodein(Kotlin Dependency Injection)并不是一个整整的依赖注入框架，而是依赖检索容器。

## Kodein作用

* Lazily instantiate your dependencies when needed
* Stop caring about dependency initialization order
* Easily bind classes or interfaces to their instance or provider
* Easily debug your dependency bindings and recursions
* It proposes a very simple and readable declarative DSL

## 如何使用？

1.Install
```
compile 'org.kodein.di:kodein-di-generic-jvm:5.2.0'
compile 'org.kodein.di:kodein-di-framework-android-x:5.2.0'
```

2.Declare the dependency bindings in the Android Application, having it implements KodeinAware

```
class MyApp : Application(), KodeinAware {
	override val kodein = Kodein.lazy {
	    /* bindings */
	}
}
```

3.Retrieve the Kodein object with the colsestKodein function

```
class MyActivity : Activity(), KodeinAware {
    override val kodein by closestKodein()
    val ds: DataSource by instance()
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ds.connect()
        /* ... */
    }
}
```

## 绑定方式

1.Singleton 单例 传入 () → T

2.Provider 每次加载都生成新的实例，无参， 传入() -> T
```
val kodein = Kodein {
    bind<Die>() with provider { RandomDie(6) }
}
```

3.Factory 跟Provider相似，每次调用会返回一个新的实例，不同的是接受以定义类型的参数并返回绑定类型的对象 (A) -> T
```
val kodein = Kodein {
    bind<Die>() with factory { sides: Int -> RandomDie(sides) }
}
```
