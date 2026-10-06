---
vinciId: cc29835e-d658-55d3-ada9-ed4e7e680fa1
title: std::chrono
description: 本节解决什么问题
authors:
  - dongjiahui
publishedAt: 2023-10-05T00:00:00.000Z
updatedAt: 2026-08-12T11:38:56.779Z
---

## 本节解决什么问题

在 C 语言中，处理时间用 `time()`、`clock()`、`sleep()`、`gettimeofday()` 等函数，它们使用不同的单位和类型（秒、毫秒、微秒...），容易搞混，精度也不够。

`std::chrono` 是 C++11 引入的**类型安全的时间库**，把"时间点"、"时长"、"时钟"概念清晰地分开，单位在类型系统中自动管理，不需要手动换算。

## 这个特性是什么

`std::chrono` 提供了三个核心概念：

- **时长 `duration`**：一段时间长度，如 5 秒、10 毫秒。
- **时间点 `time_point`**：某个时刻，如 2026-06-02 12:00:00。
- **时钟 `clock`**：获取当前时间的工具，如 `system_clock`、`steady_clock`。

## C++ 标准版本

C++11（基础），C++20 增加了日历、时区等。

## 需要的头文件

```cpp
#include <chrono>
#include <thread>  // for std::this_thread::sleep_for
```

## 基本语法

```cpp
using namespace std::chrono;

// 时长
auto t1 = 5s;           // 5 秒
auto t2 = 100ms;        // 100 毫秒
auto t3 = 50us;         // 50 微秒
auto t4 = 10ns;         // 10 纳秒
auto t5 = 1min;         // 1 分钟（C++20）
auto t6 = 2h;           // 2 小时（C++20）

// 时间点
auto now = std::chrono::system_clock::now();  // 当前时间（系统时钟）
auto now2 = std::chrono::steady_clock::now(); // 单调时钟（不会往回跳）

// 时长转换
auto sec = std::chrono::duration_cast<std::chrono::seconds>(100ms);  // 0s（截断）

// 休眠
std::this_thread::sleep_for(500ms);
```

## 常用时钟对比

| 时钟 | 特点 | 用途 |
|:---|:---|:---|
| `system_clock` | 系统时间，可转换为日历时间 | 显示时间，时间戳 |
| `steady_clock` | 单调递增，不会往回跳 | 测量耗时（最常用） |
| `high_resolution_clock` | 最高精度时钟 | 需要最高精度的计时 |

## 示例代码

### 示例 1：测量一段代码的执行时间

```cpp
#include <iostream>
#include <chrono>
#include <thread>  // for sleep

int main()
{
    // 记录开始时间
    auto start = std::chrono::steady_clock::now();

    // 模拟耗时操作
    std::this_thread::sleep_for(std::chrono::milliseconds(500));

    // 记录结束时间
    auto end = std::chrono::steady_clock::now();

    // 计算耗时
    auto elapsed = end - start;
    auto elapsed_ms = duration_cast<std::chrono::milliseconds>(elapsed);
    auto elapsed_us = duration_cast<std::chrono::microseconds>(elapsed);

    std::cout << "Elapsed: " << elapsed_ms.count() << " ms\n";
    std::cout << "Elapsed: " << elapsed_us.count() << " us\n";

    return 0;
}
```

**运行结果**（大约）：

```
Elapsed: 500 ms
Elapsed: 500000 us
```

这里的us可能有波动，一般是百位数以内级别的波动。

### 示例 2：在示例 1 基础上，不同时间单位的使用和转换

```cpp
#include <iostream>
#include <chrono>

int main()
{
    using namespace std::chrono_literals;  //可以使用1s,100ms,500us这种更便捷

    // 不同单位
    auto s = 1s;         // 1 秒 等同于 auto s = std::chrono::seconds(1);
    auto ms = 100ms;     // 100 毫秒 等同于 auto ms = std::chrono::milliseconds(100);
    auto us = 500us;     // 500 微秒 等同于 auto us = std::chrono::microseconds(500);

    // 时长可以相加
    auto total = s + ms + us;
    std::cout << "total in us: " << std::chrono::duration_cast<std::chrono::microseconds>(total).count() << " us\n";

    // 秒到毫秒的转换
    auto two_seconds = 2s;
    auto as_ms = std::chrono::duration_cast<std::chrono::milliseconds>(two_seconds);
    std::cout << "2s = " << as_ms.count() << " ms\n";

    // 毫秒到秒的转换（会截断）
    auto ms_to_sec = std::chrono::duration_cast<std::chrono::seconds>(1500ms);
    std::cout << "1500ms = " << ms_to_sec.count() << " s\n";  // 1 秒

    return 0;
}
```

**运行结果**：

```
total in us: 1100500 us
2s = 2000 ms
1500ms = 1 s
```

### 示例 3：在示例 2 基础上，实现一个简单的计时器

```cpp
#include <chrono>
#include <cstdio>
#include <functional>
#include <print>

// 简单的计时器：测量函数运行时间
double measure(std::function<void()> func)
{
    auto start = std::chrono::steady_clock::now();
    func();
    auto end = std::chrono::steady_clock::now();

    return std::chrono::duration_cast<std::chrono::microseconds>(end - start).count() / 1000.0;  // 返回毫秒（先转us再转ms可以保留小数，只是最终展示形式以ms形式）
}

void slow_operation()
{
    long int sum = 0;
    for (int i = 0; i < 10000000; ++i)
    {
        sum += i;
    }
    std::println("sum = {}",sum);
}

void quick_operation()
{
    int sum = 0;
    for (int i = 0; i < 1000; ++i)
    {
        sum += i;
    }
    std::println("sum = {}",sum);
}

int main()
{
    std::print("slow_op took {} ms\n",measure(slow_operation));
    std::print("quick_op took {} ms\n",measure(quick_operation));

    return 0;
}
```

**运行结果**（运行时间因机器而异）：

```
sum = 49999995000000
slow_op took 17.712 ms
sum = 499500
quick_op took 0.005 ms
```

### 示例 4：在示例 3 基础上，获取和格式化系统时间

```cpp
#include <chrono>
#include <ctime>
#include <print>

int main()
{
    using namespace std::chrono_literals;

    // 获取当前系统时间
    auto now = std::chrono::system_clock::now();

    // 获取自 epoch（1970-01-01）以来的秒数
    auto epoch_seconds = std::chrono::duration_cast<std::chrono::seconds>(now.time_since_epoch());
    std::println("Seconds since epoch: {}",epoch_seconds.count());

    // 转换为 C 风格的 time_t 打印 （打印的是本地时间，比如上海时间UTC+8）
    std::time_t now_c = std::chrono::system_clock::to_time_t(now);
    std::print("Current time: {}",std::ctime(&now_c));  //std::ctime()自己带一个换行

    // 计算未来时间点（C++23的chrono格式化打印，打印UTC时间）
    auto future = now + 24h;  // C++20 的 24h 字面量
    // 如果没有 C++20，可以用: auto future = now + std::chrono::hours(24);
    std::println("24 hours later: {:%Y-%m-%d %H:%M:%S}",future); // 末尾会带一串高精度纳秒级部分

    //转秒单位，来忽略future带的更高精度的时间（C++23的chrono格式化打印，打印UTC时间）
    auto future_sec = std::chrono::time_point_cast<std::chrono::seconds>(future);
    std::println("24 hours later: {:%Y-%m-%d %H:%M:%S}",future_sec);

    return 0;
}
```

**运行结果**（日期因运行时间而异）：

```
Seconds since epoch: 1788875788
Current time: Tue Sep  8 21:56:28 2026
24 hours later: 2026-09-09 13:56:28.573118608
24 hours later: 2026-09-09 13:56:28
```

这里为何`ctime()`和`{:%Y-%m-%d %H:%M:%S}`打印出来的时间相差8小时呢？

```text
system_clock::now()
    ↓
绝对时间点
没有“本地时间”这个概念
    │
    ├── ctime()
    │      ↓
    │   自动按系统本地时区显示
    │
    ├── 直接 chrono 格式化 system_clock::time_point
    │      ↓
    │   按 UTC 显示
    │
    └── zoned_time
           ↓
        按指定时区显示
```

所以

```text
13:56 UTC
   =
21:56 UTC+8
```

可以通过以下命令在终端里看你的电脑在什么时区里

```bash
date
timedatectl
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1788876514267-a8ed2547.webp)

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1788876531473-3c31ee22.webp)

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 测量代码耗时 | `steady_clock::now()`、`duration_cast`、字面量 `500ms` | steady_clock 单调递增，适合计时 | 字面量需要 `using namespace std::chrono` |
| 示例 2 | 时长单位转换 | `1s`、`100ms`、`duration_cast` | 类型自动管理单位，转换时要显式 cast | `duration_cast` 是截断，不是四舍五入 |
| 示例 3 | 通用计时器 | `measure(function<void()>)` | 包装成一个工具函数，可重复使用 | `steady_clock` 是最佳选择 |
| 示例 4 | 系统时间和格式化 | `system_clock::now()`、`to_time_t()`、`24h` | system_clock 可以转为日历时间 | system_clock 可能被系统时间调整影响，不用于计时 |

## 计时和显示时间不要混用

| 需求 | 推荐时钟 | 原因 |
|:---|:---|:---|
| 测量函数耗时 | `steady_clock` | 单调递增，不受系统时间调整影响 |
| 超时判断 | `steady_clock` | 不会因为系统时间跳变导致超时错误 |
| 打印当前日期时间 | `system_clock` | 可以转换成日历时间 |
| 生成日志时间戳 | `system_clock` | 对人类可读 |

一个常见错误是用 `system_clock` 做超时判断。如果系统时间被 NTP 或用户手动调回去，程序可能“等很久都不超时”。测耗时和超时都优先用 `steady_clock`。

### 示例 5：用 steady_clock 做超时判断

```cpp
#include <chrono>
#include <thread>
#include <print>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    using namespace std::chrono_literals;

    auto deadline = std::chrono::steady_clock::now() + 300ms;

    while (std::chrono::steady_clock::now() < deadline)
    {
        std::println("waiting...");

        std::this_thread::sleep_for(100ms);
    }

    std::println("timeout");

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**可能的运行结果**：

```
waiting...
waiting...
waiting...
timeout
```

这里的重点不是循环本身，而是超时基准用 `steady_clock`。这类写法在定时器、串口读取超时、网络等待超时里非常常见。

## 常见错误

**错误 1：忘记 `using namespace std::chrono_literals` 或者 `using namespace std::chrono` 导致字面量不识别**

```cpp
auto t = 500ms;  // ❌ 编译错误！
```

正确做法：加 `using namespace std::chrono_literals;` 或 加 `using namespace std::chrono` 或 写 `std::chrono::milliseconds(500)`。

**错误 2：用 system_clock 测量耗时**

```cpp
auto start = system_clock::now();  // ❌ 系统时间可能被人调回去了！
```

正确做法：测量耗时一定要用 `steady_clock`。

**错误 3：忘记 `.count()` 直接输出 duration**

```cpp
std::cout << duration_cast<milliseconds>(elapsed);  // ❌ 不能直接输出
```

正确做法：`std::cout << elapsed_ms.count() << " ms\n";`

**错误 4：duration_cast 的截断问题**

```cpp
auto sec = duration_cast<seconds>(1500ms);  // 1 秒！不是 1.5 秒
```

正确做法：如果需要小数，用 `duration<double>` 或 `duration_cast<milliseconds>` 保留精度。

```cpp
std::chrono::duration<double> sec = 1500ms;  //std::chrono::duration<double>的默认单位就是“秒”，只不过底层数值类型从整数变成了 double。
std::println("{}", sec.count());  //输出1.5
```

```cpp
auto ms = 1500ms;
std::println("{}", ms.count() / 1000.0);   //输出1.5
```

## 使用建议

1. **测量耗时用 `steady_clock`**：单调递增，不受系统时间调整影响。
2. **显示时间用 `system_clock`**：可以转为日历时间。
3. **用字面量 `1s`、`100ms` 而不是直接写数字**：可读性更好。
4. **`duration_cast` 是截断，不是四舍五入**：精度会丢失，注意使用场景。
5. **C++20 增加了日历和时区支持**：`std::chrono::year_month_day` 等，更强大。
6. **超时判断也用 `steady_clock`**：和测耗时一样，不受系统时间调整影响。

## 小结

- `std::chrono` 提供类型安全的时间处理：duration（时长）、time_point（时间点）、clock（时钟）。
- 用 `steady_clock::now()` 测量耗时，用 `system_clock::now()` 获取系统时间。
- 字面量 `1s`、`100ms`、`50us` 让时间代码直观易读。
- `duration_cast` 做单位转换，`.count()` 获取数值。
- `std::this_thread::sleep_for(500ms)` 做线程休眠。

### 工程拓展

在 ROS2 中，`rclcpp::Duration` 和 `rclcpp::Time` 的用法和 `std::chrono` 非常相似。学好了 `std::chrono`，理解 ROS2 的时间处理就很容易了。在 Boost.Asio 中，定时器也大量使用 `std::chrono::duration`。
