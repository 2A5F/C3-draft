**内联其实是卫生宏**  

# 内联函数
- <定义头> `inline`[`(`<其他标注>`)`] [<内联返回类型>] <函数定义> [async标注] <函数体>

内联函数会要求调用者具有与内联函数一样的上下文  
比如内联函数标注返回 `void` 那么调用者也必须返回 `void`  
不写的话就代表任何情况都可以，并且没有相关影响的代码    
作为类型时 `async标注` 也写在 `inline` 的括号内  

```scala
def some inline(return) fun() = { 
    return
}

def foo fun() {
    some();
}
```

展开成  

```scala
def foo fun() {
    return
}
```

使用 `inline return` 返回一个值  
```scala
def some inline int fun() = { 
    def x = 1
    inline return x * 2
} 
def foo fun() = {
  def x = some()
}
```

展开成  

```scala
def foo fun() {
  def x_1 = 1
  def x = x_1 * 2
}
```

但并不是所有 `inline` 函数都会被展开  
比如作为变量传递的 `inline` 

```scala
def [A, B] map fun(cb A -> B inline(loopbody), f Arr[A]) Arr[B] = {
    for(x in f) {
        cb(x);
    }
}
map({
    break
}, [])
```