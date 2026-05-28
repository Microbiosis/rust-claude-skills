---
name: rust-edition
description: "Rust 版本指南技能。提供 Rust 版本（Edition）的完整参考，包括版本差异、迁移指南、新特性等。TRIGGER when: 用户需要了解 Rust 版本差异、迁移代码到新版本、或需要了解版本新特性时触发。SKIP: 非版本相关的 Rust 开发。"
---

# Rust 版本指南技能

本技能提供 Rust 版本（Edition）的完整参考，基于 [Edition Guide](https://github.com/rust-lang/edition-guide)。

## 触发条件

当用户执行以下操作时触发此技能：
- 了解 Rust 版本差异
- 迁移代码到新版本
- 了解版本新特性

## 跳过条件

以下情况不触发此技能：
- 非版本相关的 Rust 开发

---

## 版本概述

Rust 版本（Edition）是 Rust 的里程碑，每 3 年发布一次：

| 版本 | 发布年份 | 主要特性 |
|------|----------|----------|
| Rust 2015 | 2015 | 初始版本 |
| Rust 2018 | 2018 | 模块系统改进、async/await |
| Rust 2021 | 2021 | 闭包捕获改进、IntoIterator for arrays |
| Rust 2024 | 2024 | gen 关键字、let-else |

---

## Cargo.toml 配置

```toml
[package]
name = "my-project"
version = "0.1.0"
edition = "2021"  # 指定版本
```

---

## Rust 2015

### 特性

- 初始版本
- 基础语法和类型系统
- 所有权和借用
- trait 和泛型

### 限制

- 没有 async/await
- 模块系统较复杂
- 没有 NLL（Non-Lexical Lifetimes）

---

## Rust 2018

### 新特性

#### 模块系统改进

```rust
// 2015
mod foo {
    mod bar {
        fn baz() {}
    }
}

// 2018
mod foo;
mod foo {
    mod bar;
    mod bar {
        fn baz() {}
    }
}
```

#### async/await

```rust
async fn fetch_data() -> String {
    // 异步操作
    String::from("data")
}

#[tokio::main]
async fn main() {
    let data = fetch_data().await;
}
```

#### NLL（Non-Lexical Lifetimes）

```rust
fn main() {
    let mut data = vec![1, 2, 3];
    let first = &data[0];
    println!("{}", first);
    data.push(4);  // 2015 会报错，2018 可以
}
```

#### 路径改进

```rust
// 2015
use std::vec::Vec;
use std::collections::HashMap;

// 2018
use std::{vec::Vec, collections::HashMap};
use std::collections::{HashMap, HashSet};
```

---

## Rust 2021

### 新特性

#### 闭包捕获改进

```rust
// 2018
let mut map = HashMap::new();
let key = String::from("key");
let closure = || {
    map.insert(key, 1);  // 需要 move
};

// 2021
let mut map = HashMap::new();
let key = String::from("key");
let closure = || {
    map.insert(key, 1);  // 自动捕获需要的字段
};
```

#### IntoIterator for arrays

```rust
// 2018
for i in [1, 2, 3].iter() {
    println!("{}", i);
}

// 2021
for i in [1, 2, 3] {
    println!("{}", i);
}
```

#### 新的 prelude

```rust
// 2021 prelude 包含
use std::prelude::rust_2021::*;
// 包含 TryFrom, TryInto, FromIterator
```

#### panic 宏改进

```rust
// 2018
panic!("hello {}", "world");

// 2021
panic!("hello {name}", name = "world");
```

---

## Rust 2024

### 新特性

#### gen 关键字

```rust
// 生成器
let mut gen = gen {
    yield 1;
    yield 2;
    yield 3;
};

// 异步生成器
let mut async_gen = async gen {
    yield 1;
    yield 2;
    yield 3;
};
```

#### let-else

```rust
// 2021
let Some(value) = some_option else {
    return;
};

// 2024
let Some(value) = some_option else {
    return;
};
// value 在后续代码中可用
```

#### 改进的模式匹配

```rust
// 2024
match value {
    Some(x) if x > 0 => println!("正数: {}", x),
    Some(x) => println!("非正数: {}", x),
    None => println!("无值"),
}
```

---

## 版本迁移

### 使用 cargo fix

```bash
# 自动迁移
cargo fix --edition

# 检查迁移
cargo fix --edition --check
```

### 手动迁移

1. 更新 `Cargo.toml` 中的 `edition`
2. 运行 `cargo fix --edition`
3. 手动修复无法自动迁移的代码
4. 运行测试确保功能正常

### 迁移注意事项

1. **依赖兼容性**：确保依赖支持新版本
2. **破坏性变更**：检查是否有破坏性变更
3. **测试覆盖**：迁移后运行完整测试
4. **逐步迁移**：可以逐个 crate 迁移

---

## 最佳实践

1. **使用最新稳定版本**：除非有特殊需求
2. **关注版本说明**：了解新特性和变更
3. **测试迁移**：迁移前确保测试通过
4. **逐步迁移**：大型项目可以分模块迁移

---

## 常见陷阱

1. **版本不匹配**：依赖版本可能与项目版本不兼容
2. **破坏性变更**：某些变更可能导致编译错误
3. **自动迁移不完整**：某些代码需要手动迁移
4. **依赖版本**：依赖可能需要更新以支持新版本
