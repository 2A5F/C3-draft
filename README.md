# CCC 草案

ccc，简写c3

为了记录版本变更，已放在 [https://github.com/2A5F/C3-draft/](https://github.com/2A5F/C3-draft/)  

编译器 [https://github.com/2A5F/Chaoite](https://github.com/2A5F/Chaoite)  

[QQ群](https://jq.qq.com/?_wv=1027&k=5uYunY7)

### 基础规定
- 采用胖指针形式  
- 所有类型均有运行时实体，泛型也是  
- 尽量一切都是第一类的(first class)
- 使用引用计数
- 尽量静态分析声明周期
- 尽量只有使用了才有开销
- 强制要求 `;` 结尾
- 块内最后表达式可以不加 `;`

## 基础语法  
```csharp
print('hello world');
```
没有printf   
单引号为字符串  
分号是可选的  

- #### 变量定义
    ```cc
    mut a:[int?];
    val a:[int] = 1; // 有赋值语句时建议使用这种
    mut a int?;
    val a int = 1;
    ```

    - ##### 变量定义自动推断类型
         ```csharp
         val a = 1;
         ```
    - ##### 不可变变量
        变量默认不可变  
        不可变变量必须马上赋值  
        
        ```csharp
        val a int = 1;
        val a = 1;
        ```
        - ###### 使用`lazy`块延迟初始化不可变变量  
            ```cc
            val a:[int] = lazy {
                2 ^ 6
            };
            ```
    - ##### 可变变量
        ```cc
        mut a:[int] = 1;
        mut a = 1;
        ```
- #### 变量修改语句
    `let` 是语句，没有返回值  
    ```swift
    let a = 2;
    a++;
    let a + 1; // 其他语言是 a += 1
    ```
- #### 常量
    常量是编译时决定的  
    一般不需要使用，不可变值会自动优化成常量  

    ```cc
    const a = 1;
    ```
- #### 注释
    ```swift
    // 单行
    /* 多
    行*/
    /* /* 嵌套 */ */
    /** 文档 */
    ```
- #### 数字中间符
    ```kotlin
    1_000_000
    ```
- #### 字符串模板
    ```dart
    '${some}'
    ```
- #### 所有字符串都是多行的  
    ```c
    "asd
    123"
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
|~~N/A~~|null|null|
|nullptr|~~N/A~~|nullptr|

### 高级类型
高级类型大小是变长的  

|c/c艹语言名|类型名|字面量|
|-|-|-|
|char*/string|string|'x', "x"|
|~~N/A~~|Mutable[type]|mut type|
|~~N/A~~|Nullable[type]|type?|

### 指针和引用
|名称|c语言内|ccc内|
|-|-|-|
|指针|type \*name| mut (mut type)* name|
|常量的指针|const type \*name| mut type* name|
|指针的常量|type \* const name| (mut type)* name|
|常量的指针的常量|const type \* const name| type* name|
|引用|type &name| mut type& name|
|常量的引用|const type &name| type& name|

**指针是不安全的，必须在`unsafe`块内使用**  
指针和引用的区别在于  
指针可以运算来改变地址  
而引用不能  

### 可空类型

可空的 空为 `null`  
而指针的空位 `nullptr`  

`type?`  
`mut type?`  
`type?*`  
`type?&`  

## 函数
```csharp
fun add(a int, b int):[int] {
    return a + b;
}
fun main() {
    print(add! 1 2);
}
```
- #### 函数最后省略return
    ```cc
    fun add(a int, b int):[int] {
        a + b
    }
    ```
- #### 可变函数变量  
    ```cc
    fun mut add(a int, b int):[int];
    let add = { a + b }
    ```
- #### 类型自动推断
    ```csharp
    fun add(val a, mut b):[int] {
        a + b
    }
    fun add(a, b):[int] {
        a + b
    }
    ```
    - ##### 返回值自动推导
        并且返回值不能是可变的  
        
        ```csharp
        fun mul(a int, b):[auto] {
            a * b
        }
        ```
- #### 函数参数重载
    ```cc
    fun add(a int, b int):[int] {
        a + b
    }
    fun add(a int, b int, c int):[int] {
        a + b + c
    }
    fun add(a float, b float):[float] {
        a + b
    }
    ```
- #### 表达式函数简写
    ```csharp
    fun add(a, b):[int] => a + b;
    ```
- #### lambda
    ```csharp
    val l = x => { x + 1 };
    l 1;
    
    val l2 = { it + 1 };
    l2 1;
    
    val l3 = (a, b)=> { a + b };
    
    val l4 = { a, b => a + b };
    
    val a = (a int, b int):[int] => { a + b }; 
    
    val b = {(a int, b int):[int] => a + b }; 

    val b = { a int, b int -> int => a + b }; 
    
    val c = fun add(int a, int b):[int] => a + b;
    ```
- #### 指定闭包捕获
    如果不指定那么默认自动判断捕获  

    ```csharp
    fun some() {
        val a = 1;
        val b = 1;
        val c = |a, b|{ a + b };
    }
    fun some() {
        val a = 1;
        val b = 1;
        fun c|a, b|() { a + b };
    }
    ```
- #### 函数类型
    ```csharp
    val add:[(a int, b int):[int]] = { a + b };
    val add:[a int -> b int -> int] = { a + b };
    fun add(a int, b int) = { a + b };
    
    fun some(fun add(a, b):[int]) {
        add(1, 2);
    }
    fun some(add (a, b):[int]) {
        add(1, 2);
    }
    ```
- #### 部分调用
    ```csharp
    val add0 = add(1, ?);
    val add1 = add! 1;
    val add2 = add(?, 2);
    val add3 = add! ? 2;
    val more = some(1, 2, ?..3, 6);
    val type = some(1, ?int);
    ```
    - ##### 尾部函数部分调用   
    同个调用只能存在一个尾部函数调用  
    同时接受多个函数时候需要用 `?=>?` 或 `()=>?` 指定  
    
    ```csharp
    some((a, b)=>?, 1, 2) {
        a + b
    };
    
    some(?, 1, 2) { a, b ->
        a + b
    };
    
    some(?=>?, ?, ?, 1, 2) {
        a + b
    };
    ```
    - ##### 模板字符串的部分调用 
    ```cc
    fun some() {
        return '${?} - ${1}';
    }
    some();
    // [`a](a `a):[string]
    ```
    
## 内联函数
内联函数其实是卫生宏  

- `[]` 表示可选  

-  =`inline`[`(`<其他标注>`)`]`:`[`[`<内联返回类型>`]`] <函数定义> [async标注] <函数体>

内联函数会要求调用者具有与内联函数一样的上下文  
比如内联函数标注返回 `void` 那么调用者也必须返回 `void`  
不写的话就代表任何情况都可以，并且没有相关影响的代码    
作为类型时 `async标注` 也写在 `inline` 的括号内  

```cc
inline fun some() { 
    return
}

fun foo() {
    some();
}
```

展开成  

```cc
fun foo() {
    return
}
```

使用 `inline return` 返回一个值  
```cc
inline:[int] fun some() { 
    val x = 1;
    inline return x * 2;
} 
fun foo() {
  val x = some();
}
```

展开成  

```cc
fun foo() {
  val x_1 = 1;
  val x = x_1 * 2;
}
```

但并不是所有 `inline` 函数都会被展开  
比如作为变量传递的 `inline` 

```cc
fun map(cb (`a)[`b] inline(loopbody), f Arr[`a]):[Arr[`b]] {
    for(x in f) {
        cb! x;
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
fun some():[Async[int]] async {
    await some();
    some()!;
    return 1;
}
```

`!` 实际上是后缀运算符

```csharp
inline fun operator [`T]Async.self!():[`T] async {
    inline return await self;
}
```

## 结构
```csharp
record Vector3 {
    mut x int;
    mut y int;
    mut z int;
}
val v = Vector3@{
    val x = 1;
    val y = 2;
    val z = 3;
}
```
- #### 构造函数 
    构造函数使用首字母大写的 `Self` 标注  
    
    ```cc
    record Vector3 {
        val x int, y int, z int;
        fun Self(x, y, z) {
            self.x = x;
            self.y = y;
            self.z = z;
        }
    }
    val v = Vector3(1, 2, 3);
    ```
    - ##### 构造函数语法糖
    ```csharp
    record Vector3(x, y, z) {
        val x:[int] = x;
        val y:[int] = y;
        val z:[int] = z;
    }
    ```
    - ##### 构造函数内成员定义
    ```csharp
    record Vector3(self x iny, self y iny, self z iny) {}
    record Vector3 <-(x int, y int, z int) {}
    record Vector3 {
        fun Self(self x int, self y int, self z int);
    }
    record Vector3 {
        fun Self <-(x int, y int, z int);
    }
    ```
- #### 成员函数
    在结构内定义的成员和在外面定义的成员是等价的  
    `.` 在函数调用上实际是管道运算符  
    区别在于结构内定义可以省略一个自身参数  
    并且 c3 使用 `self` 引用 和 `Self` 类型 而不使用 `this`  

    ```cc
    record Some {
        mut a int;
        fun add(v int):[int] {
            a + v
        }
    }
    fun add({ a } Some, v int):[int] { // no self
        a + v
    }
    fun Some.add(vint):[int] { // has self
        a + v
    }
    
    val s = Some@{ a = 1 };
    s.add(2);
    // 3
    ```
    - ##### 构造函数一样可以在外部  
    并且构造函数也可以返回其他值  
    结构内返回其他类型的构造函数不能进行构造  
    也没有 `self` 引用  
    
    ```csharp
    record Some {
        mut a int;
        fun Self():[float] => 2.0;
    }
    fun Some():[Some] = @{ a = 1 }; // no self
    fun Some.Self():[Some] { a = 1 }; // has self
    fun Some():[int] => 1;
    ```
- #### 分部定义
    对于结构的分部定义，只能在同个包并且同个模块内  

    ```csharp
    record Some {
        val a int;
    }
    record Some {
        val b int;
    }
    ```
- #### 析构函数
    ```cc
    record Some {
        fun ~Self() { }
    }
    ```
    
#### 构造数据结构 
的几种方式  

```csharp
val v = Vector3@{
    x = 1;
    y = 2;
    z = 3;
}
val v = Vector3(1, 2, 3);
Vector3 v = @{
    x = 1;
    y = 2;
    z = 3;
}

Vector3 v = default;
val v = default Vector3;
```

#### lambda形式构造  
lambda形式时必须明确具有类型名  

```scala
val v = Vector3@=>{
    x = 1;
    self.y = 2;
    print(self);
    self.z = 3;
    // do anything
}
```

## 子范围限定
子范围只能选择超集内有的内容  
但构造函数可以随意增加  

```csharp
record Superset(a int, b int) {
    val a:[int] = a;
    val b:[int] = b;
}
record A from Superset { a }
record B(b int) from Superset(0, b) { b }

val a:[A] = Superset@{ a = 1; b = 2; };
val b:[B] = B(1);

val c:[B] = a; // error
```

## 枚举
```csharp
enum [`T]Maybe {
    of Some(val `T);
    of None;

    fun Self(val `T) Some(val);
    fun Some(fn (v `T):[`R]):[Maybe[`R]] => case (self) {
        of Some(v) { Some(fn(v)) }
        of None = None;
    };
}
fun Maybe[`T].FlatMap(fn (`T v):[Maybe[`R]]):[Maybe[`R]] => case (self) {
    of Some(v) { fn(v) }
    of None => None;
};

Maybe some = Some(1);
val some = Maybe.Some(1);
val some = Maybe(1);

Some(some, { 1 }).FlatMap{ None[int] }.Some(v => { v + 1 });

//////////////////////////////

enum Some {
    of A {
        val a int, b int;
    }
}
val x = Some.A@{ a = 1; b = 2 };
```

## 元组
元组同时也是优先级表示方式  
1个元素的元组会自动解构  
并且1个元素的具名元组可以自动构造  

使用 `@()` 来表示元组字面量  

```cc
val x = @(1, 2);
```

### 元组结构
```csharp
record some @(int, bool);

val x:[some] = @(1, true);

record foo @(int);

val x:[foo] = 1;

val y:[int] = foo@(1);
```

## 数组
使用 `@[]` 来表示数组字面量  

```cc
val x = @[1, 2, 3];
```

### 数组构造器

在构造函数使用`@[]`来表示数组构造器构造函数  

```cc
record some {
    fun Self@[args] {}
}
```

## 解构模式

在定义时使用相应的构造字面量来进行解构  

```cc
val @(x, y) = @(1, 2);
val @[a, ..tail] = @[1, 2, 3, 4, 5];
val @{some, foo as bar} = @{ some = 1; foo = 2 };
```

## 控制流
```csharp
val x = if (true) { 1 } 
    elif (false) { 2 }
    else { 3 };
    
for (x in some) { }
for (x, i in some) { } // i >= 0

val count = for(x, i in some) { } else { 
    /* 当所有项目遍历结束后调用, 可以返回值 */ 
    return i
};

while (true) { break }
do { continue } while (false)

while({ return false }) {}
while({ val x = false }, { return x }) {}

while({ int i = 0; }, { i < 10 }) { 
    print(i); 
} finally { i++ }

while({ false }) {
    int inner = 0;
} finally { inner++ }

while(false) {
    int inner = 0;
} finally { inner++ }

while { } // inf loop

val iter = for(x in 0..10) ^ { yield x };
val arr:[Arr[Arr]] = @[];
val iter = for(x in arr) ^ { yield^ x };
val iter = while(false) ^ { yield 1 };

int^ some() { yield 1 }

val gent = for(x in 0..) int^bool {
    if(!yield x) break;
}
for(x in gent, { x < 10 }) { }

^bool some() {
  if(yield) { } 
}

for await(x in aiter) { }

val x = case(1) {
    of 0 => '0';
    of 1 => { true }; // return function
    else { print('no') }
}

case(1)
of i:[int] => true;
of f:[float] => false;

fun some() {
    return
}

label:for(x in 0..10) {
    for(x in 0..10) {
        continue:label;
        break:label;
    }
}

fun some() {
    label:
    print('loop');
    goto:label;
}

fun some(){
    val x = 1;
    :{ // block
        val x = 2;
    }
}

val x = :{
    break 'some val';
}

fun some(){
    val x = label:{
        :{
            break:label 'some val';
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
   |fun a() {
5  |
6  |    throw '123';
8  |
   |}
   |fun b() {
1  |
2  |    catch (any msg) {   
7  |
   |    }           
3  |
4  |    a();
9  |
10 |    catch (any msg) {
   |        // 不会执行
   |    }
11 |
   |}
0  |b()
```

```js
   |fun a() {
7  |
8  |    throw '123';
10 |
   |}
   |fun b() {
1  |
2  |    catch (any msg) {   
   |
   |    }           
3  |
4  |    catch (any msg) {
9  |    
   |    }
5  |
6  |   a();
11 |
   |}
0  |b();
```
### 抛出标注
默认会自动隐式标注抛出  
但如果想显式标注

```java
fun some() throws(string, int) {
    throw '123';
    throw 123;
}
```
### 抛出中断 
默认的抛出不会阻止下面的代码运行  
想要阻止需要使用 `return throw`  
并且标注 `throws(break <Type>)` 或者自动推导  

```java
fun some() throws(string, break int, int) {
    throw '123';
    return throw 123;
    throw 123;   // 不会执行
}
```
`try` 关键字可以在调用可能会抛出的函数时使用  
`try` 将会一旦遇到抛出就停止下面的代码的运行  
并且向上抛  
具有抛出中断标注的函数必须使用 `try`  
`try` 会将所有抛出类型都转成 `break` 标注的   

```java
fun some() {
    try a();
    // 不会执行
}
```
`try` 块可以让停止范围限制在块内而不是函数级  
`try` 块如果有返回值那么返回值将被转换成 `maybe` / `option` / `nullable` 类型 (名字待定)  

```java
fun some() {
    try {
        a();
        // 不会执行
    }
    // 会执行
}
```
在 `try` 块后面使用 `with` 跟随 `catch` 来处理抛出时的默认返回值  

```java
void some() {
    val r = try {
        a();
    } with catch (msg:[string]) { msg }
    
    val r = try {
        a();
    } with catch {
        of (msg:[string]) { msg }
    }
}
```

## 性质
性质是一性质不能扩展的  
对结构实现的约束和提供   
对性质的实现必须在结构上声明  
并且必须在同一个程序集内  

```csharp
kind SomeKind {
    fun add(o int):[int];
}
record Some with SomeKind {
    val v int;
    val add(o int):[int] {
        v + o;
    }
}
```

#### 性质默认实现
性质可以有默认实现  
但如果结构同时实现了多个性质具有同个成员  
那么忽略默认实现并要求结构必须实现  

```csharp
kind SomeKind {
    val v int;
    fun add({ v } Self , o int):[int] {
        v + o
    }
}
record Some with SomeKind {}
```

##  泛型
泛型类型必须在类型前面加上``` ` ```  
泛型括号 `[]` 在左边是定义，在右边是实例  

```csharp
fun some(v `T):[`T] {
    return v;
}
```
- #### 自动泛化
    ```csharp
    fun add(a, b) {
        return a + b;
    }
    ```
- #### 泛型参数
    ```csharp
    fun [`T]some(v ` v) {}
    ```
- #### 泛型注解
    ```csharp
    #[`T]
    fun some(v `T) {}
    ```
- #### 泛型实际传递了类型值
    ```csharp
    #[`T]
    fun a():[type] {
        return `T;
    }
    
    ```
- #### 泛型约束
    ```csharp
    fun some(v `T):[`T] where(`T( Eq[`T] )) {
        return v
    }
    
    fun [`T( Eq[`T] )]some(v `T):[`T] {
        return v
    }
    
    #[`T( Eq[`T] )]
    fun some(v `T):[`T] {
        return v
    }
    
    #[`A, `B] where(`A(num), `A( Eq[`B] ))
    fun some(a `A, b `B):[bool] {
        return Equals(a, b);
    }
    ```
- #### 第一类泛型 
    泛型可以被值携带传递  
    泛型本身也可以携带泛型  

    ```csharp
    fun some():[[`T]():[`T]] {
        fun [`T]foo():[`T] = default
        return foo
    }

    fun some(foo [`T](`T v)[void]) {
        foo(1);
        foo('a');
    }

    mut [`T]some:[`T?];

    some[int] = 1;
    some[bool] = true;

    [`T]all_type_default = default;

    fun some():[`A[`B[`C]]] {};
    ```
- #### 匿名泛型
    用 ``` `数字``` 来表示匿名泛型  
    ```cc
    val [int]a:[`0] // 表示泛型约束为 int
    val [`T(int)]a:[`T] // 等价
    ```
## 泛型重叠
泛型类型处于重叠时  
具体取值是惰性的  
只有所有泛型参数都被填充时才会开始实际计算  

```scala
fun a(a int, b int):[int] {}
fun a(a int):[(b int):[string]] {}
fun a(a int, b string):[int] {}
fun a(a int):[int] {}

val b = a! 1;
// [ int * (int):[int] * (string):[int] * (int):[string] ]b

val i:[int] = b;
// i = b[int]
// fun a(a int):[int]

val c = b! 1;
// [int * string]c:[`0] = b[(int):[auto]] 1
// fun a(a int, b int):[int]
// fun a(a int):[(b int):[string]]

val d = b! 'a';
// d:[int] = b[(string):[int]] 'a'
// fun a(a int, b string):[int]
```

## 类
使用`class`定义一个类  
使用`inspace`定义一个实现空间  

类和性质的区别是  
性质是对结构的实现约束和提供  
而类是存在量化  
性质不可扩展，类可扩展  

对函数的类  

```csharp
class [`A, `B]Eq {
    fun Equals(a `A, b `B):[bool];
}
inpsace Eq[int, int] {
    fun Equals(a int, b int):[bool] {
        return a == b;
    }
}
Equals(1, 2);
1.Equals(2);
```
对泛型实现  

```csharp
class [`T]Eq {
    fun Equals(a `T, b `T):[bool];
}
[`A]inspace Eq[Arr[`A]] where(`A( Eq[`A] )) {
    fun Equals(a [`A], b Arr[`A]):[bool] {
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
    fun Equals(a int, b int):[bool] {
        return a == b
    }
    fun Equals(a float, b float):[bool] {
        return a == b
    }
}
```

#### 类依赖
类依赖会要求实现这个类之前必须实现依赖的类  

```csharp
class [`T]Some( Eq[`T] ) { 
    fun some(t `T);
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
    some(v `T);
}
inspace Some[int] {
    some(v int) {};
}
fun some(v int) {} // 优先使用

some(1);            // 使用独立定义
some::Some[int](1); // 使用 inspace

using(Some[int], Other[xxx]) {   // 使用 using 块
    some(1);
}

fun foo() using(Some[int]) {    // 函数级 using 块
    some(1);
}

fun [Some[`T]]bar(v `T) {      // 泛型模式匹配
    some(v);
}
```
#### 多个类共用一个实现
```csharp
class A[`T] {
    fun some(v `T);
}
class B[`T] {
    fun some(v `T);
}
inspace A[int], B[int] {
    fun some(v int) {}
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
||具有复制传递性质|栈|
|2|其他 或 `auto` 标注|堆|
*具有无法确定大小那一条的类型不能标注 `stackalloc`*
```csharp
new val x = 1; // 堆
stackalloc val v = 'a'; //栈
```
# 模块
没有命名空间，只有模块  
不在文件头写的话，整个文件都是独立的模块  
而不会并入全局模块  

*同一个父级模块内不能有除了函数外的模块成员名和模块名重名*  
*也就是说函数可以和模块合并*

普通模块只能包含定义  
单文件模块可以包含任意代码  

```c++
module path.to.Name; // 文件头

export val a = 1; // 

module path.to.Name { // 局部模块
    export module sub.Name {
        
    }
}
```
```c++
using path.to.Name.a, path.to.Name.b; // 全文件引用

using path.to.Name import a, b; // 导入部分内容

using path.to.Name as NewName import a as b, c; // 创建别名

using(path.to.Name) { // 局部引用
    using(path.to.Name) import a, b  { 
    
    }
}
```
```csharp
fun some() using(path.to.Name) {
    // 函数级局部引用
}


using './path/to/name.c3'; 
// 按文件路径引入，只能引入没有在文件头定义的模块

using './path/to/name.h3'; 
// 引入定义文件

using link './path/to/name.lib';
using link './path/to/name.dll';
// 指定链接

using './path/to/name.c3lib';
// 引入 c3 库

using './path/to/name.h'; 
// 引入 c 头文件
// 目前没有支持 c艹 的打算
```
在名字后面用`::`指定模块路径
```cc
Some::path.to.Name; // 不引入直接指定模块
```
```cc
export path.to.Name; // 重新导出
export path.to.Name import a, b; // 重新导出部分
export path.to.Name import a as c, b; // 重新导出部分并重命名
export using path.to.Name; // 引入的同时重新导出
```

## 单文件模块和构造模块  
```c++
// a.c3
module()
print('loaded')

// b.c3
using './a.c3'

// c.c3
using './a.c3'
using './b.c3'

// > loaded
```
```c++
module x() {
    module a.b.c() {}
}
```

# 类型别名
在`()`括号内描述需要建立别名的类型或者类型运算  

```typescript
type A(B);
```
# 类型运算
- 叠加态
  
    ```typescript
    type s(a * b); // 同时是a也是b
    ```
- or
  
    ```typescript
    type s(a | b); // 或运算，满足a或b任意一个即可
    ```
- and
  
    ```typescript
    type s(a & b); // 与运算，要求满足a和b都有的部分
    ```
    ![and](https://upload.wikimedia.org/wikipedia/commons/9/99/Venn0001.svg)
- add
  
    ```typescript
    type s(a + b); // 和运算，要求满足a和b的所有内容
    ```
    ![add](https://upload.wikimedia.org/wikipedia/commons/3/30/Venn0111.svg)
- sub

    ```typescript
    type s(a - b); // 减运算，从a中排除掉b的部分
    ```
    ![sub](https://upload.wikimedia.org/wikipedia/commons/5/5a/Venn0010.svg)
- dif
  
    ```typescript
    type s(a !& b); // 对称差集，要求a和b之间不重叠的部分
    ```
    ![dif](https://upload.wikimedia.org/wikipedia/commons/4/46/Venn0110.svg)
- not
  
    ```typescript
    type s(!a); // 非运算，要求除了a以外的所有情况
    ```
    ![not](https://upload.wikimedia.org/wikipedia/commons/7/73/Venn10.svg)
### 类型运算级联
类型运算可以级联，既多个括号，一步步的缩小范围  
*这里的 `num` 是一个抽象类型，表示所有数字，不具有实际大小*  

```typescript
type x(num)(float - int);
```
# 前置类型
在泛型标注内的泛型名后带上括号来要求泛型前置依赖  
前置依赖可以是值也可以是其他类型  
前置依赖表示这个泛型必须是括号内类型运算结果的子类型  
*也可以用 ``` `() ``` 来表示一个匿名前置依赖*  

```csharp
fun [`n(num)]add(`n a, `n b):[`n] {
    return a + b;
}
add[int](1, 2);
add[float](1.5, 3.14);
add[1](1, 1);
```
#### 字面量类型运算
对于数字字面量类型可以进行范围运算  

```typescript
type x(int)(> 0);
type x(> 0); // 与上面等价，自动推导出 Int
type x(> 0.0); // 推导出 Float

type x(< 0);
type x(>= 0);
type x(<= 0);

type x(0..5);
type x(0<..5); // 不包括 0
type x(0..<5); // 不包括 5
```
#### 限定明确的数字类型
```typescript
type x(0i); 
type x(0i32);
type x(0i64); // 只能是 i16 类型的 0

type x(0f);
type x(0f32);
type x(0f64);
```
# 运算符

普通运算符不能连用，必须明确可区分  

## 运算符重载

- 中缀运算符

  ```csharp
  fun operator (a string) + (b string):[string] { }
  fun operator string.self + (b string):[string] { }
  fun operator (a string) + string.self:[string] { }

  val x = 'a' + 'b';
  ```

- 前缀运算符  

  ```csharp
  fun operator -(some int):[int] { 0 - some }

  val x = -1;
  ```

- 后缀运算符  
  重载时，后缀运算符的参数不能和中缀运算符的第一个参数重复  

  ```csharp
  inline:[Type] operator (a Type)? { 
    inline return Nullable[a] 
  }
  inline [`T(FunctionKind)]operator(f `T)! {
      // `T is * -> * -> ...
      // #Self 表示这个函数的类型
      // #self 表示这个函数自身
      // $0 表示第一个参数
      // ``${nameof(`T.head)}`` 不会影响编译结果，但是会影响代码提示  
      fun operator foo(``${nameof(`T.head)}`` `T.head):[`T.tail | #Self[`T.tail].inline.tail] {
          case(`T) 
          of [any -> FunctionKind] => f($0, ?..)!
          else f($0)
      }
      inline return foo
  }
  ```

## 第一类运算
第一类运算必然是前缀的  
从左向右的  

第一类运算很像调用运算符重载  
但是第一类运算必须有且只有一个参数  

```csharp
val a = (1 +);
val b = a b;

struct Some {
    fun operator self(other int):[int] {
        return other + 1
    }
}

val x = Some();
val y = x 1;
// 2

fun operator string.self(other int):[string] { '${self}${other}' }
val a = 'a' 1;
// 'a1'

fun operator foo(other int):[int] { other + 1 }
val b = foo 1
// 2
```

## 调用运算符重载

```csharp
record Some {
    fun self(other int):[int] {
        return other + 1
    }
    fun self(index int) operator =(value int) {
        print(index, value)
    }
}
val some = Some();
some(1);
// 2

let some(1) = 2;
// print: 1 2

fun string.self(other int):[int] { '${self}${other}' }
val a = 'a'(1);
// 'a1'
```
## 优先级

越上面优先级越低  

|运算符|结合性|备注|
|-|-|-|
|a `=op` b|右|
|a `|`, `||` b|左|
|a `&`, `&&` b|左|
|a `>op`, `<op`, `==op`, `|op`, `&op`, `!op` b|左|
|a `+op`, `-op` b|左|
|a `*op`, `/op`, `%op` b|左|
|a `**op` b|右|
|a `^op`, `..op` b|左|
|前缀运算符||
|后缀运算符||
|a `?op` b|左|
|f `( )`, `?()`|左|调用，语法特性
|a `[ ]`|左|类型构造，语法特性
|`[ ]` b|右|类型构造器定义，语法特性
|a `.`, `?.` id|左|取成员，管道运算符，语法特性

## 内置普通运算符列表

|运算符|用途|类型|
|-|-|-|
|- a|让值变成负数|数字|
|a + b      | 加运算    |数字|
|a - b      | 减运算    |数字|
|a * b      | 乘运算    |数字|
|a / b      | 除运算    |数字|
|a % b      | 模运算    |数字|
|a ** b     | 幂运算    |数字|
|! b        | 逻辑否    |是否|
|a && b     | 逻辑与    |是否|
|a \|\| b   | 逻辑或    |是否|
|a == b     | 相等      |相等类|
|a != b     | 不等      |相等类|
|a > b      | 大于      |比较类|
|a < b      | 小于      |比较类|
|a >= b     | 大于等于  |比较类|
|a <= b     | 小于等于  |比较类|
|a !<       | 不小于    |比较类|
|a !>       | 不大于    |比较类|
|a !        | 可变化    |类型|
|a ?        | 可空化    |类型|
|a ^ b      | 生成器    |类型|
|a ^        | 生成器    |类型|
|^ a        | 生成器    |类型|
|a ++       | 尾自增    |数字|
|++ a       | 首自增    |数字|
|a --       | 尾自减    |数字|
|-- a       | 首自减    |数字|
|a .. b     | 范围      |范围类|
|a ..       | 范围      |范围类|
|.. b       | 范围      |范围类|
|a & b      | 与类型    |类型|
|a + b      | 和类型    |类型|
|a - b      | 差类型    |类型|
|a !& b     | 对称差类型 |类型|
|! a        | 非类型    |类型|
|? a        | 空检测    |可空|
|a ?. b     | 空检测链  |可空|
|a ?? b     | 如果空    |可空|
|a !        | 异步等待  |异步|
---

## 特殊运算符
|运算符|用途|类型|是否可重载|是否能组合|
|-|-|-|-|-|
|=|赋值|可变，语法特性|是|否|
|x=|x为任何中缀运算符||否|否|
|()|调用，类型约束，语法特性|任何|是，仅调用|否|
|?()|空检测调用|可空可调用|是，仅调用|否|
|a \| b|或类型|类型|否|是|

未完待续





---
## 一些例子

#### fib
```haskell
type nat(>= 0);
fun fib(0):[nat] 0;
fun fib(1):[nat] 1;
fun fib(n):[nat] fib(n - 1) + fib(n - 2);
```
#### vector
```csharp
record [`n(1..), `T(Num)]Vec case {
    of [1] {
        val x `T;
    }
    of [2] {
        val x `T, `T y;
    }
    of [3] {
        val x `T, y `T, z `T;
    }
    of [4] {
        val x `T, y `T, z `T, w `T;
    }
    else {
        val x `T, y `T, z `T, w `T;
        #for(i in 5..`n) {
            val ``v${i}``
        }
    }
}

// Vec[3, int]
Vec[3] v = @{
    x = 1;
    y = 2;
    z = 3;
}
Vec[5] v = @{
    x = 1;
    y = 2;
    z = 3;
    w = 4;
    v5 = 5;
}
```
#### functor
```cc
class [`f]Functor {
    fun fmap(`f[`a], (`a)[`b]):[`f[`b]];
}
```
