---
name: rust-unstable
description: "Rust 不稳定特性技能。提供 Rust 不稳定特性的完整参考，包括 nightly 特性、实验性功能等。TRIGGER when: 用户使用 nightly Rust、需要不稳定特性、或需要了解实验性功能时触发。SKIP: 稳定版 Rust 开发。"
---

# Rust 不稳定特性技能

本技能提供 Rust 不稳定特性的完整参考，基于 [Unstable Book](https://github.com/rust-lang/rust/tree/HEAD/src/doc/unstable-book)。

## 触发条件

当用户执行以下操作时触发此技能：
- 使用 nightly Rust
- 需要不稳定特性
- 了解实验性功能

## 跳过条件

以下情况不触发此技能：
- 稳定版 Rust 开发

---

## 使用 nightly Rust

### 安装 nightly

```bash
rustup install nightly
rustup default nightly
```

### 使用 nightly 特性

```rust
#![feature(some_feature)]

fn main() {
    // 使用不稳定特性
}
```

---

## 常用不稳定特性

### async_fn_in_trait

```rust
#![feature(async_fn_in_trait)]

trait Database {
    async fn get(&self, key: &str) -> Option<String>;
    async fn set(&self, key: &str, value: String);
}

struct MyDatabase;

impl Database for MyDatabase {
    async fn get(&self, key: &str) -> Option<String> {
        // 实现
        None
    }

    async fn set(&self, key: &str, value: String) {
        // 实现
    }
}
```

### const_generics

```rust
#![feature(generic_const_exprs)]

struct Array<T, const N: usize> {
    data: [T; N],
}

impl<T, const N: usize> Array<T, N> {
    fn len(&self) -> usize {
        N
    }
}

fn main() {
    let arr = Array { data: [1, 2, 3] };
    println!("长度: {}", arr.len());
}
```

### type_alias_impl_trait

```rust
#![feature(type_alias_impl_trait)]

type MyFuture = impl std::future::Future<Output = i32>;

fn make_future() -> MyFuture {
    async { 42 }
}

fn main() {
    let future = make_future();
}
```

### generators

```rust
#![feature(generators, generator_trait)]

use std::ops::{Generator, GeneratorState};

fn main() {
    let mut generator = || {
        yield 1;
        yield 2;
        yield 3;
        return "done";
    };

    loop {
        match generator.resume() {
            GeneratorState::Yielded(value) => println!("Yielded: {}", value),
            GeneratorState::Complete(result) => {
                println!("Complete: {}", result);
                break;
            }
        }
    }
}
```

### box_syntax

```rust
#![feature(box_syntax)]

fn main() {
    let boxed = box 42;
    println!("{}", boxed);
}
```

### try_trait_v2

```rust
#![feature(try_trait_v2)]

use std::ops::ControlFlow;

fn divide(a: i32, b: i32) -> ControlFlow<String, i32> {
    if b == 0 {
        ControlFlow::Break("除以零".to_string())
    } else {
        ControlFlow::Continue(a / b)
    }
}

fn main() {
    match divide(10, 2) {
        ControlFlow::Continue(value) => println!("结果: {}", value),
        ControlFlow::Break(error) => println!("错误: {}", error),
    }
}
```

### pattern

```rust
#![feature(pattern)]

use std::str::Pattern;

fn main() {
    let s = "hello world";
    let pattern = "world";

    if let Some(index) = s.find(pattern) {
        println!("找到 'world' 在索引 {}", index);
    }
}
```

### slice_patterns

```rust
#![feature(slice_patterns)]

fn main() {
    let v = vec![1, 2, 3, 4, 5];

    match v.as_slice() {
        [1, rest @ ..] => println!("以 1 开头, 剩余: {:?}", rest),
        [.., 5] => println!("以 5 结尾"),
        [first, middle @ .., last] => {
            println!("第一个: {}, 中间: {:?}, 最后: {}", first, middle, last);
        }
        _ => println!("其他"),
    }
}
```

### let_chains

```rust
#![feature(let_chains)]

fn main() {
    let Some(x) = Some(42) else {
        return;
    };

    if x > 0 && let Some(y) = Some(x * 2) {
        println!("y = {}", y);
    }
}
```

### generic_associated_types

```rust
#![feature(generic_associated_types)]

trait LendingIterator {
    type Item<'a> where Self: 'a;

    fn next<'a>(&'a mut self) -> Option<Self::Item<'a>>;
}

struct WindowsMut<'a, T> {
    slice: &'a mut [T],
    pos: usize,
}

impl<'a, T> LendingIterator for WindowsMut<'a, T> {
    type Item<'b> = &'b mut [T] where Self: 'b;

    fn next<'b>(&'b mut self) -> Option<Self::Item<'b>> {
        // 实现
        None
    }
}
```

### trait_upcasting

```rust
#![feature(trait_upcasting)]

trait Animal {
    fn name(&self) -> &str;
}

trait Dog: Animal {
    fn bark(&self) -> &str;
}

struct MyDog;

impl Animal for MyDog {
    fn name(&self) -> &str {
        "MyDog"
    }
}

impl Dog for MyDog {
    fn bark(&self) -> &str {
        "Woof!"
    }
}

fn main() {
    let dog: &dyn Dog = &MyDog;
    let animal: &dyn Animal = dog;  // trait upcasting
    println!("{} says {}", animal.name(), dog.bark());
}
```

---

## 最佳实践

1. **谨慎使用**：不稳定特性可能在稳定版中变更
2. **关注 RFC**：了解特性的设计和讨论
3. **测试覆盖**：不稳定特性可能有 bug
4. **版本锁定**：锁定 nightly 版本避免意外变更

---

## 常见陷阱

1. **特性变更**：不稳定特性可能在 nightly 版本间变更
2. **编译错误**：某些特性可能有未解决的 bug
3. **性能问题**：某些特性可能未优化
4. **兼容性**：不稳定特性可能与稳定版代码不兼容
