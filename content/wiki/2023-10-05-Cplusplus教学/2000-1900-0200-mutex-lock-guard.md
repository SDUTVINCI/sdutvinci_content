---
title: "mutex 与 lock_guard"
---

## 本节解决什么问题

多个线程同时修改同一个变量时，`++counter` 并不是一个不可分割的动作。它通常包含读取、加一、写回几个步骤。两个线程交错执行时，就会产生数据竞争。

保护共享数据最常见的做法是：用 `std::mutex` 互斥锁，加锁后只有一个线程能进入临界区。`std::lock_guard` 是 RAII 锁，构造时加锁，离开作用域时自动解锁。

## 示例代码

### 示例 1：用 mutex 和 lock_guard 保护共享计数器

```cpp
#include <iostream>
#include <mutex>
#include <thread>

int counter = 0;
// 加锁用来保护共享数据，避免多个线程同时修改造成数据竞争。
std::mutex counter_mutex;

void add_many(int times)
{
    for (int i = 0; i < times; ++i)
    {
        std::lock_guard<std::mutex> lock(counter_mutex);
        ++counter;
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread t1(add_many, 100000);
    std::thread t2(add_many, 100000);

    // join 会等待子线程结束，避免 main 提前退出。
    t1.join();
    t2.join();

    std::cout << "counter = " << counter << "\n";
    std::cout << "expected = 200000\n";

    return 0;
}
```

**运行结果**：

```text
counter = 200000
expected = 200000
```

### 示例 2：锁的作用域要尽量小

这个例子里，线程先在锁外准备数据，只有更新共享总数时才加锁。这样另一个线程不用在无关工作上等待。

```cpp
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>

int total = 0;
// 加锁用来保护共享数据，避免多个线程同时修改造成数据竞争。
std::mutex total_mutex;

int sum_part(const std::vector<int>& data, int begin, int end)
{
    int local_sum = 0;
    for (int i = begin; i < end; ++i)
    {
        local_sum += data[i];
    }
    return local_sum;
}

void worker(const std::vector<int>& data, int begin, int end)
{
    int local_sum = sum_part(data, begin, end);

    {
        std::lock_guard<std::mutex> lock(total_mutex);
        total += local_sum;
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    std::vector<int> data = {1, 2, 3, 4, 5, 6};

    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread t1(worker, std::cref(data), 0, 3);
    std::thread t2(worker, std::cref(data), 3, 6);

    // join 会等待子线程结束，避免 main 提前退出。
    t1.join();
    t2.join();

    std::cout << "total = " << total << "\n";

    return 0;
}
```

**运行结果**：

```text
total = 21
```

## lock_guard 和 unique_lock 的区别

| 锁类型 | 特点 | 常见用途 |
|:---|:---|:---|
| `std::lock_guard` | 简单，构造加锁，析构解锁，不能手动解锁 | 保护一小段共享数据访问 |
| `std::unique_lock` | 可延迟加锁、手动解锁、转移所有权 | 条件变量、需要提前释放锁的场景 |

初学时优先用 `lock_guard`。只有需要配合 `condition_variable` 或者需要更灵活地控制加锁时，再用 `unique_lock`。

## 常见错误

1. 多个线程同时修改共享数据，却没有用 `mutex` 或 `atomic` 保护。
2. 手动 `lock()` 后忘记 `unlock()`，或者中途异常导致无法解锁。
3. 持有锁时做耗时操作，比如睡眠、网络请求、文件 IO。
4. 锁保护的范围太小，读写共享数据的某些路径漏掉了锁。

## 小结

- 共享数据需要同步保护。
- `std::mutex` 提供互斥访问。
- `std::lock_guard` 是 RAII 锁，构造加锁，析构解锁。
- 锁的作用域越小越好，但必须完整覆盖共享数据访问。
