# 函数

函数最后可省略 `return`  
没有 `{}` 的函数可以省略返回类型  
不写类型的话会自动推断  
函数里最后的一句话可以省略 `;`

```cs
def add fun(a int, b int) int = {
    return a + b
};
def add fun(a, b) int = {
    a + b
};
def add fun(a, b) = a + b;
```

## 类型注解
```cs
#type fun(int, int) int;
def add fun(a, b) = {
    a + b
};

#type fun(a int, b int) int;
def add = {
    a + b
};
```

### 函数变量

```cc
def add mut fun(a int, b int) int;
let add = { a + b };
```

### 返回类型自动推断
使用 `auto` 关键字 

```cc
def add fun(a int, b int) auto = {
    a + b
};
```

### 可变返回值
返回值必须是不可变的  
但是可以在定义变量时省略具体类型  

```scala
def some fun() int = 1;
def a mut = some();
```

## lambda

```csharp
def l = x => { x + 1 };
l 1;
    
def l2 = { it + 1 };
l2 1;
    
def l3 = (a, b)=> { a + b };
    
def l4 = { a, b => a + b };
    
def a = (a int, b int):[int] => { a + b }; 
    
def b = {(a int, b int):[int] => a + b }; 

def b = { a int, b int -> int => a + b }; 
    
def c = fun (int a, int b) int => a + b;
```
### 指定闭包捕获
如果不指定那么默认自动判断捕获  

```scala
def some fun() {
    def a = 1;
    def b = 1;
    def c = |a, b|{ a + b };
}
def some fun() {
    def a = 1;
    def b = 1;
    def c fun|a, b|() = { a + b };
}
```

## 函数类型
```csharp
def add (a int, b int) -> int = { a + b };
def add a int -> b int -> int = { a + b };
    
def some fun(add fun(a int, b int) int) = {
    add(1, 2);
}
def some fun(add (a int, b int) -> int) = {
    add(1, 2);
}
def some fun(add a int -> b int -> int) = {
    add(1, 2);
}
```

## 部分调用
```csharp
def add1 = add(1, ?);
def add2 = add(?, 2);
def more = some(1, 2, ?..3, 6);
def type = some(1, ?int);
```
- ##### 尾部函数部分调用   
同个调用只能存在一个尾部函数调用  
同时接受多个函数时候需要用 `?=>?` 或 `()=>?` 指定  

```csharp
some((a, b)=>?, 1, 2) {
    a + b
};
    
some(?, 1, 2) { a, b =>
    a + b
};

some(?=>?, ?, ?, 1, 2) {
    a + b
};
```
### 模板字符串的部分调用 
```cc
def some fun() {
    return '${?} - ${1}';
};
some();
// [A](a A) -> string
```
