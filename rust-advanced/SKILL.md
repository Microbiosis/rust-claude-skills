---
name: rust-advanced
description: "Rust 高级特性技能。提供 Rust 高级特性的完整参考，包括 unsafe Rust、高级 trait、高级类型、宏、FFI 等。TRIGGER when: 用户使用 unsafe 代码、实现高级 trait、使用高级类型、编写宏、或需要了解 Rust 高级特性时触发。SKIP: 基础 Rust 开发。"
---

# Rust 高级特性技能

本技能提供 Rust 高级特性的完整参考，基于 [Rustonomicon](https://github.com/rust-lang/nomicon) 和 [Rust Reference](https://github.com/rust-lang/reference)。

## 触发条件

当用户执行以下操作时触发此技能：
- 使用 unsafe 代码
- 实现高级 trait
- 使用高级类型
- 编写宏
- 进行 FFI 互操作

## 跳过条件

以下情况不触发此技能：
- 基础 Rust 开发（使用 `rust-language` 技能）

---

## unsafe Rust

### unsafe 块

```rust
fn main() {
    let mut num = 5;
    let r1 = &num as *const i32;
    let r2 = &mut num as *mut i32;

    unsafe {
        println!("r1 is: {}", *r1);
        println!("r2 is: {}", *r2);
    }
}
```

### unsafe 函数

```rust
unsafe fn dangerous() {
    // 危险操作
}

fn main() {
    unsafe {
        dangerous();
    }
}
```

### unsafe trait

```rust
unsafe trait Foo {
    fn dangerous();
}

unsafe impl Foo for i32 {
    fn dangerous() {
        println!("dangerous");
    }
}
```

---

## 高级 Trait

### 关联类型

```rust
trait Graph {
    type N;
    type E;

    fn has_edge(&self, &Self::N, &Self::N) -> bool;
    fn edges(&self, &Self::N) -> Vec<Self::E>;
}

struct Node;
struct Edge;

struct MyGraph;

impl Graph for MyGraph {
    type N = Node;
    type E = Edge;

    fn has_edge(&self, n1: &Node, n2: &Node) -> bool {
        false
    }

    fn edges(&self, n: &Node) -> Vec<Edge> {
        Vec::new()
    }
}
```

### 默认泛型参数

```rust
trait Add<Rhs = Self> {
    type Output;

    fn add(self, rhs: Rhs) -> Self::Output;
}

#[derive(Debug, Copy, Clone)]
struct Point {
    x: f64,
    y: f64,
}

impl Add for Point {
    type Output = Point;

    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}
```

### 完全限定语法

```rust
trait Animal {
    fn baby_name() -> String;
}

struct Dog;

impl Dog {
    fn baby_name() -> String {
        String::from("Spot")
    }
}

impl Animal for Dog {
    fn baby_name() -> String {
        String::from("puppy")
    }
}

fn main() {
    println!("A baby dog is called a {}", <Dog as Animal>::baby_name());
}
```

### Supertraits

```rust
use std::fmt;

trait OutlinePrint: fmt::Display {
    fn outline_print(&self) {
        let output = self.to_string();
        let len = output.len();
        println!("{}", "*".repeat(len + 4));
        println!("*{}*", " ".repeat(len + 2));
        println!("* {} *", output);
        println!("*{}*", " ".repeat(len + 2));
        println!("{}", "*".repeat(len + 4));
    }
}
```

---

## 高级类型

### 类型别名

```rust
type Kilometers = i32;
type Thunk = Box<dyn Fn() + Send + 'static>;

fn takes_long_type(f: Thunk) {
    // ...
}

fn returns_long_type() -> Thunk {
    Box::new(|| println!("hi"))
}
```

### Never 类型

```rust
fn bar() -> ! {
    // 永不返回的函数
    loop {}
}
```

### 动态大小类型

```rust
fn generic<T: ?Sized>(t: &T) {
    // T 可能是动态大小类型
}
```

---

## 宏

### 声明宏

```rust
macro_rules! vec {
    ( $( $x:expr ),* ) => {
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
}
```

### 过程宏

```rust
// derive 宏
use proc_macro::TokenStream;

#[proc_macro_derive(HelloMacro)]
pub fn hello_macro_derive(input: TokenStream) -> TokenStream {
    // 实现
}
```

---

## FFI

### 调用 C 函数

```rust
extern "C" {
    fn abs(input: i32) -> i32;
}

fn main() {
    unsafe {
        println!("Absolute value of -3 according to C: {}", abs(-3));
    }
}
```

### 导出函数给 C

```rust
#[no_mangle]
pub extern "C" fn call_from_c() {
    println!("Just called a Rust function from C!");
}
```

### 使用 libc

```rust
use libc;

fn main() {
    unsafe {
        let s = b"Hello, world!\0";
        libc::printf(s.as_ptr() as *const libc::c_char);
    }
}
```

---

## 内存布局

### repr 属性

```rust
#[repr(C)]
struct CStyle {
    a: u8,
    b: u32,
    c: u8,
}

#[repr(packed)]
struct Packed {
    a: u8,
    b: u32,
    c: u8,
}
```

### 内存对齐

```rust
use std::mem;

fn main() {
    println!("Alignment of u8: {}", mem::align_of::<u8>());
    println!("Alignment of u32: {}", mem::align_of::<u32>());
    println!("Size of u8: {}", mem::size_of::<u8>());
    println!("Size of u32: {}", mem::size_of::<u32>());
}
```

---

## 并发原语

### 原子操作

```rust
use std::sync::atomic::{AtomicUsize, Ordering};

static COUNTER: AtomicUsize = AtomicUsize::new(0);

fn main() {
    COUNTER.fetch_add(1, Ordering::SeqCst);
    println!("Counter: {}", COUNTER.load(Ordering::SeqCst));
}
```

### 内存顺序

```rust
use std::sync::atomic::{AtomicBool, Ordering};

static FLAG: AtomicBool = AtomicBool::new(false);

fn main() {
    // Relaxed: 不保证顺序
    FLAG.store(true, Ordering::Relaxed);

    // Acquire: 保证后续操作在加载后执行
    if FLAG.load(Ordering::Acquire) {
        println!("Flag is set");
    }

    // Release: 保证之前的操作在存储前执行
    FLAG.store(false, Ordering::Release);
}
```

---

## 最佳实践

1. **最小化 unsafe 代码**：只在必要时使用 unsafe
2. **封装 unsafe 代码**：提供安全的抽象接口
3. **文档化 unsafe 代码**：说明为什么需要 unsafe
4. **测试 unsafe 代码**：特别关注边界情况
5. **使用 Miri**：检测未定义行为

---

## 常见陷阱

1. **悬垂指针**：确保引用的有效性
2. **数据竞争**：使用正确的同步原语
3. **内存泄漏**：注意循环引用
4. **未定义行为**：遵循 Rust 的安全规则
5. **ABI 兼容性**：注意 C 和 Rust 的类型差异
