---
title: "std::thread 与 join"
---

## 本节解决什么问题

`std::thread` 用来创建线程。线程创建后会立刻启动，主线程可以继续执行自己的代码。

初学阶段最重要的是记住：`std::thread` 对象销毁前，必须调用 `join()` 或 `detach()`。多数教学和普通业务代码里优先用 `join()`，因为它会等待线程结束，生命周期最清楚。

## 示例代码

### 示例 1：创建两个线程分别处理两份数据

这个例子中，两个线程分别写入不同的 `vector`，所以没有共享写同一个容器，不需要加锁。主线程等两个线程都结束后再打印结果。

```cpp
#include <functional>
#include <iostream>
#include <thread>
#include <vector>

// vector 是动态数组，元素数量可以在运行时变化。
void fill_numbers(std::vector<int>& output, int start)
{
    for (int i = 0; i < 3; ++i)
    {
        output.push_back(start + i);
    }
}

void print_numbers(const char* name, const std::vector<int>& values)
{
    std::cout << name << ": ";
    for (int value : values)
    {
        std::cout << value << " ";
    }
    std::cout << "\n";
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    std::vector<int> left;
    std::vector<int> right;

    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread t1(fill_numbers, std::ref(left), 1);
    std::thread t2(fill_numbers, std::ref(right), 10);

    // join 会等待子线程结束，避免 main 提前退出。
    t1.join();
    t2.join();

    print_numbers("left", left);
    print_numbers("right", right);
    std::cout << "done\n";

    return 0;
}
```

**运行结果**：

```text
left: 1 2 3
right: 10 11 12
done
```

这里使用 `std::ref(left)` 和 `std::ref(right)`，表示把 `vector` 按引用传给线程函数。否则 `std::thread` 会尝试复制参数。

### 示例 2：顺序等待 vs 并发等待

一个任务等待 1 秒，顺序写法和线程写法看起来都只是"等完了"。两个任务各等 1 秒时，区别就明显了：

- 顺序执行：先等 A，再等 B，总共约 2 秒。
- 两个线程：A 和 B 同时等，总共约 1 秒。

```cpp
#include <chrono>
#include <iostream>
#include <thread>

using namespace std::chrono;

void wait_one_second()
{
    std::this_thread::sleep_for(seconds(1));
}

long long run_sync()
{
    auto begin = steady_clock::now();

    wait_one_second();
    wait_one_second();

    auto end = steady_clock::now();
    return duration_cast<seconds>(end - begin).count();
}

long long run_threads()
{
    auto begin = steady_clock::now();

    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread t1(wait_one_second);
    std::thread t2(wait_one_second);

    // join 会等待子线程结束，避免 main 提前退出。
    t1.join();
    t2.join();

    auto end = steady_clock::now();
    return duration_cast<seconds>(end - begin).count();
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    std::cout << "sync seconds = " << run_sync() << "\n";
    std::cout << "thread seconds = " << run_threads() << "\n";

    return 0;
}
```

**运行结果**：

```text
sync seconds = 2
thread seconds = 1
```

这个例子不是说所有任务都应该开线程。线程创建和调度也有成本。非常短的任务，例如只做几次加法，开线程可能反而更慢。

## 常见错误

**错误 1：忘记 join 或 detach**

```cpp
{
    std::thread t([] {
        std::cout << "work\n";
    });
} // t 还是 joinable，程序会 std::terminate
```

正确做法：在 `t` 离开作用域前调用 `t.join()`。

**错误 2：线程函数引用了已经销毁的局部变量**

```cpp
void start_thread()
{
    int value = 42;
    std::thread t([&value] {
        std::cout << value << "\n";
    });
    t.detach();
}
```

`detach()` 后线程可能晚于 `start_thread()` 返回才执行，此时 `value` 已经销毁。正确做法是按值捕获，或者用智能指针管理生命周期。

## 小结

- `std::thread` 创建后会立刻启动。
- `join()` 表示等待线程完成。
- 初学阶段优先用 `join()`，少用 `detach()`。
- 向线程函数传引用参数时要用 `std::ref`。
- 两个耗时任务同时推进时，才能直观看出并发的价值。
