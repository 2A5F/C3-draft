# 异步
使用 `await` 关键字或  
在一个异步值后面加上 `!` 来 `await`  

```scala
def some fun() Async[int] = async {
    await some()
    some()!
    return 1
}
```

`!` 实际上是后缀运算符

```scala
def [T] operator(Async.self!) inline fun() T = async {
    inline return await self
}
```