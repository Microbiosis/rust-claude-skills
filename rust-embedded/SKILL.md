---
name: rust-embedded
description: "Rust 嵌入式开发技能。提供使用 Rust 进行嵌入式开发的完整参考，包括 no_std 环境、HAL 层、外设驱动、中断处理、实时系统等。TRIGGER when: 用户开发嵌入式 Rust 程序、使用 cortex-m/esp-hal、配置 no_std 环境、处理硬件外设、或需要了解 Rust 嵌入式最佳实践时触发。SKIP: 非嵌入式 Rust 开发。"
---

# Rust 嵌入式开发技能

本技能提供使用 Rust 进行嵌入式开发的完整参考，基于 [Embedded Rust Book](https://github.com/rust-embedded/book)。

## 触发条件

当用户执行以下操作时触发此技能：
- 开发嵌入式 Rust 程序
- 使用 cortex-m 或 esp-hal
- 配置 no_std 环境
- 处理硬件外设
- 了解 Rust 嵌入式最佳实践

## 跳过条件

以下情况不触发此技能：
- 非嵌入式 Rust 开发
- CLI 工具开发（使用 `rust-cli` 技能）

---

## 项目结构

### 推荐结构

```
my-embedded/
├── Cargo.toml
├── .cargo/
│   └── config.toml      # 目标配置
├── src/
│   ├── main.rs          # 入口点
│   ├── lib.rs           # 库代码
│   └── hal/             # 硬件抽象层
└── build.rs             # 构建脚本
```

### Cargo.toml

```toml
[package]
name = "my-embedded"
version = "0.1.0"
edition = "2021"

[dependencies]
cortex-m = "0.7"
cortex-m-rt = "0.7"
cortex-m-semihosting = "0.5"
panic-halt = "0.2"

# 对于 ESP32
# esp-hal = { version = "0.14", features = ["esp32c3"] }
# esp-backtrace = { version = "0.10", features = ["esp32c3", "panic-handler", "exception-handler"] }
# esp-println = { version = "0.8", features = ["esp32c3"] }

[profile.release]
opt-level = "z"
lto = true
codegen-units = 1
```

### .cargo/config.toml

```toml
[build]
target = "thumbv7em-none-eabihf"  # Cortex-M4F

[target.'cfg(all(target_arch = "arm", target_os = "none"))']
runner = "probe-rs run --chip STM32F411CETx"
```

---

## no_std 环境

### 基本设置

```rust
#![no_std]
#![no_main]

use cortex_m_rt::entry;
use panic_halt as _;

#[entry]
fn main() -> ! {
    loop {
        // 主循环
    }
}
```

### 内存分配

```rust
#![no_std]

extern crate alloc;

use alloc::vec::Vec;
use alloc::string::String;

// 需要提供全局分配器
use cortex_m_rt::heap::{Heap, init as heap_init};

#[global_allocator]
static HEAP: Heap = Heap::empty();
```

---

## 硬件抽象层 (HAL)

### GPIO 操作

```rust
use cortex_m::peripheral::Peripherals;
use hal::prelude::*;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let mut gpioa = dp.GPIOA.split();

    // 配置为输出
    let mut led = gpioa.pa5.into_push_pull_output(&mut gpioa.moder, &mut gpioa.otyper);

    loop {
        led.set_high();
        cortex_m::asm::delay(8_000_000);
        led.set_low();
        cortex_m::asm::delay(8_000_000);
    }
}
```

### 定时器

```rust
use cortex_m::peripheral::Peripherals;
use hal::timer::Timer;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let mut timer = Timer::tim1(dp.TIM, 1.hz(), &mut rcc.apb2);

    loop {
        timer.wait().unwrap();
        // 每秒执行一次
    }
}
```

---

## 中断处理

### 中断配置

```rust
use cortex_m::peripheral::Peripherals;
use cortex_m_rt::interrupt;

#[interrupt]
fn TIM2() {
    // 中断处理代码
}

fn main() -> ! {
    let dp = Peripherals::take().unwrap();

    // 使能中断
    dp.NVIC.enable(interrupt::TIM2);

    loop {}
}
```

### 临界区

```rust
use cortex_m::interrupt;

fn critical_section_example() {
    interrupt::free(|cs| {
        // 在临界区内访问共享资源
        let mut shared = SHARED.borrow(cs).borrow_mut();
        *shared += 1;
    });
}
```

---

## 通信协议

### UART

```rust
use hal::serial::Serial;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let serial = Serial::usart2(
        dp.USART2,
        (tx, rx),
        9600.bps(),
        &mut rcc.apb1,
    );

    let (mut tx, mut rx) = serial.split();

    // 发送数据
    tx.write(b'H').unwrap();

    // 接收数据
    let data = nb::block!(rx.read()).unwrap();
}
```

### I2C

```rust
use hal::i2c::I2c;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let i2c = I2c::i2c1(
        dp.I2C1,
        (scl, sda),
        100.khz(),
        &mut rcc.apb1,
    );

    // 读取数据
    let mut buffer = [0u8; 2];
    i2c.read(0x68, &mut buffer).unwrap();
}
```

### SPI

```rust
use hal::spi::Spi;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let spi = Spi::spi1(
        dp.SPI1,
        (sck, miso, mosi),
        1.mhz(),
        &mut rcc.apb2,
    );

    // 发送数据
    spi.write(&[0x01, 0x02]).unwrap();
}
```

---

## 实时系统

### RTIC 框架

```rust
#[rtic::app(device = stm32f4xx_hal::pac)]
mod app {
    #[shared]
    struct Shared {
        counter: u32,
    }

    #[local]
    struct Local {}

    #[init]
    fn init(ctx: init::Context) -> (Shared, Local) {
        (Shared { counter: 0 }, Local {})
    }

    #[task(binds = TIM2, shared = [counter])]
    fn timer2(mut ctx: timer2::Context) {
        ctx.shared.counter.lock(|counter| {
            *counter += 1;
        });
    }
}
```

---

## 调试

### Semihosting

```rust
use cortex_m_semihosting::hprintln;

fn main() -> ! {
    hprintln!("Hello, world!").unwrap();

    loop {}
}
```

### Probe-rs

```bash
# 安装
cargo install probe-rs-cli

# 运行
probe-rs run --chip STM32F411CETx target/thumbv7em-none-eabihf/debug/my-embedded

# 调试
probe-rs debug --chip STM32F411CETx target/thumbv7em-none-eabihf/debug/my-embedded
```

---

## 常见外设

### ADC

```rust
use hal::adc::Adc;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let adc = Adc::adc1(dp.ADC1, &mut rcc.apb2);

    let value: u16 = adc.read(&mut channel).unwrap();
}
```

### PWM

```rust
use hal::pwm::Pwm;

fn main() -> ! {
    let dp = Peripherals::take().unwrap();
    let pwm = Pwm::tim1(dp.TIM1, &mut rcc.apb2);

    pwm.set_duty(500);  // 50% 占空比
    pwm.enable();
}
```

---

## 最佳实践

1. **使用 HAL 层**：不要直接操作寄存器
2. **使用 no_std**：嵌入式环境通常没有标准库
3. **处理错误**：使用 `nb` crate 处理非阻塞操作
4. **使用中断**：而不是轮询
5. **优化内存**：使用 `opt-level = "z"` 和 LTO
6. **使用 RTIC**：对于实时应用

---

## 常见陷阱

1. **内存限制**：嵌入式设备内存有限
2. **实时性**：避免在中断中执行耗时操作
3. **外设冲突**：注意外设的共享和互斥
4. **电源管理**：合理使用低功耗模式
5. **调试困难**：使用 semihosting 或 probe-rs
