# CCC 草案

ccc，简写c3

[有道云](http://note.youdao.com/noteshare?id=8f503f3830ded4be4bdf7ef25f40fbc9)

为了记录版本变更，已放在 [https://github.com/2A5F/C3-draft/](https://github.com/2A5F/C3-draft/)

[https://github.com/2A5F/Chaoite](https://github.com/2A5F/Chaoite)

### 基础规定
- 采用胖指针形式  
- 所有类型均有运行时实体，泛型也是  
- 尽量一切都是第一类的(first class)
- 使用引用计数
- 尽量静态分析声明周期
- 尽量只有使用了才有开销
- 强制要求 `;` 结尾

## 基础语法  
```csharp
print('hello world')
```
没有printf   
单引号为字符串  
分号是可选的  

- #### 变量定义
    ```cc
    int?! a
    int a = 1
    ```
    - ##### 变量定义自动推断类型
         ```csharp
         val a = 1
         ```
    - ##### 不可变变量
        变量默认不可变  
        不可变变量必须马上赋值  
        
        ```csharp
        int a = 1
        val a = 1
        ```
        - ###### 使用`lazy`块延迟初始化不可变变量  
            ```csharp
            int a = lazy {
                2 ^ 6
            }
            ```
    - ##### 可变变量
        可变变量需要在类型名后面加上 `!`  
        
        ```csharp
        int! a = 1
        val! a = 1
        ```
- #### 变量修改语句
    ```cc
    a = 2
    a++
    a += 1
    ```
- #### 常量
    常量是编译时决定的  
    一般不需要使用，不可变值会自动优化成常量  

    ```cc
    const a = 1
    ```
- #### 注释
    ```cc
    // 单行
    /* 多
    行*/
    ```
- #### 数字中间符
    ```ccc
    1_000_000
    ```
- #### 字符串模板
    ```dart
    '$some'
    '${some}'
    ```
- #### 所有字符串都是多行的  
    ```scala
    'asd
    123'
    ```

## 基础类型  
|c语言名|类型名|大小(字节)|字面量|
|-|-|-|-|
|_Bool|bool|1，1/8|true，false|
|char|i8|1|0i8|
|unsigned char|u8，char|1|c"x", c'x'，0u8|
|short|i16|2|0i16|
|int|i32，int|4|0，0i32|
|long|i64|8|0i64|
|long long|i128|16|0i128|
|unsigned short|u16|2|0u16|
|unsigned int|u32，uint|4|0u，0u32|
|unsigned long|u64|8|0u64|
|unsigned long long|u128|16|0u128|
|float|f32|4|0f，0f32|
|double|float，f64|8|0.0，0d，0f64|
|long double|f128|16|0f128|

### 抽象类型
抽象类型大小不可确定  

|c/c艹语言名|类型名|字面量|
|-|-|-|
|void|void|void|
|nullptr|null|null|
|~~N/A~~|num|0n, 0.0n|
|~~N/A~~|int|0|
|~~N/A~~|float|0.0, 0f|

### 高级类型
高级类型大小是变长的  

|c/c艹语言名|类型名|字面量|
|-|-|-|
|char*/string|string|'x', "x"|

### 指针和可空类型
|名称|c语言内|ccc内|
|-|-|-|
|指针|type \*name| type!*! name|
|常量的指针|const type \*name| type*! name|
|指针的常量|type \* const name| type!* name|
|常量的指针的常量|const type \* const name| type* name|
|可空|~~N/A~~|~~N/A~~|
|常量的可空|~~N/A~~|~~N/A~~|
|可空的常量|~~N/A~~|type?! name|
|常量的可空的常量|~~N/A~~| type? name|
**指针是不安全的，必须在`unsafe`块内使用**  
指针和可空类型的区别在于  
指针可以运算来改变地址  
而可空类型不能  
并且可空类型受rc托管而指针不受  

## 控制流
```csharp
val x = if (true) { 1 } 
    elif (false) { 2 }
    else { 3 }
    
for (x in some) { }

while (true) { break }
do { continue } while (false)

while({ return false }) {}
while({ val x = false }, { return x }) {}

while({ int i = 0 }, { i < 10 }) { 
    print(i) 
} finally { i++ }

while({ false }) {
    int inner = 0
} finally { inner++ }

while(false) {
    int inner = 0
} finally { inner++ }

while { } // inf loop

val iter = for(x in 0..10) ^ { yield x }
Arr[Arr] arr = []
val iter = for(x in arr) ^ { yield^ x }
val iter = while(false) ^ { yield 1 }

int^ some() { yield 1 }

val gent = for(x in 0..) int^bool {
    if(!yield x) break
}
for(x in gent, { x < 10 }) { }

^bool some() {
  if(yield) { } 
}

for await(x in aiter) { }

val x = case(1) {
    of 0 = '0'
    of 1 = { true }
    else { print('no') }
}

case(1)
of Int i = true
of Float f = false

void some() {
    return
}

label:for(x in 0..10) {
    for(x in 0..10) {
        continue:label
        break:label
    }
}

void some() {
    label:
    print('loop')
    goto:label
}

void some(){
    val x = 1
    { // block
        val x = 2
    }
}

// breakable block
val x = :{
    break 'some val'
}

void some(){
    val x = label:{
        :{
            break:label 'some val'
        }
    }
}
```
## 异常/抛出
c3 里异常也是一种控制流  
并且其实叫抛出  
因为并不一定抛的都是异常，可以是任何值  
类似于 `yield` 但是是跨层的  
`catch` 块可以在当前块作用域的任何位置定义  
`catch` 块必须在需要捕获的调用前面定义  
`catch` 块将会从下往上按顺序查找  
直到匹配到需要捕获的类型  
然后就会中断，不再匹配上面的 `catch` 块  
`catch` 是需要注册的  
在 `catch` 注册之前 它 不会进行捕获  
`catch` 注册表是一个单向链表结构  
每个有 `catch` 的块都会对 `catch` 注册表添加一个注册结构  
每个 `catch` 块本身就是一个注册结构  
每个有 `throw` 的块都会对当前最后链表项保持引用  
直到有 `throw` 的块结束或者闭包被释放  
多线程一样会使用此链表  
多线程会导致此链表产生分支  
多线程会导致有 `catch` 注册的块的闭包一直持续到线程结束  

#### 异常的执行顺序
```js
   |void a() {
5  |
6  |    throw '123'
8  |
   |}
   |void b() {
1  |
2  |    catch (any msg) {   
7  |
   |    }           
3  |
4  |    a()
9  |
10 |    catch (any msg) {
   |        // 不会执行
   |    }
11 |
   |}
0  |b()
```

```js
   |void a() {
7  |
8  |    throw '123'
10 |
   |}
   |void b() {
1  |
2  |    catch (any msg) {   
   |
   |    }           
3  |
4  |    catch (any msg) {
9  |    
   |    }
5  |
6  |   a()
11 |
   |}
0  |b()
```
### 抛出标注
默认会自动隐式标注抛出  
但如果想显式标注

```java
void some() throws string, int {
    throw '123'
    throw 123
}
```
### 抛出中断 
默认的抛出不会阻止下面的代码运行  
想要阻止需要使用 `return throw`  
并且标注 `throws break <Type>` 或者自动推导  

```java
void some() throws string, break int, int {
    throw '123'
    return throw 123
    throw 123   // 不会执行
}
```
`try` 关键字可以在调用可能会抛出的函数时使用  
`try` 将会一旦遇到抛出就停止下面的代码的运行  
并且向上抛  
具有抛出中断标注的函数必须使用 `try`  
`try` 会将所有抛出类型都转成 `break` 标注的   

```java
void some() {
    try a()
    // 不会执行
}
```
`try` 块可以让停止范围限制在块内而不是函数级  
`try` 块如果有返回值那么返回值将被转换成 `maybe` / `option` / `nullable` 类型 (名字待定)  

```java
void some() {
    try {
        a()
        // 不会执行
    }
    // 会执行
}
```
在 `try` 块后面使用 `with` 跟随 `catch` 来处理抛出时的默认返回值  

```java
void some() {
    val r = try {
        a()
    } with catch (string msg) { msg }
    
    val r = try {
        a()
    } with catch {
        of (string msg) { msg }
    }
}
```
## 函数
```csharp
int add(int a, int b) {
    return a + b
}
void main() {
    print(add(1, 2))
}
```
- #### 函数最后省略return
    ```csharp
    int add(int a, int b) {
        a + b
    }
    ```
- #### 类型自动推断
    ```csharp
    int add(val a, val b) {
        a + b
    }
    int add(a, b) {
        a + b
    }
    ```
    - ##### 返回值自动推导
        并且返回值不能是可变的  
        
        ```csharp
        val mul(int a, b) {
            a * b
        }
        ```
- #### 函数参数重载
    ```cc
    int add(int a, int b) {
        a + b
    }
    int add(int a, int b, int c) {
        a + b + c
    }
    float add(float a, float b) {
        a + b
    }
    ```
- #### 表达式函数简写
    ```csharp
    int add(a, b) a + b
    int add(a, b) = a + b
    ```
- #### 内链函数
    ```cc
    inline bool eq(a, b) a == b
    ```
- #### lambda
    ```csharp
    val l = x -> |{ x + 1 }
    l(1)
    
    val l2 = |{ it + 1 }
    l2(1)
    
    val l3 = (a, b)-> |{ a + b }
    
    val l4 = |{ a, b -> a + b }
    
    val a = int <-(int a, int b)-> |{ a + b } 
    
    val b = |{ int <- int a, int b -> a + b } 
    
    val c = int add(int a, int b) a + b
    ```
- #### 指定闭包捕获
    如果不指定那么默认自动判断捕获  

    ```csharp
    void some(){
        val a = 1
        val b = 1
        val c = |a, b|{ a + b }
    }
    void some() {
        val a = 1
        val b = 1
        val c|a, b|() { a + b }
    }
    ```
- #### 函数类型
    ```csharp
    int <-(int a, int b) add = |{ a + b }
    int <-(int a, int b)! add = |{ a + b }
    
    void some(int add(a, b)) {
        add(1, 2)
    }
    void some(int <-(a, b) add) {
        add(1, 2)
    }
    ```
- #### 部分调用
    ```csharp
    val add1 = add(1, ?)
    val add2 = add(?, 2)
    val more = some(1, 2, ?..3, 6)
    val type = some(1, ?int)
    ```
    - ##### 尾部函数部分调用   
    同个调用只能存在一个尾部函数调用  
    同时接受多个函数时候需要用 `?->?` 或 `()->?` 指定  
    
    ```csharp
    some((a, b)-> ?, 1, 2) |{
        a + b
    }
    
    some(?, 1, 2) |{ a, b ->
        a + b
    }
    
    some(?->?, ?, ?, 1, 2) |{
        a + b
    }
    ```
    - ##### 模板字符串的部分调用 
    ```cc
    val some() {
        return '$? - ${?} - ${1}'
    }
    some()
    // string <-[`a, `b](`a a, `b b)
    ```
    
## 内联函数
内联函数其实是卫生宏  

- `[]` 表示可选  

-  [内联返回值] `inline`[`(`<其他标注>`)`] [上下文返回值] <函数名>`(`[函数参数]`)` [async标注] <函数体>

内联函数会要求调用者具有与内联函数一样的上下文  
比如内联函数标注返回 `void` 那么调用者也必须返回 `void`  
不写的话就代表任何情况都可以，并且没有相关影响的代码    
作为类型时 `async标注` 也写在 `inline` 的括号内  

```cc
inline void some() { 
    return
}

void foo() {
    some()
}
```

展开成  

```cc
void foo() {
    return
}
```

使用 `inline return` 返回一个值  
```cc
int inline some() { 
    val x = 1
    inline return x * 2
} 
void foo() {
  val x = some()
}
```

展开成  

```cc
void foo() {
  val x_1 = 1
  val x = x_1 * 2
}
```

但并不是所有 `inline` 函数都会被展开  
比如作为变量传递的 `inline` 

```cc
Arr[`b] map(`b inline(loopbody) <-(`a) cb, Arr[`a] f) {
    for(x in f) {
        cb(x)
    }
}
map({
    break
}, [])
```

## 异步
使用 `await` 关键字或  
在一个异步值后面加上 `!` 来 `await`  

```csharp
Async[int] some() async {
    await some()
    some()!
    return 1
}
```

`!` 实际上是后缀运算符

```csharp
`T  inline operator [`T]Async.self! async {
    inline return await self
}
```

## 结构
```csharp
struct Vector3 {
    int x
    int y
    int z
}
val v = Vector3@{
    x = 1
    y = 2
    z = 3
}
```
- #### 构造函数 
    构造函数使用首字母大写的 `Self` 标注  
    
    ```cc
    struct Vector3 {
        int x, y, z
        Self(x, y, z) {
            self.x = x
            self.y = y
            self.z = z
        }
    }
    val v = Vector3(1, 2, 3)
    ```
    - ##### 构造函数语法糖
    ```csharp
    struct Vector3(x, y, z) {
        int x = x
        int y = y
        int z = z
    }
    ```
    - ##### 构造函数内成员定义
    ```csharp
    struct Vector3(self int x, self int y, self int z) {}
    struct Vector3 <-(int x, int y, int z) {}
    struct Vector3 {
        Self(self int x, self int y, self int z)
    }
    struct Vector3 {
        Self <-(int x, int y, int z)
    }
    ```
- #### 成员函数
    在结构内定义的成员和在外面定义的成员是等价的  
    `.` 在函数调用上实际是管道运算符  
    区别在于结构内定义可以省略一个自身参数  
    并且 c3 使用 `self` 引用 和 `Self` 类型 而不使用 `this`
    
    ```cc
    struct Some {
        int a
        int add(int v) {
            a + v
        }
    }
    int add(Some { a }, int v) { // no self
        a + v
    }
    int Some.add(int v) { // has self
        a + v
    }
    
    val s = Some@{ a = 1 }
    s.add(2)
    // 3
    ```
    - ##### 构造函数一样可以在外部  
    并且构造函数也可以返回其他值  
    结构内返回其他类型的构造函数不能进行构造  
    也没有 `self` 引用  
    
    ```csharp
    struct Some {
        int a
        float Self() 2.0
    }
    Some Some() = @{ a = 1 } // no self
    Some Some.Self() { a = 1 } // has self
    int Some() 1
    ```
- #### 分部定义
    对于结构的分部定义，只能在同个包并且同个模块内  

    ```csharp
    struct Some {
        int a
    }
    struct Some {
        int b
    }
    ```
- #### 析构函数
    ```cc
    struct Some {
        ~Self() { }
    }
    ```
    
#### 构造数据结构 
的几种方式  

```csharp
val v = Vector3@{
    x = 1
    y = 2
    z = 3
}
val v = Vector3(1, 2, 3)
Vector3 v = @{
    x = 1
    y = 2
    z = 3
}

Vector3 v = default
val v = default Vector3
```

#### lambda形式构造  
lambda形式时必须明确具有类型名  

```scala
val v = Vector3@|{
    x = 1
    self.y = 2
    print(self)
    self.z = 3
    // do anything
}
```

## 子范围限定
子范围只能选择超集内有的内容  
但构造函数可以随意增加  

```csharp
struct Superset(int a, int b) {
    int a = a
    int b = b
}
struct A from Superset { a }
struct B(int b) from Superset(0, b) { b }

A a = Superset@{ a=1 b=2 }
B b = B(1)

B c = a // error
```

## 枚举
```csharp
enum [`T]Maybe {
    Maybe(`T val) Some(val)
    of Some(`T val)
    of None
    Maybe[`R] Some(`R <-(`T v) fn) case (self) {
        of Some(v) { Some(fn(v)) }
        of None = None
    }
}
Maybe[`R] Maybe[`T].FlatMap(Maybe[`R] <-(`T v) fn) case (self) {
    of Some(v) { fn(v) }
    of None = None
}

Maybe some = Some(1)
val some = Maybe.Some(1)
val some = Maybe(1)

Some(some, |{ 1 }).FlatMap(|{ None[int] }).Some(v -> |{ v + 1 })

enum Some {
    of A {
        int a, b
    }
}
val x = Some.A@{ a = 1  b = 2 }
```

## 种
种是一种不能扩展的  
对结构实现的约束和提供   
对种的实现必须在结构上声明  
并且必须在同一个程序集内  

```csharp
kind SomeKind {
    int add(int o)
}
struct Some with SomeKind {
    int v
    int add(Some { v }, int o) {
        v + o
    }
}
```

#### 种默认实现
种可以有默认实现  
但如果结构同时实现了多个种具有同个成员  
那么忽略默认实现并要求结构必须实现  

```csharp
kind SomeKind {
    int v
    int add(Self { v }, int o) {
        v + o
    }
}
struct Some with SomeKind {}
```

##  泛型
泛型类型必须在类型前面加上``` ` ```  
泛型括号 `[]` 在左边是定义，在右边是实例  

```csharp
`T some(`T v) {
    return v
}
```
- #### 自动泛化
    ```csharp
    val add(a, b) {
        return a + b
    }
    ```
- #### 泛型参数
    ```csharp
    void [`T]some(`T v) {}
    ```
- #### 泛型注解
    ```csharp
    #[`T]
    void some(`T v) {}
    ```
- #### 泛型实际传递了类型值
    ```csharp
    #[`T]
    Type a() {
        return `T
    }
    
    ```
- #### 泛型约束
    ```csharp
    `T some(`T v) where `T( Eq[`T] ) {
        return v
    }
    
    `T [`T( Eq[`T] )]some(`T v) {
        return v
    }
    
    #[`T( Eq[`T] )]
    `T some(`T v) {
        return v
    }
    
    #[`A, `B] where `A(num), `A( Eq[`B] ) 
    bool some(`A a, `B b) {
        return Equals(a, b)
    }
    ```
- #### 第一类泛型 
    泛型可以被值携带传递  
    泛型本身也可以携带泛型  

    ```csharp
    `T <-[`T]() some() {
        `T [`T]foo() = default
        return foo
    }

    void some(void [`T]foo(`T v)) {
        foo(1)
        foo('a')
    }

    `T?! [`T]some
    some[int] = 1
    some[bool] = true

    `T [`T]all_type_default = default

    `A[`B[`C]] some() {}
    ```

## 返回值特化 
可以重载不同类型的返回值  
只能存在一个默认返回值类型  
其他返回值类型必须包裹在 `:[]` 内  
调用时在参数括号后附加 `:[]`   
或者在明确类型的时候自动推导    

```scala
TypeDefault some(){}
:[TypeA] some(){}
:[TypeB] some(){}

val d = some()
val a = some():[TypeA]
val b = some():[TypeB]

TypeA a = some()
TypeB b = some()
```

## 类
使用`class`定义一个类  
使用`inspace`定义一个实现空间  

类和种的区别是  
种是对结构的实现约束和提供  
而类是存在量化  
种不可扩展，类可扩展  

对函数的类  

```csharp
class [`A, `B]Eq {
    bool Equals(`A a, `B b)
}
inpsace Eq[int, int] {
    bool Equals(int a, int b) {
        return a == b
    }
}
Equals(1, 2)
1.Equals(2)
```
对泛型实现  

```csharp
class [`T]Eq {
    bool Equals(`T a, `T b)
}
[`A]inspace Eq[Arr[`A]] where `A( Eq[`A] ) {
    bool Equals(Arr[`A] a, Arr[`A] b) {
        if(a.length != b.length) return false
        for(val i in a) {
          if(!Equals(a(i), b(i))) return false  
        }
        return true
    }
}
```
一次性实现多个  

```csharp
inspace Eq[int, int], Eq[float, float] {
    bool Equals(int a, int b) {
        return a == b
    }
    bool Equals(float a, float b) {
        return a == b
    }
}
```

#### 类依赖
类依赖会要求实现这个类之前必须实现依赖的类  

```csharp
class [`T]Some( Eq[`T] ) { 
    void some(`T t){}
}
```

#### 实现与现存定义重复
同时存在类内实现和独立定义  
并且签名完全重复时  
优先使用独立定义  
如果需要使用类内实现  
可以用`::`在目标名字后面加上路径  
或者使用`using`块   

```csharp
class [`T]Some {
    void some(`T v)
}
inspace Some[int] {
    void some(int v) {}
}
void some(int v) {} // 优先使用

some(1)             // 使用独立定义
some::Some[int](1)  // 使用 inspace

using Some[int], Other[xxx] {   // 使用 using 块
    some(1)
}

void foo() using Some[int] {    // 函数级 using 块
    some(1)
}
```
#### 多个类共用一个实现
```csharp
class A[`T] {
    void some(`T v)
}
class B[`T] {
    void some(`T v)
}
inspace A[int], B[int] {
    void some(int v) {}
}
// some(1) == some::A[int](1) == some::B[int](1)
```
# 堆栈分配
|互斥组|情况(优先级大小↓)|分配|
|-|-|-|
|2|`new` 标注|堆|
|1|基础类型|栈|
|1, 3|无法确定大小，装箱，多态，指针，引用， 闭包|堆|
|2, 3|`stackalloc` 标注|栈|
||大小过大的|堆|
||具有复制传递种|栈|
|2|其他 或 `auto` 标注|堆|
*具有无法确定大小那一条的类型不能标注 `stackalloc`*
```csharp
new val x = 1 // 堆
stackalloc val v = 'a' //栈
```
# 模块
没有命名空间，只有模块  
不在文件头写的话，整个文件都是独立的模块  
而不会并入全局模块  
*同一个父级模块内不能有除了函数外的模块成员名和模块名重名*  
*也就是说函数可以和模块合并*
```c++
module path.to.Name // 文件头

export int a = 1 // 

module path.to.Name { // 局部模块
    export module sub.Name {
        
    }
}
```
```c++
using path.to.Name.a, path.to.Name.b // 全文件引用

using path.to.Name import a, b // 导入部分内容

using path.to.Name as NewName import a as b, c // 创建别名

using path.to.Name { // 局部引用
    using path.to.Name import a, b  { 
    
    }
}
```
```csharp
void some() using path.to.Name {
    // 函数级局部引用
}


using './path/to/name.c3' 
// 按文件路径引入，只能引入没有在文件头定义的模块

using './path/to/name.h3' 
// 引入定义文件

using link './path/to/name.lib'
using link './path/to/name.dll'
// 指定链接

using './path/to/name.c3lib'
// 引入 c3 库

using './path/to/name.h' 
// 引入 c 头文件
// 目前没有支持 c艹 的打算
```
在名字后面用`::`指定模块路径
```cc
Some::path.to.Name // 不引入直接指定模块
```
```cc
export path.to.Name // 重新导出
export path.to.Name import a, b // 重新导出部分
export path.to.Name import a as c, b // 重新导出部分并重命名
export using path.to.Name // 引入的同时重新导出
```
# 类型别名
在`()`括号内描述需要建立别名的类型或者类型运算  

```typescript
type A(B)
```
# 类型运算
- or
  
    ```typescript
    type s(a | b) // 或运算，满足a或b任意一个即可
    ```
- and
  
    ```typescript
    type s(a & b) // 与运算，要求满足a和b都有的部分
    ```
    ![and](https://upload.wikimedia.org/wikipedia/commons/9/99/Venn0001.svg)
- add
  
    ```typescript
    type s(a + b) // 和运算，要求满足a和b的所有内容
    ```
    ![add](https://upload.wikimedia.org/wikipedia/commons/3/30/Venn0111.svg)
- sub

    ```typescript
    type s(a - b) // 减运算，从a中排除掉b的部分
    ```
    ![sub](https://upload.wikimedia.org/wikipedia/commons/5/5a/Venn0010.svg)
- dif
  
    ```typescript
    type s(a ^ b) // 对称差集，要求a和b之间不重叠的部分
    ```
    ![dif](https://upload.wikimedia.org/wikipedia/commons/4/46/Venn0110.svg)
- not
  
    ```typescript
    type s(!a) // 非运算，要求除了a以为的所有情况
    ```
    ![not](https://upload.wikimedia.org/wikipedia/commons/7/73/Venn10.svg)
### 类型运算级联
类型运算可以级联，既多个括号，一步步的缩小范围  
*这里的 `num` 是一个抽象类型，表示所有数字，不具有实际大小*  

```typescript
type x(num)(float - int)
```
# 前置类型
在泛型标注内的泛型名后带上括号来要求泛型前置依赖  
前置依赖可以是值也可以是其他类型  
前置依赖表示这个泛型必须是括号内类型运算结果的子类型  
*也可以用 ``` `() ``` 来表示一个匿名前置依赖*  

```csharp
`n [`n(num)]add(`n a, `n b) {
    return a + b
}
add[int](1, 2)
add[float](1.5, 3.14)
add[1](1, 1)
```
#### 字面量类型运算
对于数字字面量类型可以进行范围运算  

```typescript
type x(Int)(> 0)
type x(> 0) // 与上面等价，自动推导出 Int
type x(> 0.0) // 推导出 Float

type x(< 0)
type x(>= 0)
type x(<= 0)

type x(0..5)
type x(0<..5) // 不包括 0
type x(0..<5) // 不包括 5
```
#### 限定明确的数字类型
```typescript
type x(0i)
type x(0i32)
type x(0i64)

type x(0f)
type x(0f32)
type x(0f64)
```

# 运算符重载

- 中缀运算符

  ```csharp
  string operator (string a) + (string b) { }
  string operator string.self + (string b) { }
  string operator (string a) + string.self { }

  val x = 'a' + 'b'
  ```

- 前缀运算符  

  ```csharp
  int operator -(int some) { 0 - some }

  val x = -1
  ```

- 后缀运算符  
  重载时，后缀运算符的参数不能和中缀运算符的第一个参数重复  

  ```csharp
  Type inline operator (Type a)! { 
    inline return Mutable[a] 
  }
  Type inline operator (Type a)? { 
    inline return Nullable[a] 
  }
  ```

---

未完待续





---
## 一些例子

#### fib
```haskell
type nat(>= 0)
nat fib(0) 0
nat fib(1) 1
nat fib(n) fib(n - 1) + fib(n - 2)
```
#### vector
```csharp
struct [`n(1..4), `T(Num)]Vec case {
    of [1] {
        `T x
    }
    of [2] {
        `T x, y
    }
    of [3] {
        `T x, y, z
    }
    of [4] {
        `T x, y, z, w
    }
}

// Vec[3, int]
Vec[3] v = @{
    x = 1
    y = 2
    z = 3
}
```
#### array
```csharp
struct [`T, `l(> 0):len]Array {
    type Index(`l - 1)
    `(0..`l) length { get }
    `T self(`(0..Index) index) { get set }
}

val a = Array[string, 3]@{ 'a', 'b', 'c' }
a[0] // 'a'
a[5] // type error

val a = Array[string]@{ 'a', 'b', 'c' }
a[5] // runtime error

int len = 3
val a = Array[string, len]@{ 'a', 'b', 'c' }
a[5] // runtime error

// Array[any, 3]
val a == Array[:len = 3]@{ 1, 'b', false }
```
#### functor
```cc
class [`f]Functor {
    `f[`b] fmap(`f[`a], `b <-(`a))
}
```
