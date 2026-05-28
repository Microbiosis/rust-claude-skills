---
name: rust-reference
description: "Rust 语言参考技能。提供 Rust 语言的完整参考，包括语法、类型系统、内存模型、并发模型等。TRIGGER when: 用户需要了解 Rust 语言细节、查阅语言规范、或需要深入了解 Rust 内部机制时触发。SKIP: 基础 Rust 开发。"
---

# Rust 语言参考技能

本技能提供 Rust 语言的完整参考，基于 [Rust Reference](https://github.com/rust-lang/reference/)。

## 触发条件

当用户执行以下操作时触发此技能：
- 了解 Rust 语言细节
- 查阅语言规范
- 深入了解 Rust 内部机制

## 跳过条件

以下情况不触发此技能：
- 基础 Rust 开发（使用 `rust-language` 技能）

---

## 语法

### 关键字

```rust
// 保留关键字
as, async, await, break, const, continue, crate, dyn, else, enum, extern
false, fn, for, if, impl, in, let, loop, match, mod, move, mut, pub, ref
return, self, Self, static, struct, super, trait, true, type, unsafe, use
where, while, yield

// 保留关键字（未来使用）
abstract, become, box, do, final, macro, override, priv, try, typeof
unsized, virtual
```

### 运算符

```rust
// 算术运算符
+ - * / %

// 比较运算符
== != < > <= >=

// 逻辑运算符
! && ||

// 位运算符
& | ^ ! << >>

// 赋值运算符
= += -= *= /= %= &= |= ^= <<= >>=

// 其他运算符
.. ..=  // 范围
?       // 错误传播
->      // 函数返回类型
=>      // match 分支
@       // 模式绑定
#       // 属性
$       // 宏
```

---

## 类型系统

### 原始类型

```rust
// 布尔类型
bool  // true, false

// 字符类型
char  // Unicode 标量值

// 整数类型
i8, i16, i32, i64, i128, isize
u8, u16, u32, u64, u128, usize

// 浮点类型
f32, f64

// 单元类型
()  // 空元组

// 永不类型
!   // 永不返回
```

### 复合类型

```rust
// 元组
(i32, f64, bool)  // 固定长度
()                // 空元组
(T,)              // 单元素元组

// 数组
[i32; 5]  // 固定长度数组
[T; N]    // 泛型数组

// 切片
[T]       // 动态大小切片
str       // 字符串切片
```

### 类型别名

```rust
type Kilometers = i32;
type Thunk = Box<dyn Fn() + Send + 'static>;
type Result<T> = std::result::Result<T, std::io::Error>;
```

---

## 内存模型

### 所有权

```rust
// 每个值有且只有一个所有者
let s1 = String::from("hello");
let s2 = s1;  // s1 所有权转移给 s2

// 当所有者离开作用域，值被丢弃
{
    let s = String::from("hello");
    // s 在这里有效
}   // s 在这里被丢弃
```

### 引用

```rust
// 不可变引用
let s = String::from("hello");
let r = &s;  // r 是 s 的不可变引用

// 可变引用
let mut s = String::from("hello");
let r = &mut s;  // r 是 s 的可变引用

// 引用规则
// 1. 任意数量的不可变引用
// 2. 或者一个可变引用
// 3. 不能同时存在
```

### 生命周期

```rust
// 生命周期标注
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// 生命周期省略规则
// 1. 每个引用参数都有自己的生命周期
// 2. 如果只有一个输入生命周期，它被赋给所有输出生命周期
// 3. 如果有多个输入生命周期，但其中一个是 &self 或 &mut self，那么 self 的生命周期被赋给所有输出生命周期
```

---

## 并发模型

### 线程

```rust
use std::thread;

// 创建线程
let handle = thread::spawn(|| {
    // 线程代码
});

// 等待线程完成
handle.join().unwrap();

// 线程间通信
let (tx, rx) = mpsc::channel();
```

### 同步原语

```rust
use std::sync::{Mutex, RwLock, Arc};

// Mutex
let m = Mutex::new(5);
{
    let mut num = m.lock().unwrap();
    *num = 6;
}

// RwLock
let lock = RwLock::new(5);
let r1 = lock.read().unwrap();
let r2 = lock.read().unwrap();
let mut w = lock.write().unwrap();

// Arc
let counter = Arc::new(Mutex::new(0));
let counter_clone = Arc::clone(&counter);
```

### Send 和 Sync

```rust
// Send: 可以在线程间转移所有权
// Sync: 可以在线程间共享引用

// 自动实现
// 大多数类型自动实现 Send 和 Sync
// Rc<T> 不是 Send，Cell<T> 不是 Sync

// 手动实现
unsafe impl Send for MyType {}
unsafe impl Sync for MyType {}
```

---

## 模式匹配

### 模式语法

```rust
// 字面量
42
"hello"
true

// 变量绑定
let x = 5;
let (x, y) = (1, 2);

// 通配符
let _ = 5;
let (_, y) = (1, 2);

// 引用
let &x = &5;
let ref x = 5;

// 结构体
struct Point { x: i32, y: i32 }
let Point { x, y } = Point { x: 1, y: 2 };
let Point { x, .. } = Point { x: 1, y: 2 };

// 枚举
enum Shape { Circle(f64), Rectangle(f64, f64) }
let Shape::Circle(radius) = shape;
let Shape::Rectangle(width, height) = shape;

// 范围
1..=5
'a'..='z'

// 守卫
match x {
    n if n > 0 => println!("正数"),
    n if n < 0 => println!("负数"),
    _ => println!("零"),
}

// 绑定
match x {
    n @ 1..=5 => println!("1 到 5: {}", n),
    _ => println!("其他"),
}
```

---

## 属性

### 内置属性

```rust
// 派生属性
#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash)]
struct Point { x: i32, y: i32 }

// 条件编译
#[cfg(target_os = "linux")]
fn linux_only() {}

// 测试
#[test]
fn test_function() {}

// 忽略测试
#[test]
#[ignore]
fn expensive_test() {}

// 应该 panic
#[test]
#[should_panic(expected = "除以零")]
fn test_panic() {}

// 文档
/// 这是文档注释
#[doc = "这是文档属性"]
fn documented() {}
```

### 自定义属性

```rust
// 过程宏属性
#[my_attribute]
fn decorated() {}

// 属性宏
#[derive(MyMacro)]
struct MyStruct;
```

---

## 宏

### 声明宏

```rust
macro_rules! my_macro {
    () => {
        println!("无参数");
    };
    ($x:expr) => {
        println!("一个参数: {}", $x);
    };
    ($x:expr, $y:expr) => {
        println!("两个参数: {} 和 {}", $x, $y);
    };
}

fn main() {
    my_macro!();
    my_macro!(42);
    my_macro!(1, 2);
}
```

### 过程宏

```rust
// 派生宏
#[proc_macro_derive(MyMacro)]
pub fn my_macro_derive(input: TokenStream) -> TokenStream {
    // 实现
}

// 属性宏
#[proc_macro_attribute]
pub fn my_attribute(args: TokenStream, input: TokenStream) -> TokenStream {
    // 实现
}

// 函数式宏
#[proc_macro]
pub fn my_macro(input: TokenStream) -> TokenStream {
    // 实现
}
```

---

## 最佳实践

1. **理解所有权**：所有权是 Rust 的核心概念
2. **使用引用**：避免不必要的所有权转移
3. **生命周期标注**：让编译器推断，只在必要时标注
4. **模式匹配**：使用 match 和 if let 处理枚举
5. **属性驱动**：使用属性简化代码

---

## 常见陷阱

1. **借用检查器**：理解可变和不可变借用的规则
2. **生命周期**：理解引用的有效期
3. **类型推断**：有时需要显式类型标注
4. **模式匹配**：确保覆盖所有情况
5. **宏卫生**：理解宏的作用域和展开规则
