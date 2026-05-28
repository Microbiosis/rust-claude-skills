---
name: rust-macros
description: "Rust 宏技能。提供 Rust 宏的完整参考，包括声明宏、过程宏、宏规则等。TRIGGER when: 用户编写宏、使用宏、调试宏、或需要了解宏系统时触发。SKIP: 非宏相关的 Rust 开发。"
---

# Rust 宏技能

本技能提供 Rust 宏的完整参考，包括声明宏、过程宏、宏规则等。

## 触发条件

当用户执行以下操作时触发此技能：
- 编写宏
- 使用宏
- 调试宏
- 了解宏系统

## 跳过条件

以下情况不触发此技能：
- 非宏相关的 Rust 开发

---

## 声明宏

### 基本语法

```rust
macro_rules! say_hello {
    // 无参数
    () => {
        println!("Hello!");
    };
}

// 调用
say_hello!();
```

### 参数匹配

```rust
macro_rules! create_function {
    // 匹配标识符
    ($func_name:ident) => {
        fn $func_name() {
            println!("函数 {} 被调用", stringify!($func_name));
        }
    };
}

create_function!(foo);
create_function!(bar);

fn main() {
    foo();
    bar();
}
```

### 表达式匹配

```rust
macro_rules! calculate {
    // 匹配表达式
    ($a:expr, $b:expr, $op:expr) => {
        match $op {
            "+" => $a + $b,
            "-" => $a - $b,
            "*" => $a * $b,
            "/" => $a / $b,
            _ => panic!("未知运算符"),
        }
    };
}

fn main() {
    let result = calculate!(10, 5, "+");
    println!("10 + 5 = {}", result);
}
```

### 重复模式

```rust
macro_rules! vec {
    // 匹配重复的表达式
    ($($x:expr),*) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
    // 匹配重复的表达式，带分隔符
    ($($x:expr),+ $(,)?) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}

fn main() {
    let v = vec![1, 2, 3];
    let v = vec![1, 2, 3,];  // 尾逗号
}
```

### 多分支宏

```rust
macro_rules! my_macro {
    // 分支 1：无参数
    () => {
        println!("无参数");
    };
    // 分支 2：一个参数
    ($x:expr) => {
        println!("一个参数: {}", $x);
    };
    // 分支 3：两个参数
    ($x:expr, $y:expr) => {
        println!("两个参数: {} 和 {}", $x, $y);
    };
    // 分支 4：重复参数
    ($($x:expr),+) => {
        $(
            println!("参数: {}", $x);
        )+
    };
}

fn main() {
    my_macro!();
    my_macro!(42);
    my_macro!(1, 2);
    my_macro!(1, 2, 3, 4, 5);
}
```

---

## 过程宏

### 派生宏

```rust
use proc_macro::TokenStream;
use quote::quote;
use syn::{parse_macro_input, DeriveInput};

#[proc_macro_derive(MyMacro)]
pub fn my_macro_derive(input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as DeriveInput);
    let name = &input.ident;

    let expanded = quote! {
        impl MyTrait for #name {
            fn my_method(&self) -> String {
                format!("{} 实现了 MyTrait", stringify!(#name))
            }
        }
    };

    TokenStream::from(expanded)
}
```

### 属性宏

```rust
use proc_macro::TokenStream;
use quote::quote;
use syn::{parse_macro_input, ItemFn};

#[proc_macro_attribute]
pub fn my_attribute(args: TokenStream, input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as ItemFn);
    let name = &input.sig.ident;
    let block = &input.block;

    let expanded = quote! {
        fn #name() {
            println!("函数 {} 被调用", stringify!(#name));
            #block
        }
    };

    TokenStream::from(expanded)
}
```

### 函数式宏

```rust
use proc_macro::TokenStream;
use quote::quote;
use syn::{parse_macro_input, LitStr};

#[proc_macro]
pub fn my_macro(input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as LitStr);
    let value = input.value();

    let expanded = quote! {
        println!("宏输出: {}", #value);
    };

    TokenStream::from(expanded)
}
```

---

## 宏规则

### 匹配器

```rust
macro_rules! matchers {
    // 匹配标识符
    ($i:ident) => { stringify!($i) };
    // 匹配表达式
    ($e:expr) => { $e };
    // 匹配类型
    ($t:ty) => { stringify!($t) };
    // 匹配字面量
    ($l:literal) => { $l };
    // 匹配模式
    ($p:pat) => { stringify!($p) };
    // 匹配语句
    ($s:stmt) => { stringify!($s) };
    // 匹配块
    ($b:block) => { stringify!($b) };
    // 匹配项
    ($i:item) => { stringify!($i) };
    // 匹配元
    ($m:meta) => { stringify!($m) };
    // 匹配生命期
    ($l:lifetime) => { stringify!($l) };
    // 匹配 tt
    ($t:tt) => { stringify!($t) };
}
```

### 重复操作符

```rust
macro_rules! repeat {
    // * 零次或多次
    ($($x:expr),*) => {
        vec![$($x),*]
    };
    // + 一次或多次
    ($($x:expr),+) => {
        vec![$($x),+]
    };
    // ? 零次或一次
    ($x:expr $(, $y:expr)?) => {
        ($x, $($y)?)
    };
}
```

### 宏导出

```rust
#[macro_export]
macro_rules! my_macro {
    () => {
        println!("导出的宏");
    };
}
```

---

## 内置宏

### 调试宏

```rust
// println!
println!("Hello, {}!", "world");
println!("{:?}", vec![1, 2, 3]);
println!("{:#?}", vec![1, 2, 3]);

// eprintln!
eprintln!("错误: {}", "文件未找到");

// dbg!
let x = 5;
let y = dbg!(x * 2);  // 打印表达式和值

// todo!
fn not_implemented() -> i32 {
    todo!("这个函数还没实现");
}

// unimplemented!
fn not_implemented() -> i32 {
    unimplemented!();
}

// unreachable!
fn unreachable() {
    unreachable!("不应该到达这里");
}

// panic!
panic!("发生错误");
panic!("错误: {}", "文件未找到");
```

### 类型宏

```rust
// stringify!
let s = stringify!(hello world);

// concat!
let s = concat!("Hello", ", ", "world!",);

// stringify!
let name = stringify!(my_function);

// file!()
println!("文件: {}", file!());

// line!()
println!("行号: {}", line!());

// column!()
println!("列号: {}", column!());

// module_path!()
println!("模块路径: {}", module_path!());
```

### 编译宏

```rust
// cfg!
if cfg!(target_os = "linux") {
    println!("Linux 平台");
}

// env!
let version = env!("CARGO_PKG_VERSION");

// option_env!
let debug = option_env!("DEBUG");

// include_str!
let content = include_str!("data.txt");

// include_bytes!
let data = include_bytes!("data.bin");

// compile_error!
#[cfg(not(target_os = "linux"))]
compile_error!("这个库只支持 Linux");
```

---

## 宏最佳实践

1. **命名规范**：使用下划线命名宏
2. **导出宏**：使用 `#[macro_export]`
3. **文档注释**：为宏添加文档
4. **测试宏**：编写测试确保宏正确工作
5. **调试宏**：使用 `cargo expand` 查看宏展开

---

## 常见陷阱

1. **卫生性**：宏中的变量不会污染外部作用域
2. **重复匹配**：注意重复模式的匹配规则
3. **优先级**：宏展开可能影响运算符优先级
4. **错误信息**：宏错误可能难以调试
5. **性能**：宏可能增加编译时间

---

## 调试宏

### cargo expand

```bash
# 安装
cargo install cargo-expand

# 展开宏
cargo expand

# 展开特定模块
cargo expand my_module
```

### 编译器调试

```bash
# 查看宏展开
RUSTFLAGS=-Zmacro-backtrace cargo build

# 打印宏展开
#[macro_export]
macro_rules! debug_macro {
    ($($tt:tt)*) => {
        eprintln!("宏输入: {}", stringify!($($tt)*));
        $($tt)*
    };
}
```
