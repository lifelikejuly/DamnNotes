# Kotlin

## 标签
kotlin的Break与Continue标签
```
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return // 非局部直接返回到 foo() 的调用者
        print(it)
    }
    println("this point is unreachable")
}
12
```

```
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach lit@{
        if (it == 3) return@lit // 局部返回到该 lambda 表达式的调用者，即 forEach 循环
        print(it)
    }
    print(" done with explicit label")
}
1245 done with explicit label
```

```
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // 局部返回到该 lambda 表达式的调用者，即 forEach 循环
        print(it)
    }
    print(" done with implicit label")
}
1245 done with implicit label
```

```
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return  // 局部返回到匿名函数的调用者，即 forEach 循环
        print(value)
    })
    print(" done with anonymous function")
}
1245 done with anonymous function
```

```
fun foo() {
    run loop@{
        listOf(1, 2, 3, 4, 5).forEach {
            if (it == 3) return@loop // 从传入 run 的 lambda 表达式非局部返回
            print(it)
        }
    }
    print(" done with nested loop")
}
12 done with nested loop
```

## singleton
koltin创建单例特别简单，object就能创建单例对象,并且是线程安全的
```
object Resource {
    val name = "Name"
}
```
还有另外lazy的单例对象实现
```
class Singleton private constructor() {
    companion object {
        val instance: Singleton by lazy { Singleton() }
    }
}
```
两者略有不同，在单例开销较大时lazy加载更为合适，若内存占用小可以使用object

## IO
kotlin的读写操作和Java存在很大不同，并且在Java代码转Kotlin时经常会出现错误
```
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```


## 伴生对象
与 Java 或 C# 不同，在 Kotlin 中类没有静态方法。在大多数情况下，它建议简单地使用包级函数
类内部的对象声明可以用 companion 关键字标记。可以理解为Java中static静态表达的一种方式。


## 扩展
Kotlin 同 C# 与 Gosu 类似，能够扩展一个类的新功能而无需继承该类或使用像装饰者这样的任何类型的设计模式。 
可以扩展函数，扩展属性。扩展属性并没有实际将成员插入到类中
```
fun BaseActivity.showToast(cxt: Context,text: String,duration: Int = Toast.LENGTH_SHORT){
    Toast.makeText(cxt,text,duration).show()
}
fun BaseActivity.go(intent: Intent,requestCode: Int = -1){
    if(requestCode == -1){
        startActivity(intent)
    }else{
        startActivityForResult(intent,requestCode)
    }
}
val <T> List<T>.lastIndex: Int
    get() = size - 1
```
## 集合
### 类型别名
typealias

### 内联类
inline

### 委托
设计模式中有委托模式，可以说是继承另一种实现方式。koltin原生就支持委托设计模式,如以下例子直接通过对象 Base by b实现对委托的支持。
```
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val b = BaseImpl(10)
    Derived(b).print()
}
```

### 委托属性* 

## 其他

### 空安全

```
var a : Int ?= null //可空表达
var b : Int = 3 //非空表达
val len = str?.length ?: 0 //Elvis
val len = str!!.length //NPE表达
```

### 作用域
let、also、apply、run、with、takeIf、takeUnless


