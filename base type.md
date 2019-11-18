# 基础类型  
|类型名|大小(字节)|字面量|
|-|-|-|
|bool|1，1/8|true，false|
|i8|1|0i8|
|u8，char|1|c"x", c'x'，0u8|
|i16|2|0i16|
|i32，int|4|0，0i32|
|i64|8|0i64|
|i128|16|0i128|
|u16|2|0u16|
|u32，uint|4|0u，0u32|
|u64|8|0u64|
|u128|16|0u128|
|f32|4|0f，0f32|
|float，f64|8|0.0，0d，0f64|
|f128|16|0f128|

## 抽象类型
抽象类型大小不可确定  

|类型名|字面量|
|-|-|
|void|void|
|null|null|
|~~N/A~~|nullptr|
|never|never|

## 高级类型
高级类型大小是变长的  

|类型名|字面量|
|-|-|
|string|'x', "x"|
|Mutable[type]|mut type|
|Nullable[type]|type?|

## 指针和引用
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

## 可空类型

可空的 空为 `null`  
而指针的空位 `nullptr`  

`type?`  
`mut type?`  
`type?*`  
`type?&`  
