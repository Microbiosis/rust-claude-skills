---
name: rust-std
description: "Rust 标准库技能。提供 Rust 标准库的完整参考，包括常用模块、类型、trait、函数等。TRIGGER when: 用户使用标准库功能、查询标准库 API、需要标准库代码示例、或需要了解标准库最佳实践时触发。SKIP: 非标准库相关的 Rust 开发。"
---

# Rust 标准库技能

本技能提供 Rust 标准库的完整参考，基于 [Standard Library Documentation](https://doc.rust-lang.org/std)。

## 触发条件

当用户执行以下操作时触发此技能：
- 使用标准库功能
- 查询标准库 API
- 需要标准库代码示例
- 了解标准库最佳实践

## 跳过条件

以下情况不触发此技能：
- 非标准库相关的 Rust 开发

---

## 常用模块

### std::collections

```rust
use std::collections::{HashMap, HashSet, BTreeMap, BTreeSet, VecDeque, LinkedList};

// HashMap
let mut map = HashMap::new();
map.insert("key", "value");
let value = map.get("key");

// HashSet
let mut set = HashSet::new();
set.insert(1);
set.contains(&1);

// BTreeMap (有序)
let mut btree = BTreeMap::new();
btree.insert(1, "a");
btree.insert(2, "b");

// VecDeque (双端队列)
let mut deque = VecDeque::new();
deque.push_back(1);
deque.push_front(2);
deque.pop_front();
```

### std::fs

```rust
use std::fs::{self, File, OpenOptions};
use std::io::{self, Read, Write, BufReader, BufWriter};

// 读取文件
let content = fs::read_to_string("file.txt")?;

// 写入文件
fs::write("file.txt", "content")?;

// 逐行读取
let file = File::open("file.txt")?;
let reader = BufReader::new(file);
for line in reader.lines() {
    println!("{}", line?);
}

// 追加写入
let mut file = OpenOptions::new()
    .append(true)
    .open("file.txt")?;
writeln!(file, "new line")?;

// 创建目录
fs::create_dir_all("path/to/dir")?;

// 遍历目录
for entry in fs::read_dir(".")? {
    let entry = entry?;
    println!("{}", entry.path().display());
}
```

### std::path

```rust
use std::path::{Path, PathBuf};

let path = Path::new("/tmp/file.txt");

// 路径组件
println!("文件名: {:?}", path.file_name());
println!("扩展名: {:?}", path.extension());
println!("父目录: {:?}", path.parent());
println!("是否存在: {}", path.exists());
println!("是否文件: {}", path.is_file());
println!("是否目录: {}", path.is_dir());

// 构建路径
let mut path = PathBuf::new();
path.push("src");
path.push("main.rs");

// 拼接路径
let path = Path::new("/tmp").join("file.txt");
```

### std::io

```rust
use std::io::{self, Read, Write, BufRead, BufReader, BufWriter, stdin, stdout};

// 从 stdin 读取
let mut input = String::new();
stdin().read_line(&mut input)?;

// 写入 stdout
stdout().write_all(b"hello")?;

// 带缓冲的读写
let reader = BufReader::new(stdin());
let writer = BufWriter::new(stdout());

// 读取所有内容
let mut content = String::new();
File::open("file.txt")?.read_to_string(&mut content)?;
```

### std::thread

```rust
use std::thread;
use std::time::Duration;

// 创建线程
let handle = thread::spawn(|| {
    for i in 1..10 {
        println!("hi number {} from the spawned thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
});

handle.join().unwrap();

// 命名线程
let builder = thread::Builder::new()
    .name("worker".to_string())
    .stack_size(32 * 1024);

let handle = builder.spawn(|| {
    println!("Thread name: {:?}", thread::current().name());
}).unwrap();
```

### std::sync

```rust
use std::sync::{Arc, Mutex, RwLock, mpsc, Barrier};

// Arc + Mutex
let counter = Arc::new(Mutex::new(0));
let counter_clone = Arc::clone(&counter);

let handle = thread::spawn(move || {
    let mut num = counter_clone.lock().unwrap();
    *num += 1;
});

// RwLock (读写锁)
let lock = Arc::new(RwLock::new(5));
let lock_clone = Arc::clone(&lock);

// 多个读取者
let r1 = lock.read().unwrap();
let r2 = lock.read().unwrap();

// 单个写入者
let mut w = lock.write().unwrap();
*w += 1;

// 消息通道
let (tx, rx) = mpsc::channel();
thread::spawn(move || {
    tx.send("hello").unwrap();
});
let received = rx.recv().unwrap();
```

### std::time

```rust
use std::time::{Duration, Instant, SystemTime};

// Instant (单调时钟)
let start = Instant::now();
// ... 某些操作 ...
let elapsed = start.elapsed();
println!("耗时: {:?}", elapsed);

// Duration
let five_seconds = Duration::from_secs(5);
let ten_millis = Duration::from_millis(10);
thread::sleep(five_seconds);

// SystemTime (系统时间)
let now = SystemTime::now();
let since_epoch = now.duration_since(SystemTime::UNIX_EPOCH)?;
println!("自 Unix 纪元以来: {} 秒", since_epoch.as_secs());
```

### std::env

```rust
use std::env;

// 获取环境变量
let path = env::var("PATH")?;
let home = env::var("HOME").unwrap_or_default();

// 设置环境变量
env::set_var("MY_VAR", "value");

// 命令行参数
let args: Vec<String> = env::args().collect();
for arg in args {
    println!("{}", arg);
}

// 当前目录
let current_dir = env::current_dir()?;

// 临时目录
let temp_dir = env::temp_dir();
```

---

## 常用类型

### String vs &str

```rust
// String (拥有所有权)
let mut s = String::new();
let s = String::from("hello");
let s = "hello".to_string();
s.push_str(" world");
s.push('!');

// &str (字符串切片，借用)
let s: &str = "hello";
let s: &str = &string[..];

// 转换
let s: String = "hello".to_string();
let s: &str = &string;
```

### Vec

```rust
let mut v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];

v.push(4);
v.pop();
v.insert(0, 0);
v.remove(0);
v.retain(|&x| x > 2);

// 排序
v.sort();
v.sort_by(|a, b| b.cmp(a));  // 降序
v.sort_unstable();  // 更快但不稳定

// 搜索
let pos = v.binary_search(&3);
let pos = v.iter().position(|&x| x == 3);

// 切片
let slice = &v[1..3];
```

### Option 和 Result

```rust
// Option
let some: Option<i32> = Some(42);
let none: Option<i32> = None;

let value = some.unwrap_or(0);
let value = some.unwrap_or_default();
let value = some.unwrap_or_else(|| expensive_computation());
let value = some.expect("值不存在");

// Result
let ok: Result<i32, String> = Ok(42);
let err: Result<i32, String> = Err("error".to_string());

let value = ok.unwrap_or(0);
let value = ok.unwrap_or_default();
let value = ok.expect("值不存在");

// 组合子
let mapped = some.map(|x| x * 2);
let filtered = some.filter(|&x| x > 10);
let flatmapped = some.and_then(|x| Some(x * 2));
```

### Iterator

```rust
let v = vec![1, 2, 3, 4, 5];

// 迭代器适配器
let doubled: Vec<i32> = v.iter().map(|x| x * 2).collect();
let evens: Vec<&i32> = v.iter().filter(|&&x| x % 2 == 0).collect();
let sum: i32 = v.iter().sum();
let product: i32 = v.iter().product();
let max = v.iter().max();
let min = v.iter().min();
let count = v.iter().count();

// 链式调用
let result: i32 = v.iter()
    .filter(|&&x| x % 2 == 0)
    .map(|&x| x * 2)
    .sum();

// enumerate
for (i, val) in v.iter().enumerate() {
    println!("{}: {}", i, val);
}

// zip
let names = vec!["Alice", "Bob"];
let ages = vec![25, 30];
let people: Vec<_> = names.iter().zip(ages.iter()).collect();
```

---

## 常用 Trait

### Display 和 Debug

```rust
use std::fmt;

struct Point {
    x: f64,
    y: f64,
}

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

impl fmt::Debug for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        f.debug_struct("Point")
            .field("x", &self.x)
            .field("y", &self.y)
            .finish()
    }
}

let p = Point { x: 1.0, y: 2.0 };
println!("{}", p);  // Display: (1, 2)
println!("{:?}", p);  // Debug: Point { x: 1.0, y: 2.0 }
```

### Clone 和 Copy

```rust
#[derive(Clone, Copy)]
struct Point {
    x: f64,
    y: f64,
}

let p1 = Point { x: 1.0, y: 2.0 };
let p2 = p1;  // Copy，p1 仍然有效
let p3 = p1.clone();  // Clone
```

### From 和 Into

```rust
struct Celsius(f64);
struct Fahrenheit(f64);

impl From<Celsius> for Fahrenheit {
    fn from(c: Celsius) -> Self {
        Fahrenheit(c.0 * 9.0 / 5.0 + 32.0)
    }
}

let c = Celsius(100.0);
let f: Fahrenheit = c.into();
let f = Fahrenheit::from(c);
```

### Default

```rust
#[derive(Default)]
struct Config {
    width: u32,
    height: u32,
    fullscreen: bool,
}

let config = Config::default();
let config = Config {
    width: 1920,
    ..Default::default()
};
```

---

## 最佳实践

1. **使用 `&str` 而非 `String`**：作为函数参数时更灵活
2. **使用 `Vec<T>` 而非数组**：动态大小更实用
3. **使用 `Option` 和 `Result`**：而非 `null` 或 `panic`
4. **使用迭代器**：比手动循环更简洁高效
5. **使用 `impl Trait`**：作为返回类型避免装箱
6. **使用 `Cow<str>`**：避免不必要的字符串克隆

---

## 常见陷阱

1. **字符串编码**：Rust 字符串是 UTF-8，不能直接索引
2. **所有权转移**：注意函数参数的所有权
3. **借用检查器**：理解可变和不可变借用的规则
4. **迭代器惰性**：迭代器是惰性的，需要消费才会执行
5. **锁的死锁**：注意 Mutex/RwLock 的使用顺序
