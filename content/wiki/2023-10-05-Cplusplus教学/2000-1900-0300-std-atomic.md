---
title: "std::atomic"
---

## 本节解决什么问题

如果共享数据只是一个简单计数器，可以用 `std::atomic<int>`。它能保证 `++counter` 这样的操作是线程安全的，不需要手动加锁。

`atomic` 适合简单独立的原子操作。多个变量需要保持一致，或者需要执行复杂逻辑时，仍然应该使用 `mutex`。

## 示例代码

### 示例 1：用 atomic 实现线程安全计数

```cpp
#include <atomic>
#include <iostream>
#include <thread>
#include <vector>

// atomic 提供原子操作，适合简单的跨线程共享状态。
std::atomic<int> counter{0};

void add_many(int times)
{
    for (int i = 0; i < times; ++i)
    {
        ++counter;
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // vector 是动态数组，元素数量可以在运行时变化。
    std::vector<std::thread> threads;

    for (int i = 0; i < 4; ++i)
    {
        threads.emplace_back(add_many, 100000);
    }

    for (auto& thread : threads)
    {
        // join 会等待子线程结束，避免 main 提前退出。
        thread.join();
    }

    std::cout << "counter = " << counter << "\n";
    std::cout << "expected = 400000\n";

    return 0;
}
```

**运行结果**：

```text
counter = 400000
expected = 400000
```

### 示例 2：atomic 适合状态标记

下面例子用一个原子布尔值做停止标记。主线程修改标记，工作线程能安全看到变化。

```cpp
#include <atomic>
#include <chrono>
#include <iostream>
#include <thread>

// atomic 提供原子操作，适合简单的跨线程共享状态。
std::atomic<bool> running{true};

void worker()
{
    int count = 0;
    while (running)
    {
        ++count;
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }

    std::cout << "worker count = " << count << "\n";
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread t(worker);

    std::this_thread::sleep_for(std::chrono::milliseconds(350));
    running = false;

    // join 会等待子线程结束，避免 main 提前退出。
    t.join();
    std::cout << "stopped\n";

    return 0;
}
```

**一种可能的运行结果**：

```text
worker count = 4
stopped
```

## mutex 和 atomic 怎么选

| 场景 | 推荐 |
|:---|:---|
| 简单计数、自增、自减 | `std::atomic` |
| 一个布尔停止标记 | `std::atomic<bool>` |
| 多个变量要同时保持一致 | `std::mutex` |
| 修改容器、对象内部复杂状态 | `std::mutex` |
| 需要等待某个条件成立 | `std::condition_variable` |

## 常见错误

1. 以为用了 `atomic`，整个对象就都线程安全。只有这个原子变量自己的操作是线程安全的。
2. 用多个 `atomic` 表达一个整体状态，却没有保证它们之间的一致性。
3. 在复杂共享数据结构上强行用 `atomic`，导致代码难懂又容易错。

## 小结

- `std::atomic<T>` 让简单变量的基本操作具备线程安全性。
- 简单计数器和停止标记很适合 `atomic`。
- 复杂共享状态仍然应该用 `mutex`。
- 初学阶段不必展开内存序，先掌握默认用法和适用边界。
