---
name: rust-exercises
description: "Rust 练习技能。提供 Rust 练习和学习资源的完整参考，包括 Rustlings、练习题、最佳实践等。TRIGGER when: 用户学习 Rust、需要练习题、或需要了解 Rust 学习资源时触发。SKIP: 非学习相关的 Rust 开发。"
---

# Rust 练习技能

本技能提供 Rust 练习和学习资源的完整参考，基于 [Rustlings](https://github.com/rust-lang/rustlings/)。

## 触发条件

当用户执行以下操作时触发此技能：
- 学习 Rust
- 需要练习题
- 了解 Rust 学习资源

## 跳过条件

以下情况不触发此技能：
- 非学习相关的 Rust 开发

---

## Rustlings

### 安装

```bash
# 安装 rustlings
curl -L https://raw.githubusercontent.com/rust-lang/rustlings/main/install.sh | bash

# 或者使用 cargo
cargo install rustlings
```

### 运行

```bash
# 运行所有练习
rustlings

# 运行特定练习
rustlings run intro1

# 监听模式
rustlings watch

# 查看进度
rustlings list

# 提示
rustlings hint intro1
```

### 练习分类

| 分类 | 说明 | 练习数 |
|------|------|--------|
| intro | 基础介绍 | 2 |
| variables | 变量 | 6 |
| functions | 函数 | 5 |
| if | 条件 | 2 |
| quiz | 测验 | 1 |
| move_semantics | 移动语义 | 6 |
| primitive_types | 原始类型 | 3 |
| structs | 结构体 | 2 |
| strings | 字符串 | 4 |
| enums | 枚举 | 3 |
| modules | 模块 | 2 |
| hashmaps | 哈希映射 | 3 |
| quiz | 测验 | 1 |
| options | 选项 | 4 |
| error_handling | 错误处理 | 4 |
| generics | 泛型 | 2 |
| traits | trait | 5 |
| quiz | 测验 | 1 |
| lifetimes | 生命周期 | 3 |
| iterators | 迭代器 | 6 |
| smart_pointers | 智能指针 | 3 |
| threads | 线程 | 2 |
| macros | 宏 | 2 |
| clippy | Clippy | 2 |
| conversions | 转换 | 2 |

---

## 基础练习

### 变量

```rust
// 练习：修复变量声明
fn main() {
    let x = 5;
    println!("x = {}", x);

    // 修复：x 是不可变的
    let mut x = 5;
    x = 10;
    println!("x = {}", x);
}
```

### 函数

```rust
// 练习：实现函数
fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    let result = add(1, 2);
    println!("1 + 2 = {}", result);
}
```

### 条件

```rust
// 练习：使用 if 表达式
fn main() {
    let x = 42;
    let message = if x > 0 {
        "正数"
    } else if x < 0 {
        "负数"
    } else {
        "零"
    };
    println!("{} 是 {}", x, message);
}
```

---

## 中级练习

### 移动语义

```rust
// 练习：理解所有权转移
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;  // s1 的所有权转移给 s2

    // println!("{}", s1);  // 编译错误！
    println!("{}", s2);     // 正常

    // 使用 clone 避免所有权转移
    let s3 = s2.clone();
    println!("s2 = {}, s3 = {}", s2, s3);
}
```

### 引用与借用

```rust
// 练习：使用引用
fn calculate_length(s: &String) -> usize {
    s.len()
}

fn main() {
    let s = String::from("hello");
    let len = calculate_length(&s);
    println!("'{}' 的长度是 {}", s, len);
}
```

### 结构体

```rust
// 练习：定义结构体和方法
struct Rectangle {
    width: f64,
    height: f64,
}

impl Rectangle {
    fn new(width: f64, height: f64) -> Self {
        Self { width, height }
    }

    fn area(&self) -> f64 {
        self.width * self.height
    }

    fn perimeter(&self) -> f64 {
        2.0 * (self.width + self.height)
    }
}

fn main() {
    let rect = Rectangle::new(10.0, 5.0);
    println!("面积: {}", rect.area());
    println!("周长: {}", rect.perimeter());
}
```

### 枚举

```rust
// 练习：使用枚举和模式匹配
enum Shape {
    Circle(f64),
    Rectangle(f64, f64),
    Triangle(f64, f64, f64),
}

impl Shape {
    fn area(&self) -> f64 {
        match self {
            Shape::Circle(radius) => std::f64::consts::PI * radius * radius,
            Shape::Rectangle(width, height) => width * height,
            Shape::Triangle(a, b, c) => {
                let s = (a + b + c) / 2.0;
                (s * (s - a) * (s - b) * (s - c)).sqrt()
            }
        }
    }
}

fn main() {
    let circle = Shape::Circle(5.0);
    let rect = Shape::Rectangle(10.0, 5.0);
    let triangle = Shape::Triangle(3.0, 4.0, 5.0);

    println!("圆形面积: {}", circle.area());
    println!("矩形面积: {}", rect.area());
    println!("三角形面积: {}", triangle.area());
}
```

---

## 高级练习

### 泛型

```rust
// 练习：使用泛型
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn main() {
    let numbers = vec![34, 50, 25, 100, 65];
    let result = largest(&numbers);
    println!("最大的数字是 {}", result);

    let chars = vec!['y', 'm', 'a', 'q'];
    let result = largest(&chars);
    println!("最大的字符是 {}", result);
}
```

### Trait

```rust
// 练习：实现 trait
trait Summary {
    fn summarize(&self) -> String;

    fn default_summary(&self) -> String {
        String::from("(阅读更多...)")
    }
}

struct Article {
    title: String,
    content: String,
    author: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{}，作者：{}", self.title, self.author)
    }
}

fn notify(item: &impl Summary) {
    println!("新闻：{}", item.summarize());
}

fn main() {
    let article = Article {
        title: String::from("Rust 语言"),
        content: String::from("Rust 是一门系统编程语言"),
        author: String::from("Mozilla"),
    };

    notify(&article);
    println!("默认摘要：{}", article.default_summary());
}
```

### 生命周期

```rust
// 练习：使用生命周期
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
        println!("请注意：{}", announcement);
        self.part
    }
}

fn main() {
    let string1 = String::from("很长的字符串");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
        println!("更长的字符串是 '{}'", result);
    }

    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence;
    {
        let i = novel.find('.').unwrap_or(novel.len());
        first_sentence = &novel[..i];
    }

    let excerpt = ImportantExcerpt { part: first_sentence };
    excerpt.announce_and_return_part("这是一个重要摘录");
}
```

---

## 学习资源

### 官方资源

- [The Rust Programming Language](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Rustlings](https://github.com/rust-lang/rustlings/)
- [Rust Reference](https://doc.rust-lang.org/reference/)

### 社区资源

- [Rust 语言圣经](https://course.rs/)
- [Rust 程序设计语言](https://kaisery.github.io/trpl-zh-cn/)
- [Rust 之旅](https://tourofrust.com/)

### 实践项目

- [Rust 项目实战](https://github.com/rust-unofficial/awesome-rust)
- [Rust 算法](https://github.com/TheAlgorithms/Rust)
- [Rust 设计模式](https://github.com/rust-unofficial/patterns)

---

## 最佳实践

1. **循序渐进**：从基础练习开始，逐步增加难度
2. **理解错误**：仔细阅读编译错误，理解原因
3. **查阅文档**：遇到问题时查阅官方文档
4. **实践项目**：通过实际项目巩固知识
5. **社区交流**：参与 Rust 社区讨论

---

## 常见陷阱

1. **急于求成**：Rust 学习曲线较陡，需要耐心
2. **忽视错误信息**：编译错误包含有用信息
3. **不理解所有权**：所有权是 Rust 的核心概念
4. **过度使用 unsafe**：优先使用安全的 Rust
5. **不写测试**：测试是保证代码质量的重要手段
