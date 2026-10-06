---
vinciId: 297e0beb-f540-4bd1-a83c-54698c6d17d3
title: future、async、promise 与 packaged_task
description: "`std::thread` 很适合表达："
authors:
  - dongjiahui
publishedAt: 2026-10-06T09:22:17.329Z
updatedAt: 2026-10-06T09:22:17.329Z
---

`std::thread` 很适合表达：

```text
启动一个线程
↓
在线程里执行某个函数
```

但它本身并不直接解决：

```text
线程函数怎么把返回值交回来？

线程里抛出的异常怎么传回来？

主线程怎么知道结果准备好了？

多个线程之间怎么建立“结果通道”？
```

C++11 在 `<future>` 中提供了一组更偏向“任务与结果”的并发工具：

```cpp
std::future
std::async
std::promise
std::packaged_task
std::shared_future
```

它们的核心思想可以概括成一句话：

> **结果现在还没有，但未来会产生；先拿到一个 future，之后再等待或取得结果。**

所以：

```text
std::thread
→ 更关心“线程本身”

std::future / std::async / std::promise
→ 更关心“任务最终产生什么结果”
```

---

## future 和 async 基础

### `std::future<T>` 是什么

`std::future<T>` 可以理解成：

> **一个“未来结果”的读取端。**

例如：

```cpp
std::future<int>
```

表示：

```text
未来某个时刻
会有一个 int 结果
```

如果任务没有返回值，则可以使用：

```cpp
std::future<void>
```

---

### future 背后其实有一个 shared state

`future` 本身并不是结果，也不是线程。

它关联的是一个：

> **共享状态（shared state）**

可以理解成：

```text
生产者
写入结果 / 异常
      ↓
 shared state
      ↓
   future
读取结果 / 异常
```

共享状态里可能保存：

```text
任务返回值

任务抛出的异常

结果是否已经准备好

等待和同步所需的信息
```

所以：

```cpp
future.get();
```

真正做的事情可以理解成：

```text
如果结果还没准备好
→ 等待

结果准备好以后
→ 取出结果

如果任务保存的是异常
→ 在这里重新抛出
```

---

### 最简单的 `std::async`

来看一个最简单的例子：

```cpp
#include <future>
#include <print>

int calculate(int a, int b)
{
    return a + b;
}

int main()
{
    std::future<int> result = std::async(
        std::launch::async,
        calculate,
        10,
        20);

    std::println("main continues");

    std::println("{}", result.get());
}
```

可能输出：

```text
main continues
30
```

这里：

```cpp
std::async(
    std::launch::async,
    calculate,
    10,
    20);
```

可以理解成：

```text
提交 calculate(10, 20) 这个任务
↓
让它异步执行
↓
返回一个 future<int>
```

这个：

```cpp
std::future<int> result
```

就是未来结果的读取端。

主线程继续执行：

```cpp
std::println("main continues");
```

直到：

```cpp
result.get();
```

如果任务已经完成：

```text
直接取得结果
```

如果任务还没完成：

```text
当前线程在这里等待
```

所以：

> `std::async` 负责“提交任务”，`std::future` 负责“以后拿结果”。

---

### `async` 的参数和 `thread` 很像

基本形式：

```cpp
std::async(policy, callable, arg1, arg2, ...);
```

例如：

```cpp
auto result = std::async(
    std::launch::async,
    calculate,
    10,
    20);
```

其中：

```text
policy
→ 任务怎么启动

callable
→ 要执行的函数 / lambda / 函数对象

arg1, arg2...
→ 传给这个可调用对象的参数
```

可调用对象可以是：

```text
普通函数

lambda

函数对象

成员函数
```

如果需要传引用，同样需要考虑：

```cpp
std::ref(x)
```

以及引用对象的生命周期问题。

---

## async 启动策略

### `std::launch::async`

`std::async` 最常用的启动策略之一是：

```cpp
std::launch::async
```

例如：

```cpp
auto result = std::async(
    std::launch::async,
    task);
```

它表示：

> **要求这个任务异步执行。**

可以先直观理解成：

```text
现在把任务安排出去执行

当前线程继续干自己的事

以后通过 future 等结果
```

重点是：

```text
调用方关心的是任务和结果
而不是手动管理 thread 对象
```

所以它和：

```cpp
std::thread
```

的使用视角不一样。

`std::thread` 更像：

```text
我要创建并管理一个线程
```

`std::async` 更像：

```text
我要提交一个任务
最后拿它的结果
```

---

### `std::launch::deferred`

另一个启动策略是：

```cpp
std::launch::deferred
```

例如：

```cpp
auto result = std::async(
    std::launch::deferred,
    task);
```

它并不会立刻把任务放到后台执行。

它表示：

> **先把这个任务记下来，等真正有人等待结果时再执行。**

例如：

```cpp
#include <future>
#include <print>
#include <thread>

int task()
{
    std::println(
        "task thread: {}",
        std::this_thread::get_id());

    return 42;
}

int main()
{
    std::println(
        "main thread: {}",
        std::this_thread::get_id());

    auto result = std::async(
        std::launch::deferred,
        task);

    std::println("before get");

    std::println("{}", result.get());
}
```

可以理解成：

```text
创建 async
↓
task 此时还没执行
↓
main 继续运行
↓
执行 result.get()
↓
就在调用 get() 的线程中执行 task()
↓
得到结果
```

所以 deferred 并不是：

```text
后台线程晚一点执行
```

而更接近：

```text
延迟到真正需要结果时
再由等待结果的线程执行
```

---

### `async` 和 `deferred` 的区别

可以简单记成：

```text
std::launch::async

→ 任务异步执行
→ 当前线程可以继续
→ future 以后取结果
```

而：

```text
std::launch::deferred

→ 任务先不执行
→ 第一次 get()/wait() 时才执行
→ 由调用等待函数的线程执行
```

所以：

```text
async
≈ “现在就安排执行”

deferred
≈ “先记下来，用到结果时再执行”
```

---

### 不写 launch policy 会怎样

可以这样写：

```cpp
auto result = std::async(task);
```

但这种情况下，标准允许实现自己选择：

```text
async

或者

deferred
```

所以不要把：

```cpp
std::async(task);
```

自动理解成：

```text
一定立刻创建后台线程并发执行
```

如果你明确要求：

> 我就是想让任务真正异步执行。

那最好显式写：

```cpp
std::launch::async
```

例如：

```cpp
auto result = std::async(
    std::launch::async,
    task);
```

---

## future 等待与异常

### `future::get()`

`get()` 是 `future` 最重要的函数之一：

```cpp
auto value = future.get();
```

它做两件事：

```text
1. 等待结果准备好

2. 取得结果
```

例如：

```cpp
auto result = std::async(
    std::launch::async,
    [] {
        return 42;
    });

int value = result.get();
```

如果任务还没执行完：

```text
get()
→ 等
```

任务结束以后：

```text
get()
→ 返回 42
```

---

### 普通 `future::get()` 通常只能调用一次

例如：

```cpp
auto result = std::async(
    std::launch::async,
    [] {
        return 42;
    });

int value = result.get();
```

这里：

```cpp
result.get();
```

把这个 future 里的结果取走了。

之后：

```cpp
result.valid()
```

通常会变成：

```text
false
```

所以普通 `std::future` 可以理解成：

> **一次性的取件凭证。**

```text
结果准备好
↓
get()
↓
结果被取走
↓
future 不再关联有效共享状态
```

因此不要这样写：

```cpp
result.get();
result.get();
```

第二次 `get()` 会对无效 future 操作，通常会抛出：

```cpp
std::future_error
```

---

### `future::valid()`

可以通过：

```cpp
future.valid()
```

查询：

> 这个 future 当前是否还关联着有效的共享状态。

例如：

```cpp
auto result = std::async(
    std::launch::async,
    [] {
        return 42;
    });

std::println("{}", result.valid());

result.get();

std::println("{}", result.valid());
```

可以理解成：

```text
get() 前
→ future 还关联共享状态

get() 后
→ 普通 future 的结果已经被消费
```

所以：

```cpp
valid()
```

不是：

```text
“任务成功了吗？”
```

而是：

```text
“这个 future 现在还有没有关联一个有效共享状态？”
```

---

### `future::wait()`

如果你只想：

> 等任务结束，但暂时不取结果。

可以：

```cpp
future.wait();
```

例如：

```cpp
result.wait();

std::println("task finished");

int value = result.get();
```

这里：

```cpp
wait()
```

只负责等待。

它不会把结果取走。

所以之后仍然可以：

```cpp
result.get();
```

可以记成：

```text
wait()
→ 等待，但不消费结果

get()
→ 等待 + 取走结果
```

---

### `wait()` 不会把任务异常直接抛出来

假设异步任务抛出了异常。

调用：

```cpp
future.wait();
```

只是等待共享状态变为 ready。

它不会在这里重新抛出任务异常。

异常通常是在：

```cpp
future.get();
```

时重新抛出。

所以：

```text
wait()
→ 确认任务已经结束

get()
→ 真正取得“结果或者异常”
```

---

### `future::wait_for()`

可以等待一段时间：

```cpp
auto status = result.wait_for(
    std::chrono::milliseconds(100));
```

返回值可能是：

```cpp
std::future_status::ready
std::future_status::timeout
std::future_status::deferred
```

分别表示：

```text
ready
→ 结果已经准备好了

timeout
→ 等待时间到了，但结果还没准备好

deferred
→ 这个任务使用 deferred 策略
```

示例：

```cpp
#include <chrono>
#include <future>
#include <print>
#include <thread>

using namespace std::chrono_literals;

int main()
{
    auto result = std::async(
        std::launch::async,
        [] {
            std::this_thread::sleep_for(1s);
            return 42;
        });

    if (result.wait_for(100ms)
        == std::future_status::timeout)
    {
        std::println("not ready yet");
    }

    std::println("{}", result.get());
}
```

输出：

```text
not ready yet
42
```

整个过程是：

```text
任务开始
↓
main 最多等 100ms
↓
任务还没完成
↓
wait_for() 返回 timeout
↓
main 继续
↓
最后 get() 等待并取得结果
```

---

### `future_status::deferred`

如果：

```cpp
wait_for(...)
```

返回：

```cpp
std::future_status::deferred
```

说明：

> 这个任务根本不是正在后台慢慢执行。

它只是一个延迟任务。

例如：

```cpp
auto result = std::async(
    std::launch::deferred,
    task);
```

此时不断写：

```cpp
result.wait_for(100ms);
result.wait_for(100ms);
result.wait_for(100ms);
```

并不会让它“等着等着就在后台完成”。

因为 deferred 的逻辑是：

```text
真正执行 get()/wait()
↓
调用这些函数的线程亲自执行任务
```

---

### 异步任务里的异常会通过 future 传回来

这是 `future` 很重要的能力。

例如：

```cpp
#include <future>
#include <print>
#include <stdexcept>

int main()
{
    auto result = std::async(
        std::launch::async,
        []() -> int {
            throw std::runtime_error("task failed");
        });

    try
    {
        std::println("{}", result.get());
    }
    catch (const std::exception& e)
    {
        std::println("{}", e.what());
    }
}
```

输出：

```text
task failed
```

这里任务线程抛出的：

```cpp
std::runtime_error
```

不会直接从工作线程“跳到”主线程。

实际逻辑是：

```text
异步任务抛异常
↓
异常被保存到 shared state
↓
main 调用 future.get()
↓
get() 发现共享状态里保存的是异常
↓
在 main 这里重新抛出
```

所以：

```cpp
future.get();
```

取得的不一定是：

```text
正常返回值
```

也可能是：

```text
任务失败时保存的异常
```

这使得异步任务的错误处理很像普通函数：

```text
普通函数
→ 调用处 catch

异步任务
→ future.get() 处 catch
```

---

## `std::promise<T>`

前面的：

```cpp
std::async
```

会自动创建结果通道。

但有时候我们想自己控制：

```text
什么时候写入结果

在哪个线程写入结果

写入正常值还是异常
```

这时可以使用：

```cpp
std::promise<T>
```

它可以理解成：

> **future 对应结果通道的“写入端”。**

关系是：

```text
promise<T>
   │
   │ set_value()
   │ set_exception()
   ↓
shared state
   ↓
future<T>
   │
   │ get()
   ↓
读取结果
```

所以最简单的记忆方式是：

```text
promise
→ 写

future
→ 读
```

---

### `promise::get_future()`

创建：

```cpp
std::promise<int> promise;
```

以后，可以通过：

```cpp
std::future<int> future = promise.get_future();
```

获得和这个 promise 关联的读取端。

于是：

```text
promise
和
future
```

通过同一个 shared state 连接起来。

---

### promise 最简单的例子

```cpp
#include <future>
#include <print>
#include <thread>

void producer(std::promise<int> promise)
{
    promise.set_value(42);
}

int main()
{
    std::promise<int> promise;

    std::future<int> future =
        promise.get_future();

    std::thread thread(
        producer,
        std::move(promise));

    std::println("{}", future.get());

    thread.join();
}
```

输出：

```text
42
```

整个过程可以理解成：

```text
main 创建 promise
↓
main 从 promise 拿到 future
↓
main 把 promise 移动给 worker
↓
worker 调用 set_value(42)
↓
42 被写入 shared state
↓
main 的 future.get()
↓
读出 42
```

---

### 为什么 promise 要 `std::move`

`std::promise` 不能随意复制。

它是：

```text
move-only
```

所以：

```cpp
std::thread thread(
    producer,
    std::move(promise));
```

是在把 promise 的所有权移动给工作线程。

可以理解成：

```text
main
→ 保留 future，负责读

worker
→ 拿走 promise，负责写
```

这样：

```text
worker 写结果
↓
shared state
↓
main 读结果
```

就建立起来了。

---

### `promise::set_value()`

生产端可以：

```cpp
promise.set_value(42);
```

它表示：

> 把最终结果写入共享状态。

写入以后：

```text
future
→ ready
```

等待中的：

```cpp
future.get();
```

就可以继续执行。

对于：

```cpp
std::promise<void>
```

没有具体返回值。

可以：

```cpp
promise.set_value();
```

表示：

> 任务已经成功完成。

---

### promise 的结果通常只能设置一次

例如：

```cpp
promise.set_value(42);
promise.set_value(100);
```

是不对的。

同一个共享状态只能完成一次。

第一次：

```cpp
set_value(42);
```

已经把结果确定了。

第二次再次：

```cpp
set_value(...)
```

通常会抛：

```cpp
std::future_error
```

所以可以理解成：

```text
一个 promise
→ 最终只能提交一次最终结果
```

---

### `promise::set_exception()`

promise 不仅能写正常值：

```cpp
promise.set_value(result);
```

也可以写异常：

```cpp
promise.set_exception(
    std::current_exception());
```

常见写法：

```cpp
void producer(std::promise<int> promise)
{
    try
    {
        int result = do_work();

        promise.set_value(result);
    }
    catch (...)
    {
        promise.set_exception(
            std::current_exception());
    }
}
```

这样 consumer：

```cpp
future.get();
```

会：

```text
任务成功
→ 返回结果

任务失败
→ 重新抛出保存的异常
```

---

### 完整的 promise 异常示例

```cpp
#include <exception>
#include <future>
#include <print>
#include <stdexcept>
#include <thread>

void producer(std::promise<int> promise)
{
    try
    {
        throw std::runtime_error("failed");
    }
    catch (...)
    {
        promise.set_exception(
            std::current_exception());
    }
}

int main()
{
    std::promise<int> promise;

    auto future = promise.get_future();

    std::thread thread(
        producer,
        std::move(promise));

    try
    {
        std::println("{}", future.get());
    }
    catch (const std::exception& e)
    {
        std::println("{}", e.what());
    }

    thread.join();
}
```

输出：

```text
failed
```

流程是：

```text
worker 发生异常
↓
catch 捕获异常
↓
promise.set_exception(...)
↓
异常进入 shared state
↓
main 调用 future.get()
↓
异常在 main 中重新抛出
```

---

### 什么是 broken promise

假设：

```cpp
std::future<int> future;

{
    std::promise<int> promise;

    future = promise.get_future();
}
```

这里 promise 离开作用域时：

```text
没有 set_value()

也没有 set_exception()
```

但 future 还在等待结果。

这种情况叫：

> **broken promise**

也就是：

```text
原本负责生产结果的人已经消失

但结果还没有产生
```

标准库不会让 future 永远傻等。

之后：

```cpp
future.get();
```

会得到一个对应的：

```cpp
std::future_error
```

可以把它理解成：

> “结果生产端已经断了，不会再有正常结果了。”

---

### promise 的所有结束路径都应该有明确结果

如果使用：

```cpp
std::promise
```

生产端最好认真处理：

```text
成功
→ set_value()

失败
→ set_exception()

提前退出 / 取消
→ 也要设计清楚最终结果
```

否则就容易出现：

```text
broken promise
```

---

## `std::packaged_task`

`std::packaged_task` 解决的是另一类问题。

假设已经有：

```cpp
int add(int a, int b)
{
    return a + b;
}
```

现在想把它变成：

```text
一个“执行以后自动把结果写到 future”的任务
```

可以使用：

```cpp
std::packaged_task
```

它可以理解成：

> **把一个可调用对象包装成一个“自带 future 结果通道”的任务对象。**

---

### packaged_task 最简单的例子

```cpp
#include <future>
#include <print>

int add(int a, int b)
{
    return a + b;
}

int main()
{
    std::packaged_task<int(int, int)> task(add);

    std::future<int> result =
        task.get_future();

    task(10, 20);

    std::println("{}", result.get());
}
```

输出：

```text
30
```

这里：

```cpp
std::packaged_task<int(int, int)>
```

表示：

```text
这个任务：

接收两个 int
返回一个 int
```

创建：

```cpp
std::packaged_task<int(int, int)> task(add);
```

以后：

```cpp
task(10, 20);
```

实际上会：

```text
调用 add(10, 20)
↓
得到返回值 30
↓
自动把 30 写入 shared state
↓
result.get() 取得 30
```

---

### packaged_task 和 promise 最大的区别

`promise` 是：

> **你自己决定什么时候写结果。**

例如：

```cpp
promise.set_value(42);
```

而 `packaged_task` 是：

> **你给它一个函数，执行这个函数时，它自动把函数的返回值写入结果通道。**

所以：

```text
promise
→ 手动写结果

packaged_task
→ 执行函数，自动写结果
```

---

### `packaged_task` 会自动传递异常

假设被包装的函数：

```cpp
int work()
{
    throw std::runtime_error("failed");
}
```

包装成：

```cpp
std::packaged_task<int()> task(work);
```

执行：

```cpp
task();
```

如果 `work()` 抛异常，`packaged_task` 会把异常保存到共享状态。

之后：

```cpp
future.get();
```

会重新抛出这个异常。

所以它不只会自动处理：

```text
返回值
```

也会自动处理：

```text
异常
```

---

### `packaged_task` 和 thread 配合

可以把 packaged task 交给线程执行：

```cpp
#include <future>
#include <print>
#include <thread>

int calculate()
{
    return 42;
}

int main()
{
    std::packaged_task<int()> task(calculate);

    std::future<int> result =
        task.get_future();

    std::thread thread(
        std::move(task));

    std::println("{}", result.get());

    thread.join();
}
```

输出：

```text
42
```

这里：

```cpp
std::thread thread(
    std::move(task));
```

表示：

```text
把 task 移动给新线程
↓
新线程执行 task()
↓
task 内部调用 calculate()
↓
返回值写入 shared state
↓
result.get() 得到 42
```

---

### packaged_task 也是 move-only

和 `promise` 类似：

```cpp
std::packaged_task
```

也不能随意复制。

通常需要：

```cpp
std::move(task)
```

把它移动到：

```text
线程

任务队列

worker
```

中。

---

### 为什么 packaged_task 很适合任务队列

线程池通常会有这种思想：

```text
用户提交函数
↓
包装成任务对象
↓
放进任务队列
↓
worker 线程取任务
↓
执行任务
↓
结果写进 shared state
↓
用户拿 future 等结果
```

这正好和：

```cpp
std::packaged_task
```

的设计非常契合。

因为 packaged task 本身已经把：

```text
怎么执行任务
+
结果写到哪里
```

绑定在一起了。

---

### `promise` 和 `packaged_task` 怎么选

可以这样记：

#### `promise`

适合：

```text
我自己决定结果什么时候产生

结果不一定直接来自某个函数返回值

生产过程可能分很多步骤
```

例如：

```cpp
promise.set_value(result);
```

由你自己控制。

---

#### `packaged_task`

适合：

```text
我已经有一个函数 / lambda

我就想把它包装成一个“执行后有 future 结果”的任务
```

例如：

```cpp
std::packaged_task<int()> task(calculate);
```

然后：

```cpp
task();
```

自动把返回值送进 future。

所以最简单的区别是：

```text
promise
→ 手动生产结果

packaged_task
→ 包装可调用对象，自动生产结果
```

---

## `std::shared_future`

普通：

```cpp
std::future<T>
```

更适合：

```text
一个消费者
```

因为：

```cpp
future.get();
```

通常只能消费一次。

如果：

> 多个线程都需要读取同一个异步结果

可以使用：

```cpp
std::shared_future<T>
```

---

### future 转成 shared_future

例如：

```cpp
auto future = std::async(
    std::launch::async,
    [] {
        return 42;
    });

std::shared_future<int> shared =
    future.share();
```

之后可以：

```cpp
shared.get();
shared.get();
shared.get();
```

多个地方都可以读取同一个结果。

---

### shared_future 更像“共享查看”

普通 future：

```text
get()
→ 把结果取走
→ future 失效
```

而 shared future：

```text
get()
→ 查看共享结果
→ 不会因为这次 get() 就把结果消费掉
```

所以：

```text
future
→ 单消费者

shared_future
→ 多消费者
```

例如：

```text
一次性加载配置
↓
得到 shared_future<Config>
↓
多个线程等待同一份配置
↓
全部读取同一个结果
```

---

## 关系与选择

### 这几个东西到底是什么关系

可以把整个 `<future>` 体系理解成：

```text
                 shared state
                     │
       ┌─────────────┴─────────────┐
       │                           │
     写入端                      读取端
       │                           │
       │                        future
       │                    shared_future
       │
       ├─ promise
       │   → 手动写结果
       │
       ├─ packaged_task
       │   → 执行函数后自动写结果
       │
       └─ async
           → 提交任务并自动管理结果通道
```

其中：

```text
future
→ 负责以后读取结果

promise
→ 负责手动写结果

packaged_task
→ 把函数包装成“执行后自动写结果”的任务

async
→ 最方便的高级接口，直接提交任务并得到 future

shared_future
→ 允许多个消费者读取同一个结果
```

---

### `async` 和 `thread` 怎么选

如果你关注的是：

```text
我需要一个长期存在的线程

线程里面一直循环

我要控制它什么时候停止

我要管理生命周期
```

更适合：

```cpp
std::thread
std::jthread
```

例如：

```text
串口接收线程

传感器采集线程

机器人后台 worker

长期循环任务
```

---

如果你关注的是：

```text
我有一个任务

它会算出一个结果

我以后要取得这个结果

任务中的异常也要传回来
```

更适合：

```cpp
std::async
std::future
```

例如：

```text
并行计算一段结果

后台加载文件

异步计算某个值
```

---

### `async` 不是线程池

不要把：

```cpp
std::async
```

理解成：

```text
C++ 标准线程池
```

它并不是标准线程池接口。

如果你需要：

```text
固定数量 worker

不断提交大量小任务

任务队列

长期后台执行

可控停止
```

通常应该考虑：

```text
线程池

std::jthread

专门的任务系统

更完整的异步框架
```

而不是不断：

```cpp
std::async(...)
```

---

## 常见错误

### 错误：认为 `std::async()` 一定创建新线程

例如：

```cpp
std::async(task);
```

如果不指定 policy，标准允许选择：

```text
async
或
deferred
```

如果明确要求真正异步：

```cpp
std::async(
    std::launch::async,
    task);
```

---

### 错误：对普通 future 重复 `get()`

错误：

```cpp
future.get();
future.get();
```

普通 `future` 的结果通常只能消费一次。

---

### 错误：忘记 `get()` 还会重新抛出异常

例如：

```cpp
auto result = std::async(
    std::launch::async,
    task);

result.get();
```

这里不仅可能拿到返回值，也可能抛出任务里的异常。

所以必要时要：

```cpp
try
{
    result.get();
}
catch (...)
{
}
```

---

### 错误：promise 没写结果就被销毁

如果 promise：

```text
既没有 set_value()

也没有 set_exception()
```

就消失了，future 会得到：

```text
broken promise
```

---

### 错误：把 promise / packaged_task 当可复制对象

它们通常是：

```text
move-only
```

跨线程转移时经常需要：

```cpp
std::move(...)
```

---

### 错误：用 async 做长期后台循环

例如：

```cpp
std::async(
    std::launch::async,
    [] {
        while (true)
        {
            ...
        }
    });
```

如果这个任务需要：

```text
明确停止

长期运行

生命周期管理
```

通常：

```cpp
std::jthread
```

更符合语义。

---

## 最后总结

这组工具最核心的区别可以记成：

```text
std::future
→ “以后我要读结果”

std::promise
→ “我手动把结果写进去”

std::packaged_task
→ “我执行这个函数，结果自动写进去”

std::async
→ “帮我提交任务，并直接给我 future”

std::shared_future
→ “多个地方都要读同一个结果”
```

其中：

```text
std::thread / std::jthread
→ 更偏线程生命周期

std::future / std::async
→ 更偏任务与结果
```

如果只记一张图：

```text
          生产结果
             ↓
 promise / packaged_task / async
             ↓
        shared state
             ↓
   future / shared_future
             ↓
          读取结果
```

这就是整个 `<future>` 体系最核心的逻辑。
