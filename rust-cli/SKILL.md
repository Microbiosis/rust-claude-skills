---
name: rust-cli
description: "Rust CLI 工具开发技能。提供使用 Rust 开发命令行工具的完整参考，包括参数解析、错误处理、输出格式化、跨平台支持等。TRIGGER when: 用户开发 Rust CLI 工具、使用 clap/structopt、处理命令行参数、格式化输出、或需要了解 Rust CLI 最佳实践时触发。SKIP: 非 CLI 工具的 Rust 开发。"
---

# Rust CLI 工具开发技能

本技能提供使用 Rust 开发命令行工具的完整参考，基于 [Rust CLI Book](https://github.com/rust-cli/book)。

## 触发条件

当用户执行以下操作时触发此技能：
- 开发 Rust CLI 工具
- 使用 clap 或 structopt 解析参数
- 处理命令行输入输出
- 格式化 CLI 输出
- 了解 Rust CLI 最佳实践

## 跳过条件

以下情况不触发此技能：
- 非 CLI 工具的 Rust 开发
- 嵌入式 Rust 开发（使用 `rust-embedded` 技能）

---

## 项目结构

### 推荐结构

```
my-cli/
├── Cargo.toml
├── src/
│   ├── main.rs          # 入口点
│   ├── cli.rs           # CLI 参数定义
│   ├── config.rs        # 配置处理
│   ├── error.rs         # 错误类型
│   └── lib.rs           # 库代码
└── tests/
    └── integration.rs   # 集成测试
```

### Cargo.toml

```toml
[package]
name = "my-cli"
version = "0.1.0"
edition = "2021"

[dependencies]
clap = { version = "4", features = ["derive"] }
anyhow = "1"
serde = { version = "1", features = ["derive"] }
serde_json = "1"
colored = "2"
indicatif = "0.17"

[dev-dependencies]
assert_cmd = "2"
predicates = "3"
```

---

## 参数解析 (clap)

### 基本用法

```rust
use clap::Parser;

/// 一个简单的 CLI 工具
#[derive(Parser, Debug)]
#[command(author, version, about, long_about = None)]
struct Args {
    /// 输入文件
    #[arg(short, long)]
    input: String,

    /// 输出文件
    #[arg(short, long, default_value = "output.txt")]
    output: String,

    /// 详细输出
    #[arg(short, long, default_value_t = false)]
    verbose: bool,
}

fn main() {
    let args = Args::parse();
    println!("输入: {}", args.input);
    println!("输出: {}", args.output);
}
```

### 子命令

```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
#[command(author, version, about)]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    /// 添加项目
    Add {
        /// 项目名称
        name: String,
    },
    /// 删除项目
    Remove {
        /// 项目名称
        name: String,
        /// 强制删除
        #[arg(short, long)]
        force: bool,
    },
    /// 列出所有项目
    List,
}

fn main() {
    let cli = Cli::parse();

    match cli.command {
        Commands::Add { name } => {
            println!("添加: {}", name);
        }
        Commands::Remove { name, force } => {
            if force {
                println!("强制删除: {}", name);
            } else {
                println!("删除: {}", name);
            }
        }
        Commands::List => {
            println!("列出所有项目");
        }
    }
}
```

---

## 错误处理

### 使用 anyhow

```rust
use anyhow::{Context, Result};

fn read_file(path: &str) -> Result<String> {
    let content = std::fs::read_to_string(path)
        .with_context(|| format!("无法读取文件: {}", path))?;
    Ok(content)
}

fn main() -> Result<()> {
    let content = read_file("data.txt")?;
    println!("{}", content);
    Ok(())
}
```

### 自定义错误类型

```rust
use thiserror::Error;

#[derive(Error, Debug)]
enum AppError {
    #[error("IO 错误: {0}")]
    Io(#[from] std::io::Error),

    #[error("解析错误: {0}")]
    Parse(#[from] std::num::ParseIntError),

    #[error("自定义错误: {message}")]
    Custom { message: String },
}
```

---

## 输出格式化

### 彩色输出

```rust
use colored::*;

fn main() {
    println!("{}", "成功".green());
    println!("{}", "警告".yellow());
    println!("{}", "错误".red());
    println!("{}", "信息".blue());
}
```

### 进度条

```rust
use indicatif::ProgressBar;

fn main() {
    let pb = ProgressBar::new(100);
    for _ in 0..100 {
        pb.inc(1);
        std::thread::sleep(std::time::Duration::from_millis(50));
    }
    pb.finish_with_message("完成");
}
```

### 表格输出

```rust
use comfy_table::Table;

fn main() {
    let mut table = Table::new();
    table.set_header(vec!["名称", "版本", "描述"]);
    table.add_row(vec!["my-cli", "0.1.0", "一个 CLI 工具"]);
    println!("{}", table);
}
```

---

## 配置文件

### 读取配置

```rust
use serde::Deserialize;
use std::fs;

#[derive(Deserialize, Debug)]
struct Config {
    name: String,
    version: String,
    debug: bool,
}

fn load_config(path: &str) -> anyhow::Result<Config> {
    let content = fs::read_to_string(path)?;
    let config: Config = serde_json::from_str(&content)?;
    Ok(config)
}
```

### 环境变量

```rust
use std::env;

fn main() {
    let debug = env::var("DEBUG").unwrap_or_default() == "true";
    let port: u16 = env::var("PORT")
        .unwrap_or_else(|_| "8080".to_string())
        .parse()
        .expect("PORT 必须是数字");
}
```

---

## 文件与路径

### 路径处理

```rust
use std::path::PathBuf;

fn main() {
    let path = PathBuf::from("/tmp/test.txt");

    println!("文件名: {:?}", path.file_name());
    println!("扩展名: {:?}", path.extension());
    println!("父目录: {:?}", path.parent());

    // 构建路径
    let mut path = PathBuf::new();
    path.push("src");
    path.push("main.rs");
}
```

### 临时文件

```rust
use tempfile::NamedTempFile;

fn main() -> anyhow::Result<()> {
    let mut file = NamedTempFile::new()?;
    std::io::Write::write_all(&mut file, b"hello")?;

    let path = file.path();
    println!("临时文件: {:?}", path);

    Ok(())
}
```

---

## 跨平台支持

### 平台检测

```rust
use std::env;

fn main() {
    let os = env::consts::OS;
    let arch = env::consts::ARCH;

    println!("操作系统: {}", os);
    println!("架构: {}", arch);

    match os {
        "windows" => println!("Windows 平台"),
        "linux" => println!("Linux 平台"),
        "macos" => println!("macOS 平台"),
        _ => println!("其他平台"),
    }
}
```

### 路径分隔符

```rust
use std::path::{Path, MAIN_SEPARATOR};

fn main() {
    let path = format!("src{}main.rs", MAIN_SEPARATOR);
    println!("路径: {}", path);
}
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
}
```

### 集成测试

```rust
// tests/integration.rs
use assert_cmd::Command;
use predicates::prelude::*;

#[test]
fn test_cli() {
    Command::cargo_bin("my-cli")
        .unwrap()
        .args(&["--input", "test.txt"])
        .assert()
        .success()
        .stdout(predicate::str::contains("输入"));
}
```

---

## 发布

### 交叉编译

```bash
# 安装目标
rustup target add x86_64-unknown-linux-musl
rustup target add x86_64-pc-windows-gnu

# 编译
cargo build --release --target x86_64-unknown-linux-musl
cargo build --release --target x86_64-pc-windows-gnu
```

### CI/CD

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
      - run: cargo build --release
      - uses: actions/upload-artifact@v3
        with:
          name: binary
          path: target/release/my-cli
```

---

## 最佳实践

1. **使用 clap derive**：比 builder 模式更简洁
2. **使用 anyhow 处理错误**：简化错误传播
3. **提供有用的帮助信息**：使用 `///` 文档注释
4. **支持环境变量**：使用 `env` 参数
5. **提供彩色输出**：使用 colored 库
6. **显示进度**：使用 indicatif 库
7. **编写测试**：使用 assert_cmd 进行集成测试

---

## 常见陷阱

1. **错误处理**：不要使用 `unwrap()`，使用 `?` 或 `expect()`
2. **路径处理**：使用 `PathBuf` 而非字符串拼接
3. **跨平台**：注意路径分隔符和行尾符
4. **性能**：大文件使用流式处理
5. **用户体验**：提供清晰的错误信息和帮助
