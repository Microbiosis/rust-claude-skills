---
name: rust-language
description: "Rust 语言核心开发技能。提供 Rust 语言的完整参考，包括所有权、借用、生命周期、泛型、trait、错误处理、并发、异步等核心概念。TRIGGER when: 用户编写 Rust 代码、询问 Rust 语法、需要 Rust 代码示例、调试 Rust 程序、或需要了解 Rust 语言特性时触发。SKIP: 非 Rust 语言的开发任务。"
---

# Rust 语言核心开发技能

本技能提供 Rust 语言的完整开发参考，基于 [The Rust Programming Language](https://github.com/rust-lang/book) 和 [Rust by Example](https://github.com/rust-lang/rust-by-example)。

## 触发条件

当用户执行以下操作时触发此技能：
- 编写或修改 Rust 代码（`.rs` 文件）
- 询问 Rust 语言语法或特性
- 需要 Rust 代码示例
- 调试 Rust 程序
- 了解 Rust 的所有权、借用、生命周期等核心概念

## 跳过条件

以下情况不触发此技能：
- 非 Rust 语言的开发任务
- 嵌入式 Rust 开发（使用 `rust-embedded` 技能）
- CLI 工具开发（使用 `rust-cli` 技能）
- unsafe Rust 开发（使用 `rust-advanced` 技能）

---

## 基础语法

### 变量与可变性

```rust
let x = 5;          // 不可变变量
let mut y = 10;     // 可变变量
y = 15;

const MAX: u32 = 100; // 常量

let x = x + 1;      // 变量遮蔽
let x = x * 2;      // 可以改变类型
```

### 数据类型

```rust
// 标量类型
let integer: i32 = 42;
let float: f64 = 3.14;
let boolean: bool = true;
let character: char = 'A';

// 复合类型
let tuple: (i32, f64, bool) = (42, 3.14, true);
let (x, y, z) = tuple;  // 解构
let first = tuple.0;     // 索引访问

let array: [i32; 5] = [1, 2, 3, 4, 5];
let first = array[0];
```

### 函数

```rust
fn add(x: i32, y: i32) -> i32 {
    x + y  // 无分号表示返回值
}

fn print_info(name: &str, age: u32) {
    println!("{} is {} years old", name, age);
}
```

### 控制流

```rust
let number = 7;

if number > 5 {
    println!("大于 5");
} else if number > 0 {
    println!("大于 0");
} else {
    println!("小于等于 0");
}

// if 表达式
let result = if number > 5 { "big" } else { "small" };

// 循环
for i in 0..10 {
    println!("{}", i);
}

let mut count = 0;
while count < 5 {
    count += 1;
}

loop {
    if count >= 10 {
        break;
    }
    count += 1;
}
```

---

## 所有权系统

### 所有权规则

1. Rust 中每一个值都有一个所有者
2. 值在任意时刻有且只有一个所有者
3. 当所有者离开作用域，这个值将被丢弃

```rust
let s1 = String::from("hello");
let s2 = s1;  // s1 所有权转移给 s2，s1 不再有效

// println!("{}", s1);  // 编译错误！
println!("{}", s2);     // 正常
```

### 引用与借用

```rust
let s = String::from("hello");

// 不可变引用
let len = calculate_length(&s);
println!("{} 的长度是 {}", s, len);

fn calculate_length(s: &String) -> usize {
    s.len()
}

// 可变引用
let mut s = String::from("hello");
change(&mut s);

fn change(s: &mut String) {
    s.push_str(", world");
}
```

### 切片

```rust
let s = String::from("hello world");
let hello = &s[0..5];   // "hello"
let world = &s[6..11];  // "world"

let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];   // [2, 3]
```

---

## 结构体与枚举

### 结构体

```rust
struct User {
    username: String,
    email: String,
    active: bool,
}

let user = User {
    username: String::from("alice"),
    email: String::from("alice@example.com"),
    active: true,
};

// 方法
impl User {
    fn new(username: String, email: String) -> Self {
        Self {
            username,
            email,
            active: true,
        }
    }

    fn greet(&self) -> String {
        format!("Hello, {}!", self.username)
    }
}
```

### 枚举

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));

// Option 枚举
let some_number: Option<i32> = Some(42);
let no_number: Option<i32> = None;

// match 模式匹配
match some_number {
    Some(n) => println!("数字: {}", n),
    None => println!("没有数字"),
}
```

---

## 泛型与 Trait

### 泛型

```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}
```

### Trait

```rust
trait Summary {
    fn summarize(&self) -> String;

    fn default_summary(&self) -> String {
        String::from("(Read more...)")
    }
}

struct Article {
    title: String,
    content: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{}: {}", self.title, &self.content[..50])
    }
}

// Trait 作为参数
fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}

// Trait bound
fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

---

## 生命周期

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

struct ImportantExcerpt<'a> {
    part: &'a str,
}

impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }

    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention please: {}", announcement);
        self.part
    }
}
```

---

## 错误处理

### panic! 与不可恢复错误

```rust
panic!("crash and burn");

// 使用 expect 提供更好的错误信息
let f = File::open("hello.txt").expect("无法打开文件");
```

### Result 与可恢复错误

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username() -> Result<String, io::Error> {
    let mut f = File::open("username.txt")?;
    let mut username = String::new();
    f.read_to_string(&mut username)?;
    Ok(username)
}

// 使用 ? 运算符简化
fn read_username_short() -> Result<String, io::Error> {
    let mut username = String::new();
    File::open("username.txt")?.read_to_string(&mut username)?;
    Ok(username)
}
```

---

## 集合

### Vec

```rust
let mut v: Vec<i32> = Vec::new();
v.push(1);
v.push(2);

let v = vec![1, 2, 3, 4, 5];
let third: &i32 = &v[2];
println!("第三个元素是 {}", third);

for i in &v {
    println!("{}", i);
}
```

### String

```rust
let mut s = String::from("Hello");
s.push_str(", world!");
s.push('!');

let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = format!("{}{}", s1, s2);
```

### HashMap

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Red"), 50);

let team = String::from("Blue");
let score = scores.get(&team);

for (key, value) in &scores {
    println!("{}: {}", key, value);
}
```

---

## 闭包与迭代器

### 闭包

```rust
let add_one = |x: i32| -> i32 { x + 1 };
let add_one = |x| x + 1;  // 类型推断

let mut list = vec![1, 2, 3];
let mut push_value = || list.push(4);  // 可变借用
push_value();

// move 闭包
let name = String::from("Alice");
let greeting = move || println!("Hello, {}!", name);
```

### 迭代器

```rust
let v = vec![1, 2, 3];
let iter = v.iter();

for val in iter {
    println!("{}", val);
}

// 迭代器适配器
let v2: Vec<i32> = v.iter().map(|x| x + 1).collect();
let sum: i32 = v.iter().sum();
let evens: Vec<&i32> = v.iter().filter(|&&x| x % 2 == 0).collect();
```

---

## 并发

### 线程

```rust
use std::thread;

let handle = thread::spawn(|| {
    for i in 1..10 {
        println!("hi number {} from the spawned thread!", i);
    }
});

for i in 1..5 {
    println!("hi number {} from the main thread!", i);
}

handle.join().unwrap();
```

### 消息传递

```rust
use std::sync::mpsc;
use std::thread;

let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    let val = String::from("hello");
    tx.send(val).unwrap();
});

let received = rx.recv().unwrap();
println!("Got: {}", received);
```

### 共享状态

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

---

## 异步编程

```rust
use tokio;

async fn fetch_data() -> String {
    // 异步操作
    String::from("data")
}

#[tokio::main]
async fn main() {
    let data = fetch_data().await;
    println!("{}", data);
}
```

---

## 模块系统

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

---

## 最佳实践

1. **优先使用不可变变量**：只在需要时使用 `mut`
2. **使用 `Option` 和 `Result`**：而不是 `null` 或 `panic`
3. **利用所有权系统**：让编译器帮助你管理内存
4. **使用 trait 抽象**：定义共享行为
5. **使用迭代器**：比手动循环更简洁高效
6. **合理使用生命周期标注**：让编译器推断，只在必要时标注

---

## 常见陷阱

1. **借用检查器**：理解可变引用和不可变引用的规则
2. **生命周期**：理解引用的有效期
3. **所有权转移**：注意值的所有权转移
4. **类型推断**：有时需要显式类型标注
5. **错误处理**：使用 `?` 运算符简化错误传播
