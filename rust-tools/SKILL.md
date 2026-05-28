---
name: rust-tools
description: "Rust 工具链技能。提供 Rust 工具链的完整参考，包括 Cargo、Rustdoc、Rustup、Clippy、Rustfmt 等。TRIGGER when: 用户使用 Cargo 命令、生成文档、配置 Rust 工具链、运行 Clippy 检查、格式化代码、或需要了解 Rust 工具链时触发。SKIP: 纯 Rust 代码编写。"
---

# Rust 工具链技能

本技能提供 Rust 工具链的完整参考，包括 Cargo、Rustdoc、Rustup、Clippy、Rustfmt 等。

## 触发条件

当用户执行以下操作时触发此技能：
- 使用 Cargo 命令
- 生成 Rust 文档
- 配置 Rust 工具链
- 运行 Clippy 检查
- 格式化代码

## 跳过条件

以下情况不触发此技能：
- 纯 Rust 代码编写（使用 `rust-language` 技能）

---

## Cargo

### 项目管理

```bash
# 创建新项目
cargo new my-project          # 二进制项目
cargo new my-lib --lib        # 库项目

# 构建
cargo build                   # 调试构建
cargo build --release         # 发布构建

# 运行
cargo run                     # 运行二进制项目
cargo run -- arg1 arg2        # 传递参数

# 检查
cargo check                   # 检查代码，不生成二进制文件

# 清理
cargo clean                   # 清理构建产物
```

### 依赖管理

```toml
# Cargo.toml
[dependencies]
serde = "1.0"
serde_json = "1.0"
tokio = { version = "1", features = ["full"] }

[dev-dependencies]
assert_cmd = "2"
predicates = "3"

[build-dependencies]
cc = "1"
```

```bash
# 添加依赖
cargo add serde
cargo add serde --features derive
cargo add tokio --features full

# 更新依赖
cargo update

# 查看依赖树
cargo tree
```

### 工作空间

```toml
# 根 Cargo.toml
[workspace]
members = [
    "member1",
    "member2",
]

[workspace.dependencies]
serde = "1.0"
```

### 特性

```toml
[features]
default = ["std"]
std = []
alloc = []
full = ["std", "alloc"]
```

```bash
# 使用特性
cargo build --features full
cargo build --no-default-features --features alloc
```

---

## Rustdoc

### 文档注释

```rust
/// 计算两个数的和
///
/// # 参数
///
/// * `a` - 第一个数字
/// * `b` - 第二个数字
///
/// # 返回值
///
/// 返回两个数的和
///
/// # 示例
///
/// ```
/// let result = add(1, 2);
/// assert_eq!(result, 3);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

### 文档属性

```rust
#![warn(missing_docs)]
#![deny(missing_docs)]

/// 这是模块级文档
pub mod my_module {
    //! 模块内部文档
}
```

### 生成文档

```bash
# 生成文档
cargo doc

# 生成并打开文档
cargo doc --open

# 生成私有项文档
cargo doc --document-private-items

# 检查文档链接
cargo doc --check
```

### 文档测试

```rust
/// # Examples
///
/// ```
/// use my_lib::add;
///
/// let result = add(2, 3);
/// assert_eq!(result, 5);
/// ```
///
/// ```should_panic
/// panic!("This will panic");
/// ```
///
/// ```no_run
/// // 这个示例不会运行
/// loop {}
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

---

## Rustup

### 工具链管理

```bash
# 查看已安装工具链
rustup toolchain list

# 安装工具链
rustup toolchain install stable
rustup toolchain install nightly
rustup toolchain install 1.70.0

# 设置默认工具链
rustup default stable

# 更新工具链
rustup update

# 卸载工具链
rustup toolchain uninstall nightly
```

### 目标管理

```bash
# 查看已安装目标
rustup target list --installed

# 安装目标
rustup target add thumbv7em-none-eabihf
rustup target add wasm32-unknown-unknown

# 卸载目标
rustup target remove thumbv7em-none-eabihf
```

### 组件管理

```bash
# 查看已安装组件
rustup component list --installed

# 安装组件
rustup component add rust-src
rustup component add rust-analyzer
rustup component add clippy
rustup component add rustfmt

# 卸载组件
rustup component remove rust-src
```

---

## Clippy

### 基本用法

```bash
# 运行 Clippy
cargo clippy

# 运行 Clippy 并修复
cargo clippy --fix

# 运行所有 Clippy lint
cargo clippy -- -W clippy::all

# 运行特定 lint
cargo clippy -- -W clippy::pedantic
```

### 配置 Clippy

```toml
# .clippy.toml
msrv = "1.70"
avoid-breaking-exported-api = true
```

```rust
// 在代码中配置
#![allow(clippy::too_many_arguments)]
#![deny(clippy::unwrap_used)]
#![warn(clippy::nursery)]
```

### 常见 lint

```rust
// clippy::all
let x = &vec![1, 2, 3];  // 警告：不需要引用
let x = vec![1, 2, 3];

// clippy::pedantic
fn foo(x: &str) -> &str {  // 警告：生命周期可以省略
    x
}

// clippy::nursery
let x = if condition { 1 } else { 2 };  // 警告：可以使用 match
```

---

## Rustfmt

### 基本用法

```bash
# 格式化代码
cargo fmt

# 检查格式
cargo fmt -- --check

# 格式化特定文件
rustfmt src/main.rs
```

### 配置 Rustfmt

```toml
# rustfmt.toml
edition = "2021"
max_width = 100
tab_spaces = 4
use_small_heuristics = "Default"
```

### 常见配置

```toml
# rustfmt.toml
edition = "2021"
max_width = 100
tab_spaces = 4
use_small_heuristics = "Default"
imports_granularity = "Crate"
group_imports = "StdExternalCrate"
```

---

## 测试

### 单元测试

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(1, 2), 3);
    }

    #[test]
    #[should_panic(expected = "divide by zero")]
    fn test_divide_by_zero() {
        divide(1, 0);
    }

    #[test]
    fn test_with_result() -> Result<(), String> {
        let result = add(1, 2);
        if result == 3 {
            Ok(())
        } else {
            Err(String::from("错误"))
        }
    }
}
```

### 集成测试

```rust
// tests/integration_test.rs
use my_lib::add;

#[test]
fn test_add_integration() {
    assert_eq!(add(1, 2), 3);
}
```

### 测试命令

```bash
# 运行所有测试
cargo test

# 运行特定测试
cargo test test_add

# 运行特定模块的测试
cargo test tests::

# 并行运行测试
cargo test -- --test-threads=4

# 显示输出
cargo test -- --nocapture
```

---

## 基准测试

### 使用 criterion

```rust
use criterion::{black_box, criterion_group, criterion_main, Criterion};

fn fibonacci(n: u64) -> u64 {
    match n {
        0 => 1,
        1 => 1,
        n => fibonacci(n - 1) + fibonacci(n - 2),
    }
}

fn criterion_benchmark(c: &mut Criterion) {
    c.bench_function("fib 20", |b| b.iter(|| fibonacci(black_box(20))));
}

criterion_group!(benches, criterion_benchmark);
criterion_main!(benches);
```

### 运行基准测试

```bash
cargo bench
```

---

## 构建脚本

### build.rs

```rust
fn main() {
    // 设置环境变量
    println!("cargo:rustc-env=VERSION=1.0.0");

    // 重新运行条件
    println!("cargo:rerun-if-changed=build.rs");
    println!("cargo:rerun-if-env-changed=VERSION");

    // 编译 C 代码
    cc::Build::new()
        .file("src/helper.c")
        .compile("helper");
}
```

---

## 最佳实践

1. **使用 Cargo workspace**：管理多个相关项目
2. **配置 Clippy**：保持代码质量
3. **配置 Rustfmt**：保持代码风格一致
4. **编写文档**：使用 `cargo doc` 生成文档
5. **编写测试**：单元测试和集成测试
6. **使用基准测试**：优化性能

---

## 常见陷阱

1. **依赖版本冲突**：使用 `cargo tree` 检查
2. **构建缓存**：使用 `cargo clean` 清理
3. **特性冲突**：注意特性依赖关系
4. **测试并行**：确保测试独立
5. **文档链接**：使用 `cargo doc --check` 检查
