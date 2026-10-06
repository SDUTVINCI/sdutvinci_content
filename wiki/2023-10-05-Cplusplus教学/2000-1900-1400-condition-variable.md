---
title: condition_variable
description: 本节解决什么问题
authors:
  - dongjiahui
publishedAt: 2023-10-05T00:00:00.000Z
updatedAt: 2026-08-12T11:38:56.764Z
---

`std::condition_variable` 用来解决这样一类问题：

> **某个条件现在还不满足，我不想一直占着 CPU 检查，而是希望先睡眠，等条件可能发生变化时再醒来。**

典型场景包括：

```text
队列里暂时没有任务
→ worker 先睡眠

初始化还没完成
→ 其他线程先等待

消费者暂时没有数据
→ 等生产者放入数据

多个线程等待某个状态变为 ready
→ 状态变化后再继续
```

条件变量最重要的不是背：

```cpp
wait()
notify_one()
notify_all()
```

而是理解它必须围绕下面这套东西一起使用：

```text
共享状态 + mutex + condition_variable + predicate
```

它们的分工是：

```text
共享状态
→ 记录“事情到底有没有发生”

mutex
→ 保护共享状态的读写

condition_variable
→ 条件不满足时让线程睡眠
→ 条件可能变化时把等待线程叫醒

predicate
→ 被唤醒以后重新判断：
  “现在到底能不能继续执行？”
```

所以：

> `condition_variable` 本身不保存业务状态，它只是一个“等待和唤醒机制”。

---

## 条件变量基础

### 为什么不能一直循环检查

最直接的等待方法可能是：

```cpp
while (!ready)
{
}
```

这种代码有两个问题。

首先，如果：

```cpp
bool ready = false;
```

是普通变量，而且多个线程同时读写它，那么可能产生：

```text
data race
```

即使改成：

```cpp
std::atomic<bool> ready{false};
```

然后：

```cpp
while (!ready.load())
{
}
```

虽然线程安全了，但线程会不断执行：

```text
load
load
load
load
load
...
```

这叫：

> **忙等（busy waiting）**

也就是线程虽然什么有用的事都没干，却一直占着 CPU 检查条件。

如果可能要等几毫秒、几秒，甚至更久，这通常很浪费。

条件变量的思想是：

```text
条件不满足
↓
线程睡眠，不占着 CPU 空转

其他线程修改状态
↓
发出通知

等待线程醒来
↓
重新检查条件
```

所以条件变量解决的不是：

```text
怎么让 bool 线程安全
```

而是：

```text
条件不满足时怎么高效等待

条件可能满足时怎么把线程叫醒
```

---

### 条件变量最基本的三个对象

通常会看到：

```cpp
std::mutex mutex;
std::condition_variable cv;
bool ready = false;
```

这里：

```cpp
ready
```

是真正的共享状态，表示：

```text
“现在能不能继续执行？”
```

`mutex`：

```cpp
std::mutex mutex;
```

负责保护：

```cpp
ready
```

的读写。

`cv`：

```cpp
std::condition_variable cv;
```

负责：

```text
让线程睡眠
+
把线程唤醒
```

最重要的一点：

> `condition_variable` 本身不是状态。

也就是说，下面这种想法是错的：

```text
“我收到 notify 了，所以条件肯定成立了。”
```

真正决定线程能不能继续的是：

```cpp
ready
```

或者：

```cpp
!queue.empty()
```

或者其他业务状态。

---

### 最简单的 wait / notify 示例

```cpp
#include <condition_variable>
#include <mutex>
#include <print>
#include <thread>

std::mutex mutex;
std::condition_variable cv;

bool ready = false;

void worker()
{
    std::unique_lock lock(mutex);

    cv.wait(lock, [] {
        return ready;
    });

    std::println("worker start");
}

int main()
{
    std::thread thread(worker);

    {
        std::lock_guard lock(mutex);
        ready = true;
    }

    cv.notify_one();

    thread.join();
}
```

运行结果：

```text
worker start
```

整个过程可以理解成：

```text
worker 获得 mutex
        ↓
检查 ready
        ↓
发现 ready == false
        ↓
wait()：
释放 mutex
并让 worker 睡眠
        ↓
main 获得 mutex
        ↓
ready = true
        ↓
main 释放 mutex
        ↓
notify_one()
        ↓
worker 被唤醒
        ↓
worker 重新获得 mutex
        ↓
重新检查 ready
        ↓
ready == true
        ↓
wait() 返回
        ↓
打印 worker start
```

这里最重要的一步是：

```text
wait() 会在睡眠期间释放 mutex
```

否则程序会死锁。

---

### 为什么 wait() 睡眠时必须释放 mutex

假设 worker 这样干：

```text
拿到 mutex
↓
发现 ready == false
↓
拿着 mutex 睡觉
```

那 main 想执行：

```cpp
ready = true;
```

之前也必须先：

```cpp
lock(mutex);
```

但 mutex 一直被 worker 拿着。

于是：

```text
worker：
等 ready 变成 true
但一直拿着 mutex

main：
想把 ready 改成 true
但拿不到 mutex
```

结果就是：

```text
谁也进行不下去
```

所以条件变量的 `wait()` 必须做一件非常特殊的事情：

```text
当前已经持有 mutex
        ↓
原子地释放 mutex + 进入等待
        ↓
收到唤醒
        ↓
重新获得 mutex
        ↓
再返回
```

---

### 为什么 wait() 要配合 `std::unique_lock`

常见写法：

```cpp
std::unique_lock lock(mutex);

cv.wait(lock, predicate);
```

而不是：

```cpp
std::lock_guard lock(mutex);
```

原因是：

`wait()` 需要暂时：

```text
unlock()
```

然后以后再：

```text
lock()
```

而 `std::unique_lock` 支持这种：

```text
临时释放锁
↓
以后重新获得锁
```

的所有权管理。

`std::lock_guard` 不支持手动解锁和重新加锁，所以不能满足普通 `condition_variable::wait()` 的要求。

可以把：

```cpp
cv.wait(lock, predicate);
```

粗略理解成：

```cpp
while (!predicate())
{
    cv.wait(lock);
}
```

而内部的：

```cpp
cv.wait(lock);
```

会做：

```text
释放 mutex
+
线程睡眠
+
被唤醒
+
重新获得 mutex
```

---

### 为什么“释放 mutex + 睡眠”必须是原子的

这里还有一个很重要的问题。

假设 wait 的内部逻辑不是原子的，而是：

```text
1. unlock mutex

2. 过一会儿才真正开始睡眠
```

那么可能发生：

```text
worker：
unlock mutex
↓
还没真正睡下去

main：
修改 ready = true
↓
notify_one()

worker：
此时才进入睡眠
```

这样：

```text
notify 已经发生了

worker 却刚刚开始睡
```

worker 就可能一直睡下去。

所以条件变量必须保证：

> **“释放 mutex”和“进入等待”之间不会留下一个能丢通知的空档。**

这也是为什么不要自己用：

```cpp
unlock();
sleep();
lock();
```

去模拟 `condition_variable::wait()`。

---

## wait 与 predicate

### `wait(lock)` 和 `wait(lock, predicate)`

条件变量有两种常见等待方式。

#### 不带谓词

```cpp
cv.wait(lock);
```

它的意思是：

```text
先睡眠
↓
被唤醒后重新拿锁
↓
返回
```

问题是：

> 被唤醒并不代表你的业务条件一定成立。

所以如果用这种形式，一般必须自己写循环：

```cpp
while (!ready)
{
    cv.wait(lock);
}
```

---

#### 带谓词

推荐：

```cpp
cv.wait(lock, [] {
    return ready;
});
```

它可以理解成标准库替你写了：

```cpp
while (!ready)
{
    cv.wait(lock);
}
```

所以：

```cpp
cv.wait(lock, predicate);
```

真正表达的是：

> **只要 predicate 还不成立，我就继续等待。**

例如：

```cpp
cv.wait(lock, [] {
    return !queue.empty();
});
```

表示：

> 只要队列还是空的，我就继续等。

---

### 什么是 predicate

predicate 就是：

> **判断“现在能不能继续”的条件。**

例如：

```cpp
[] {
    return ready;
}
```

或者：

```cpp
[] {
    return !queue.empty();
}
```

或者：

```cpp
[] {
    return !queue.empty() || finished;
}
```

所以：

```text
condition_variable
→ 负责把你叫醒

predicate
→ 决定你醒来以后到底能不能继续
```

可以记成：

> **notify 只是说“起来看看”；predicate 才说“现在能不能走”。**

---

### 虚假唤醒（spurious wakeup）

条件变量允许一种情况：

```text
没有真正的业务事件发生
↓
wait() 却醒了
```

这叫：

> **虚假唤醒（spurious wakeup）**

所以这种写法不安全：

```cpp
cv.wait(lock);

use_shared_data();
```

因为：

```text
wait() 返回
```

并不一定意味着：

```text
共享数据已经满足条件
```

正确写法是：

```cpp
cv.wait(lock, [] {
    return ready;
});
```

或者等价的：

```cpp
while (!ready)
{
    cv.wait(lock);
}
```

也就是说：

> **每次醒来都必须重新检查真正的共享状态。**

---

## notify 与状态

### notify 不是状态

条件变量还有一个非常容易误解的地方：

```cpp
cv.notify_one();
```

不是在：

```text
condition_variable 里面存了一条消息
```

它更像是：

> “现在正在等的人，可以起来重新检查条件了。”

如果通知发生时：

```text
根本没有线程在等待
```

那么这次通知就结束了。

以后才来 wait 的线程：

```text
不会补收到这次旧通知
```

例如：

```text
线程 A：
notify_one()
↓
当时没人等待
↓
通知结束

过了一会儿

线程 B：
开始 wait()
```

线程 B 不会收到线程 A 之前那次通知。

这类现象通常称为：

> **丢失唤醒（lost wakeup）**

---

### 那为什么不会轻易因为“通知先发生”而出错

因为正确的代码不会依赖：

```text
“通知有没有发生过”
```

而是依赖：

```text
“共享状态现在是什么”
```

例如：

```cpp
{
    std::lock_guard lock(mutex);
    ready = true;
}

cv.notify_one();
```

即使：

```text
ready = true
notify_one()
```

都发生在 worker 开始 wait 之前，也没问题。

因为 worker 后来执行：

```cpp
cv.wait(lock, [] {
    return ready;
});
```

会先检查：

```cpp
ready
```

发现已经：

```text
true
```

于是：

```text
根本不会睡
```

所以：

```text
共享状态
→ 保存事实

condition_variable
→ 只是优化等待方式
```

这是条件变量最核心的理解之一。

---

### `notify_one()`

```cpp
cv.notify_one();
```

表示：

> **唤醒一个当前正在等待这个 condition_variable 的线程。**

例如有：

```text
10 个 worker 都在等任务
```

但现在只放入了：

```text
1 个任务
```

通常：

```cpp
notify_one();
```

就够了。

因为最终只有一个 worker 能消费这个任务。

---

### `notify_all()`

```cpp
cv.notify_all();
```

表示：

> **唤醒所有当前正在等待这个 condition_variable 的线程。**

适合：

```text
程序准备退出

全局状态发生变化

所有 worker 都应该重新检查退出条件
```

例如：

```cpp
finished = true;
cv.notify_all();
```

所有线程都会被叫醒。

但：

```text
notify_all()
```

不代表：

```text
所有线程同时进入临界区
```

它们醒来以后仍然要：

```text
竞争同一把 mutex
↓
一个一个拿锁
↓
检查 predicate
```

所以：

```text
notify_all()
→ 所有人都醒来看看

不是
→ 所有人同时进入临界区
```

---

### 修改状态以后什么时候 notify

很常见的写法是：

```cpp
{
    std::lock_guard lock(mutex);
    ready = true;
}

cv.notify_one();
```

顺序是：

```text
持锁修改共享状态
↓
释放 mutex
↓
notify
```

这样等待线程被叫醒以后：

```text
更有机会直接拿到 mutex
```

而不是：

```text
刚醒
↓
发现通知线程还拿着 mutex
↓
又继续阻塞
```

不过要注意：

> `notify()` 并不是规定“必须永远放在锁外”。

例如：

```cpp
{
    std::lock_guard lock(mutex);

    ready = true;
    cv.notify_one();
}
```

在很多场景下也完全正确。

真正决定正确性的核心是：

```text
共享谓词状态必须正确同步

wait 方必须在锁保护下检查 predicate
```

把 notify 放锁外，更多是常见的性能和调度优化习惯。

---

## 生产者消费者

### 生产者消费者模型

这是条件变量最经典的应用。

假设：

```text
producer
→ 不断往 queue 塞数据

consumer
→ 没数据时睡眠
→ 有数据时取出来处理
```

同时还需要考虑：

```text
producer 最终会结束
```

所以消费者真正等待的条件不能只是：

```cpp
!queue.empty()
```

还要考虑：

```cpp
finished
```

完整示例：

```cpp
#include <condition_variable>
#include <mutex>
#include <print>
#include <queue>
#include <thread>

std::queue<int> queue;

std::mutex mutex;
std::condition_variable cv;

bool finished = false;

void producer()
{
    for (int value = 1; value <= 5; ++value)
    {
        {
            std::lock_guard lock(mutex);

            queue.push(value);
        }

        cv.notify_one();
    }

    {
        std::lock_guard lock(mutex);

        finished = true;
    }

    cv.notify_all();
}

void consumer()
{
    while (true)
    {
        int value;

        {
            std::unique_lock lock(mutex);

            cv.wait(lock, [] {
                return !queue.empty() || finished;
            });

            if (queue.empty() && finished)
            {
                break;
            }

            value = queue.front();
            queue.pop();
        }

        std::println("consume {}", value);
    }
}

int main()
{
    std::thread producer_thread(producer);
    std::thread consumer_thread(consumer);

    producer_thread.join();
    consumer_thread.join();
}
```

输出：

```text
consume 1
consume 2
consume 3
consume 4
consume 5
```

---

### 为什么 predicate 是：

```cpp
!queue.empty() || finished
```

而不是：

```cpp
!queue.empty()
```

假设生产者已经彻底结束：

```text
finished == true
```

同时队列也空了：

```text
queue.empty() == true
```

如果 consumer 只等待：

```cpp
!queue.empty()
```

那么：

```text
以后永远不会再有数据
↓
consumer 却继续等待“队列非空”
↓
没人会再生产任务
↓
consumer 永远睡下去
```

所以正确的等待条件应该表达：

```text
有任务了

或者

生产已经结束了
```

也就是：

```cpp
!queue.empty() || finished
```

---

### consumer 醒来后为什么还要判断

等待结束以后：

```cpp
cv.wait(lock, [] {
    return !queue.empty() || finished;
});
```

这里只说明：

```text
队列非空
或者
生产结束
```

至少有一个是真的。

所以接下来要区分：

#### 情况：队列有数据

```text
queue 非空
↓
取出一个任务
```

#### 情况：队列为空，而且 finished == true

```text
queue.empty()
&&
finished
```

说明：

```text
现在没任务
而且以后也不会再有任务
```

这时 consumer 才可以安全退出。

---

### 为什么真正处理任务要放到锁外

consumer 里面：

```cpp
{
    std::unique_lock lock(mutex);

    // 检查 queue
    // pop 一个任务
}

process_task();
```

一般比：

```cpp
std::unique_lock lock(mutex);

queue.pop();

process_task(); // 耗时 2 秒
```

更好。

原因是：

如果你拿着 mutex 做耗时工作：

```text
consumer 拿着 queue mutex 处理 2 秒
```

那么这 2 秒内：

```text
producer 不能 push

其他 consumer 不能 pop
```

所有线程都被这一把锁堵住了。

所以锁内尽量只做：

```text
检查共享状态

读取 / 修改共享状态

取出任务
```

真正耗时的业务逻辑：

```text
放到锁外
```

这是多线程程序里非常重要的习惯。

---

## 超时与高级用法

### `wait_for()`

可以让线程：

> 最多等一段时间。

例如：

```cpp
bool ok = cv.wait_for(
    lock,
    std::chrono::seconds(1),
    [] {
        return ready;
    });
```

返回：

```text
true
→ 在等待结束前 predicate 成立

false
→ 超时以后 predicate 仍然不成立
```

例如：

```cpp
if (!cv.wait_for(
        lock,
        std::chrono::seconds(1),
        [] {
            return ready;
        }))
{
    std::println("timeout");
}
```

注意：

```text
timeout
```

不代表程序一定出错。

它只表示：

```text
这段时间里条件没有成立
```

至于超时以后：

```text
重试

退出

打印日志

走备用逻辑
```

由业务决定。

---

### `wait_until()`

`wait_until()` 用来等待到某个：

```text
绝对时间点
```

例如：

```cpp
auto deadline =
    std::chrono::steady_clock::now()
    + std::chrono::seconds(2);

cv.wait_until(
    lock,
    deadline,
    [] {
        return ready;
    });
```

可以理解成：

```text
一直等
直到：

ready == true

或者

到达 deadline
```

如果只是：

```text
等 2 秒
```

通常：

```cpp
wait_for(...)
```

更直观。

如果你本来就有：

```text
明确截止时间
```

那么：

```cpp
wait_until(...)
```

更合适。

---

### 为什么超时通常推荐 `steady_clock`

涉及“持续多久”时：

```cpp
std::chrono::steady_clock
```

一般更合适。

因为它是单调时钟，不会因为：

```text
用户手动改系统时间

NTP 校时

系统时间向前 / 向后跳
```

而突然改变。

所以这种代码：

```cpp
auto deadline =
    std::chrono::steady_clock::now()
    + 2s;
```

通常比基于墙上时间更适合：

```text
超时
计时
等待
```

---

### 一个 condition_variable 能等待多个条件吗

当然可以。

真正决定等待条件的是：

```text
predicate
```

例如：

```cpp
cv.wait(lock, [] {
    return !queue.empty()
        || stopped
        || error;
});
```

表示：

```text
只要下面任何一个发生：

队列有数据
或
程序停止
或
发生错误

就结束等待并继续处理
```

然后醒来后再判断：

```cpp
if (error)
{
    // 处理错误
}
else if (stopped && queue.empty())
{
    // 退出
}
else
{
    // 处理队列任务
}
```

所以：

```text
condition_variable
```

并不关心你到底在等几个条件。

它只负责：

```text
睡
+
醒
```

复杂条件都由：

```text
predicate
```

决定。

---

### 但 predicate 不要写得太乱

如果你开始写：

```cpp
cv.wait(lock, [] {
    return
        a && b
        || c && !d
        || error_code != 0
        || queue.size() > 3
        || ...
});
```

说明共享状态设计可能已经开始变复杂了。

这时候应该考虑：

```text
能不能定义更清楚的状态对象

能不能使用 enum 表示状态

能不能拆分不同等待条件

能不能简化线程之间的协议
```

而不是把所有业务逻辑全部堆进一个巨大 predicate。

---

### `condition_variable_any`

标准库还有：

```cpp
std::condition_variable_any
```

普通：

```cpp
std::condition_variable
```

主要和：

```cpp
std::unique_lock<std::mutex>
```

配合。

而：

```cpp
std::condition_variable_any
```

可以配合更广泛的锁类型。

所以它：

```text
更灵活
```

但通常：

```text
也可能有更多开销
```

如果只是：

```cpp
std::mutex
```

普通场景优先：

```cpp
std::condition_variable
```

就够了。

---

### `condition_variable_any` 和 `stop_token`

C++20 中：

```cpp
std::condition_variable_any
```

还有一个很实用的地方：

> 可以和 `std::stop_token` 配合等待。

这对于：

```cpp
std::jthread
```

很方便。

例如 worker 正在：

```text
等待队列任务
```

同时程序又希望：

```text
request_stop()
```

以后能把它从等待中唤醒并退出。

这时：

```cpp
condition_variable_any
+
stop_token
```

就可以把：

```text
业务条件成立

或者

收到停止请求
```

组合进同一个等待过程里。

---

### condition_variable 和 atomic::wait 怎么选

C++20 以后：

```cpp
std::atomic
```

也支持：

```cpp
wait()
notify_one()
notify_all()
```

如果只是：

```text
等待一个 atomic 值变化
```

例如：

```cpp
std::atomic<bool> ready{false};

ready.wait(false);
```

这种场景：

```text
atomic::wait
```

会非常方便。

---

如果等待条件是：

```text
queue 非空

或者 finished == true

或者 error != 0
```

这类涉及：

```text
多个共享变量

容器

复杂业务状态
```

通常：

```text
mutex
+
condition_variable
+
predicate
```

更加自然。

可以简单记成：

```text
只围绕一个 atomic 值等待
→ atomic::wait()

围绕一组共享业务状态等待
→ condition_variable
```

---

## 常见错误

### 错误：把 notify 当成状态

错误想法：

```text
“notify 已经调用了，
所以以后 wait 一定知道。”
```

不对。

```cpp
cv.notify_one();
```

不会永久保存一条消息。

真正的状态必须放在：

```cpp
ready
```

或者：

```cpp
queue
```

或者：

```cpp
finished
```

中。

---

### 错误：不用 predicate 处理虚假唤醒

不推荐：

```cpp
cv.wait(lock);

use_data();
```

应该：

```cpp
cv.wait(lock, [] {
    return ready;
});
```

---

### 错误：等待方加锁，修改方却裸写共享变量

例如：

```cpp
bool ready;
```

worker：

```cpp
std::unique_lock lock(mutex);

cv.wait(lock, [] {
    return ready;
});
```

main 却：

```cpp
ready = true; // 没有锁
```

这样仍然可能产生数据竞争。

对于这种普通共享状态：

```text
读和写
```

都应该遵守同一套 mutex 同步规则。

---

### 错误：拿着锁做耗时工作

错误：

```cpp
std::unique_lock lock(mutex);

auto task = queue.front();
queue.pop();

do_heavy_work(task);
```

更好：

```cpp
Task task;

{
    std::unique_lock lock(mutex);

    task = queue.front();
    queue.pop();
}

do_heavy_work(task);
```

尽快释放锁。

---

### 错误：停止时只修改 finished，却忘了 notify

例如：

```cpp
{
    std::lock_guard lock(mutex);
    finished = true;
}
```

如果 worker 正在：

```cpp
cv.wait(...)
```

它可能仍然睡着。

通常还需要：

```cpp
cv.notify_all();
```

把等待线程叫醒，让它们重新检查：

```cpp
finished
```

---

### 错误：只考虑“现在没任务”，没考虑“以后也不会有任务”

生产者消费者模型必须设计：

```text
什么时候表示生产彻底结束
```

否则 consumer 很容易：

```text
队列空
↓
继续 wait
↓
但 producer 已经退出
↓
以后永远没人再 notify
```

所以通常需要：

```cpp
bool finished;
```

或者其他明确的结束状态。

---

## 最后总结

`std::condition_variable` 最核心的不是：

```cpp
notify_one()
```

而是这一整套关系：

```text
共享状态
+
mutex
+
condition_variable
+
predicate
```

可以这样记：

```text
共享状态
→ 记录事实

mutex
→ 保护事实

condition_variable
→ 负责睡眠和唤醒

predicate
→ 决定醒来后到底能不能继续
```

`wait()` 的核心流程：

```text
拿着 mutex
↓
predicate 不成立
↓
释放 mutex + 睡眠
↓
收到 notify
↓
重新获得 mutex
↓
重新检查 predicate
↓
条件成立才真正返回
```

另外几个重点：

```text
notify_one()
→ 唤醒一个等待者

notify_all()
→ 唤醒所有等待者

wait_for()
→ 最多等一段时间

wait_until()
→ 最多等到某个时间点
```

最后一定记住：

> **condition_variable 没有“记忆”，真正保存状态的是共享变量。**

以及：

> **被 notify 唤醒不代表条件成立，最终永远要重新检查 predicate。**
