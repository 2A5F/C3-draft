# 变量定义

```rust
def a = 1
```

## 可变变量

`mut int` 是类型

```cc
def a mut int
let a = 1
```

### 变量修改语句

`let` 是语句，没有返回值  

```swift
let a = 2;
a++; // 这个是运算符，有返回值
let a + 1; // 其他语言是 a += 1
```

## 类型标注

```cs
def a int = 1
def b mut int
let b = int
```

### 类型注解

```cs
#type int
def a = 1

#type mut int
def b
let b = 1
```

## 延迟初始化不可变变量

```cs
def a by lazy {
    1
}
```

## 常量
常量是编译时决定的  
一般不需要使用，不可变值会自动优化成常量  

```cs
def a = const 1
```

# 注释
```swift
// 单行

/* 多
行*/

/* /* 嵌套 */ */

/** 文档 */
```
# 细节 

## 字符串
```js
'123'
"asd"
```

## 数字中间符
```kotlin
1_000_000
```

## 字符串模板
```dart
'${some}'
```
## 所有字符串都是多行的  
```c
"asd
123"
```