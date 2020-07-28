---
title: Android-开发笔记-kotlin基础
date: 2019-08-23 21:57:49
tags:
---
# 未完待续。

## 1. 流程处理

### when
```
/* fizz buzz problem*/

fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fizz"
    i % 5 == 0 -> "Buzz"
    else -> "$i "
}

fun main() {
    for(i in 1..50)
        println(fizzBuzz(i))

    /* 从50到1 步长为2 */
    for(i in 50 downTo 1 step 2)
        println(fizzBuzz(i))
}
```

## 2. 类

### 自定义访问器
```
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
    get() = height == width
}

val isSquare = Rectangle(40, 40).isSquare
```

### 继承与扩展
```
/* 可重写成员函数*/
open class View {
    open fun click() = println("View clicked")
}
class Button: View() {
    override fun click() {
        println("Button Clicked")
    }
}
/* 不能重写扩展函数*/
fun View.showOff() = println("I am a view")
fun Button.showOff() = println("I am a Button")

fun main() {
    val view: View = Button()
    view.click()   /* Button Clicked */
    view.showOff() /* I am a view */
}
```

* Java类的默认方法是open, 而Kotlin是final, 在open类中仅open函数和override的函数可以在子类中重写,
当想在open类中override的方法防止被子类重写，可以将此方法标注为final, 来限制他被重写。
```
open class Clickable {
    open fun click() = println("clickable")
}

open class Button: Clickable() {
    final override fun click() {
        println("button clickable")
    }
}
```
* Kotlin中比Java多了一个internal修饰符，其作用是在模块中可见。Kotlin中的默认声明是public，而且在Kotlin中的
protected修饰符只能在类和他的子类中可见，不像java一样在同一个包中访问protected的成员。类的扩展函数不能访问类的
private和protected成员。

* 嵌套类和内部类在Java与Kotlin中的对应关系

| 类A在另一个类B中声明       | 在Java中       | 在Kotlin中    |
|----------------------------|----------------|---------------|
| 嵌套类（不存储外部引用）   | static class A | class A       |
| 内部类（存储外部类的引用） | class A        | inner class A |

* Inner类访问Outer类
```
class Outer {
    inner class Inner {
        fun getOuterReference(): Outer = this@Outer
    }
}
```
* 密封类的表达式-所有的直接子类必须嵌套在父类中, 关键字用sealed
```
sealed class Expr {
    class Num(val value: Int): Expr()
    class Sum(val left: Expr, val right: Expr): Expr()
}

fun eval(e: Expr): Int =
    when(e) {
        is Expr.Num -> e.value
        is Expr.Sum -> eval(e.right)  + eval(e.left)
    }

fun main() {
    val left = Expr.Num(5)
    val right = Expr.Num(3)
    val sum = Expr.Sum(left, right)
    println(eval(sum))  /* 8 */
}
```

* 继承父类即使他没有参数也必须要调用父类的狗仔方法，也就是在父类名称后面加空的括号，但是如果是实现
接口，不需要在他的名称后面加括号。


* 使用委托类 - by
```
class CountingTest<T>(
    val innerSet : MutableCollection<T> = HashSet<T>()
) : MutableCollection<T> by innerSet {
    var objectsAdded = 0
    override fun add(element: T): Boolean {
        objectsAdded++
        return innerSet.add(element)
    }

    override fun addAll(elements: Collection<T>): Boolean {
        objectsAdded += elements.size
        return innerSet.addAll(elements)
    }

}

fun main() {
    val cset = CountingTest<Int>()
    cset.addAll(listOf(1, 2, 3))
    println("${cset.objectsAdded} object awere added, ${cset.size}   remain")
}
```


* 对象声明是Kotlin中定义单例类的方法。
* 伴生对象代替了Java的静态方法和字段定义。


## 3. run, also, apply, let, with

```
inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    return receiver.block()
}
inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}
inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}
inline fun <T, R> T.let(block: (T) -> R): R {
    return block(this)
}
inline fun <T, R> T.run(block: T.() -> R): R {
    return block()
}
```
