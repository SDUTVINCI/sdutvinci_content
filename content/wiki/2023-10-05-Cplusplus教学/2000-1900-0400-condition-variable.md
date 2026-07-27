---
title: "condition_variable"
---

## 本节解决什么问题

`std::condition_variable` 用于"一个线程等待某个条件，另一个线程改变条件后发通知"。

典型例子是生产者消费者：生产者把数据放进队列，消费者没有数据时等待，有数据后被唤醒。

条件变量通常和 `std::unique_lock<std::mutex>` 一起使用，因为 `wait()` 需要在等待时临时释放锁，被唤醒后再重新加锁。

## 示例代码

### 示例 1：生产者消费者

```cpp
#include <chrono>
#include <condition_variable>
#include <iostream>
#include <mutex>
#include <queue>
#include <thread>

std::queue<int> data_queue;
// 加锁用来保护共享数据，避免多个线程同时修改造成数据竞争。
std::mutex queue_mutex;
std::condition_variable queue_cv;
bool finished = false;

void producer()
{
    for (int value = 1; value <= 3; ++value)
    {
        {
            std::lock_guard<std::mutex> lock(queue_mutex);
            data_queue.push(value);
            std::cout << "produced " << value << "\n";
        }

        queue_cv.notify_one();
        std::this_thread::sleep_for(std::chrono::milliseconds(50));
    }

    {
        std::lock_guard<std::mutex> lock(queue_mutex);
        finished = true;
    }
    queue_cv.notify_one();
}

void consumer()
{
    while (true)
    {
        std::unique_lock<std::mutex> lock(queue_mutex);
        queue_cv.wait(lock, [] {
            return !data_queue.empty() || finished;
        });

        while (!data_queue.empty())
        {
            int value = data_queue.front();
            data_queue.pop();

            lock.unlock();
            std::cout << "consumed " << value << "\n";
            lock.lock();
        }

        if (finished)
        {
            break;
        }
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread p(producer);
    std::thread c(consumer);

    // join 会等待子线程结束，避免 main 提前退出。
    p.join();
    c.join();

    std::cout << "all done\n";

    return 0;
}
```

**一种可能的运行结果**：

```text
produced 1
consumed 1
produced 2
consumed 2
produced 3
consumed 3
all done
```

`queue_cv.wait(lock, 条件)` 很重要。线程被唤醒后会重新检查条件，避免虚假唤醒导致逻辑错误。

### 示例 2：等待一次通知

如果只是一个线程等待另一个线程准备好数据，也可以用条件变量。这个例子比生产者消费者更短，适合先理解 `wait` 和 `notify_one` 的配合。

```cpp
#include <condition_variable>
#include <iostream>
#include <mutex>
#include <string>
#include <thread>

// 加锁用来保护共享数据，避免多个线程同时修改造成数据竞争。
std::mutex data_mutex;
std::condition_variable data_cv;
std::string message;
bool ready = false;

void prepare()
{
    {
        std::lock_guard<std::mutex> lock(data_mutex);
        message = "hello from worker";
        ready = true;
    }

    data_cv.notify_one();
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // 创建子线程，让这部分代码和 main 线程并发运行。
    std::thread worker(prepare);

    std::unique_lock<std::mutex> lock(data_mutex);
    data_cv.wait(lock, [] {
        return ready;
    });

    std::cout << message << "\n";

    lock.unlock();
    // join 会等待子线程结束，避免 main 提前退出。
    worker.join();

    return 0;
}
```

**运行结果**：

```text
hello from worker
```

## 常见错误

1. `wait()` 不写条件谓词，只靠一次唤醒，容易被虚假唤醒影响。
2. 修改共享条件后忘记 `notify_one()` 或 `notify_all()`。
3. 持锁期间做耗时处理，导致其他线程不能及时推进。
4. 没有把条件变量、互斥锁、共享条件放在同一套逻辑里维护。

## 小结

- `condition_variable` 用于线程间等待和通知。
- `wait()` 通常要配合 `unique_lock`。
- 推荐写 `cv.wait(lock, [] { return 条件; });`。
- 修改条件后调用 `notify_one()` 或 `notify_all()`。
- 条件变量适合生产者消费者、任务队列、等待初始化完成等场景。
