---
title: jthread 与 stop_token
description: C++20 提供了：
authors:
- dongjiahui
---

C++20 提供了：

```cpp
std::jthread
```

它可以理解成一个**更适合现代 C++ 的线程类**。

和 `std::thread` 相比，它最重要的改进有两个：

```text
1. 生命周期更安全
   → 析构时自动处理线程，不容易因为漏写 join() 而出问题

2. 自带协作式停止机制
   → 可以通过 stop_token / request_stop() 请求线程安全退出
```

所以这一节最重要的是把两个概念分开：

```text
线程生命周期管理
→ 谁负责等待线程结束

线程停止协议
→ 谁发出停止请求，工作线程怎么响应
```

`std::jthread` 同时解决了这两个问题，但它并不是“自动强杀线程”的工具。

---

## 为什么需要 `std::jthread`

### `std::thread` 的问题

使用普通 `std::thread`：

```cpp
std::thread thread(work);
```

在线程对象析构之前，必须保证它已经：

```cpp
thread.join();
```

或者：

```cpp
thread.detach();
```

否则，如果一个仍然 `joinable()` 的 `std::thread` 被析构，程序会调用：

```cpp
std::terminate();
```

例如：

```cpp
#include <print>
#include <thread>

void work()
{
    std::println("working");
}

int main()
{
    std::thread thread(work);

    // 如果这里忘记 join() / detach()
}
```

程序离开 `main()` 时，`thread` 仍然是 joinable 的，于是会触发 `std::terminate()`。

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1789303640375-2f45e713.webp)

真正麻烦的是，当程序中出现：

```text
异常
提前 return
多个 if / else 分支
复杂资源管理
```

就更容易漏掉 `join()`。

这和手动管理 `new/delete` 有点像：

```text
代码简单时看起来没问题

控制路径一复杂
→ 很容易忘记收尾
```

而现代 C++ 更倾向于通过 RAII 自动管理资源。

线程也一样。

---

### `std::jthread` 会自动处理线程结束

最简单的例子：

```cpp
#include <print>
#include <thread>

void work()
{
    std::println("working");
}

int main()
{
    std::jthread thread(work);
}
```

运行结果：

```text
working
```

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1789303670123-6fc66656.webp)

这里没有写：

```cpp
thread.join();
```

但程序仍然可以正常结束。

因为当：

```cpp
std::jthread thread;
```

离开作用域时，如果它仍然是 joinable 的，析构函数会负责收尾。

可以先粗略理解成：

```text
std::thread
→ 需要自己保证 join() / detach()

std::jthread
→ 析构时自动处理
```

不过 `jthread` 的析构不只是简单地 `join()`。

更准确地说，如果它仍然 joinable，析构过程概念上会做：

```text
request_stop()
      ↓
join()
```

也就是：

> 先请求线程停止，然后等待线程真正结束。

这也是 `jthread` 和普通 `thread` 一个很重要的区别。

---

### `jthread` 不会强制杀死线程

`std::jthread` 使用的是：

> **协作式停止（cooperative cancellation）**

所谓“协作式”，意思是：

```text
某个线程：
发出停止请求
      ↓
工作线程：
看到停止请求
      ↓
工作线程：
自己选择一个安全的位置退出
```

而不是：

```text
request_stop()
      ↓
操作系统立刻把工作线程强行杀掉
```

标准库不会随便把线程从某条机器指令中间直接掐死。

原因很简单。

线程可能正在：

```text
持有 mutex
修改共享数据
写文件
操作硬件
分配 / 释放资源
修改对象内部状态
```

如果在任意位置强制终止线程，很容易把程序状态搞坏。

所以：

```cpp
thread.request_stop();
```

更像是在告诉工作线程：

> “你该停了，请在合适的位置自己退出。”

而不是：

> “现在立刻把你杀掉。”

---

## stop_token 停止协议

### `std::stop_token`

#### `stop_token` 是什么

`std::stop_token` 可以理解成：

> **停止请求的观察端。**

它本身不能发出停止请求，只能查看：

```text
有没有人要求我停止？
```

例如：

```cpp
stop_token.stop_requested();
```

返回：

```text
true  → 已经有人请求停止
false → 目前还没有停止请求
```

`jthread` 内部有一个停止状态，可以粗略理解成：

```text
jthread / stop_source
        ↓
   发出停止请求

   共享停止状态

        ↑
   查看停止请求
        ↑
    stop_token
```

也就是说：

```text
request_stop()
→ 写入“停止请求”

stop_requested()
→ 查看“停止请求”
```

---

### `jthread` 可以自动传入 `stop_token`

来看一个最典型的例子：

```cpp
#include <chrono>
#include <print>
#include <stop_token>
#include <thread>

using namespace std::chrono_literals;

void worker(std::stop_token token)
{
    while (!token.stop_requested())
    {
        std::println("working");
        std::this_thread::sleep_for(100ms);
    }

    std::println("stopping");
}

int main()
{
    std::jthread thread(worker);

    std::this_thread::sleep_for(350ms);

    thread.request_stop();
}
```

可能输出：

```text
working
working
working
working
stopping
```

这里最特别的是：

```cpp
void worker(std::stop_token token)
```

但创建线程时却只写了：

```cpp
std::jthread thread(worker);
```

我们并没有手动传：

```cpp
worker(token);
```

因为 `std::jthread` 发现这个函数可以接收 `std::stop_token`，于是会自动把自己的 stop token 传进去。

可以近似理解成：

```text
std::jthread thread(worker);

内部效果类似：

worker(jthread 自动提供的 stop_token);
```

这里不可以显式填参数，比如`std::jthread thread(worker, thread.get_stop_token());`，
这是非法的，原因非常直接：在执行构造函数参数时，thread 对象还没有构造完成，所以你还不能调用`thread.get_stop_token()`。

所以这里直接记住他会自动把自己的`stop token`传入进去就行了，不需要我们手动传入。

---

### `request_stop()`

调用：

```cpp
thread.request_stop();
```

表示：

> 向这个 `jthread` 关联的停止状态发出停止请求。

它只负责“发请求”。

它不会：

```text
强制终止线程
自动跳出 while
直接让 worker return
等待线程结束
```

真正决定什么时候退出的是工作线程。

例如：

```cpp
while (!token.stop_requested())
{
    do_work();
}
```

每轮循环检查：

```cpp
token.stop_requested()
```

一旦发现停止请求，就退出循环。

整个流程是：

```text
main
│
│ 创建 jthread
↓
worker 开始执行
│
│ while (!token.stop_requested())
│
│ working...
│
│ working...
│
↑
main 调用 request_stop()
│
↓
停止状态变成“已请求停止”
│
↓
worker 下一次检查 stop_requested()
│
↓
发现为 true
│
↓
退出循环
```

所以最重要的一句话是：

> `request_stop()` 只是请求停止，真正停止必须由工作线程自己配合。

---

### `request_stop()` 的返回值

`request_stop()` 会返回一个 `bool`：

```cpp
bool result = thread.request_stop();
```

可以粗略理解成：

```text
true
→ 这次调用真的把状态从
  “还没请求停止”
  改成了
  “已经请求停止”

false
→ 之前已经请求过停止
  或者当前停止状态无法产生停止请求
```

普通业务代码经常不需要关心这个返回值。

直接写：

```cpp
thread.request_stop();
```

就可以。

---

### `stop_possible()`

`stop_token` 还可以调用：

```cpp
token.stop_possible();
```

它表示：

> 这个 token 是否关联着一个“有可能收到停止请求”的停止状态。

例如：

```cpp
std::stop_token token;
```

这是一个默认构造的 token，它没有关联正常的停止状态。

此时通常：

```text
token.stop_possible()  == false
token.stop_requested() == false
```

而 `std::jthread` 自动提供的 token 通常是：

```text
stop_possible() == true
```

对于普通使用来说，最常用的还是：

```cpp
stop_requested()
```

`stop_possible()` 更多是在需要判断“这个 token 到底支不支持停止协议”时使用。

---

### `jthread` 析构时到底发生什么

假设：

```cpp
{
    std::jthread thread(worker);
}
```

执行到右花括号：

```cpp
}
```

`thread` 开始析构。

如果它仍然是 joinable 的，那么概念上会：

```text
thread.request_stop();
        ↓
thread.join();
```

所以：

```cpp
{
    std::jthread thread(worker);
}
```

可以理解成退出作用域时自动做了：

```cpp
thread.request_stop();
thread.join();
```

这也是为什么这种代码可以正常工作：

```cpp
#include <print>
#include <stop_token>
#include <thread>

void worker(std::stop_token token)
{
    while (!token.stop_requested())
    {
        // work
    }

    std::println("stopped");
}

int main()
{
    std::jthread thread(worker);
}
```

`main()` 结束时：

```text
jthread 析构
    ↓
request_stop()
    ↓
worker 看到停止请求
    ↓
worker 退出
    ↓
join() 等待结束
    ↓
析构完成
```

---

### 但析构也不能“凭空让线程退出”

下面这个线程完全不检查 `stop_token`：

```cpp
void worker(std::stop_token)
{
    for (;;)   //死循环，效率比while(true)高
    {
    }
}
```

然后：

```cpp
int main()
{
    std::jthread thread(worker);
}
```

离开 `main()` 时：

```text
jthread 析构
    ↓
request_stop()
    ↓
停止请求确实发出了
    ↓
但是 worker 根本不检查
    ↓
for(;;) 继续运行
    ↓
析构函数接着 join()
    ↓
join() 一直等
```

程序就会一直卡在那里。

因此：

> `jthread` 提供的是停止协议，不是强制终止能力。

所以对于长期运行的 worker，一般应该在合适的位置检查：

```cpp
token.stop_requested()
```

例如：

```cpp
while (!token.stop_requested())
{
    do_some_work();
}
```

而不是只在线程开头检查一次：

```cpp
if (token.stop_requested())
{
    return;
}

// 后面运行几个小时都不再检查
```

---

### 阻塞操作也会影响停止速度

即使代码写成：

```cpp
while (!token.stop_requested())
{
    blocking_operation();
}
```

也不代表线程一定能马上停止。

假设：

```cpp
blocking_operation();
```

一次会阻塞 30 秒。

那么即使外部已经执行：

```cpp
thread.request_stop();
```

工作线程也必须先从：

```cpp
blocking_operation();
```

返回，才能进行下一轮：

```cpp
token.stop_requested()
```

所以停止响应速度还取决于工作线程内部在干什么。

对于需要快速停止的线程，要考虑：

```text
阻塞函数有没有超时

阻塞操作能不能被唤醒

能不能把长任务拆成多个小步骤

能不能在多个安全点检查 stop_requested()
```

例如：

```cpp
while (!token.stop_requested())
{
    do_small_step();
}
```

通常比：

```cpp
while (!token.stop_requested())
{
    do_one_huge_blocking_job();
}
```

更容易快速响应停止请求。

---

### `std::stop_source`

前面说过：

```text
stop_token
→ 观察停止请求
```

那么谁负责发出停止请求？

除了 `jthread.request_stop()`，标准库还提供：

```cpp
std::stop_source
```

它可以理解成：

> **停止请求的控制端。**

例如：

```cpp
#include <print>
#include <stop_token>

int main()
{
    std::stop_source source;

    std::stop_token token = source.get_token();

    std::println("{}", token.stop_requested());

    source.request_stop();

    std::println("{}", token.stop_requested());
}
```

运行结果：

```text
false
true
```

整个关系可以理解成：

```text
stop_source
    │
    │ request_stop()
    ↓
共享停止状态
    ↑
    │ stop_requested()
stop_token
```

所以：

```cpp
std::stop_source source;
```

负责：

```text
发请求
```

而：

```cpp
std::stop_token token = source.get_token();
```

负责：

```text
看请求
```

---

### 多个 `stop_token` 可以观察同一个停止状态

例如：

```cpp
std::stop_source source;

std::stop_token token1 = source.get_token();
std::stop_token token2 = source.get_token();
std::stop_token token3 = source.get_token();
```

这三个 token 都关联到同一个停止状态。

当：

```cpp
source.request_stop();
```

之后：

```cpp
token1.stop_requested()
token2.stop_requested()
token3.stop_requested()
```

都会变成：

```text
true
```

所以 `stop_source + stop_token` 很适合表达：

```text
一个管理者
    ↓
统一发出停止请求
    ↓
多个任务 / 对象
    ↓
一起观察同一个停止状态
```

---

### `std::stop_callback`

有时候我们不想一直手动检查：

```cpp
token.stop_requested()
```

而是希望：

> 一旦停止请求出现，就自动执行某个操作。

可以使用：

```cpp
std::stop_callback
```

例如：

```cpp
#include <print>
#include <stop_token>

int main()
{
    std::stop_source source;
    std::stop_token token = source.get_token();

    std::stop_callback callback(token, [] {
        std::println("stop requested");
    });

    source.request_stop();
}
```

运行结果：

```text
stop requested
```

也就是说：

```text
request_stop()
      ↓
停止状态改变
      ↓
已经注册的 stop_callback 被调用
```

他如果是`jthread`调用的话，那么顺序是：

```text
request_stop()
    ↓
同步执行 stop_callback
    ↓
request_stop() 返回
    ↓
join()
```

也就是说，callback 先于 join() 完成。std::jthread 的析构本身就是先调用 request_stop()，再调用 join()。

它很适合干一些很轻量的事情，例如：

```text
设置状态

唤醒某个等待对象

通知某个组件开始收尾
```

但不建议在 callback 里面做：

```text
复杂计算
长时间 IO
长时间阻塞
大量锁操作
```

因为停止路径本身应该尽量简单、可控。

还有一个细节：

如果注册 callback 时，停止请求已经发生，那么 callback 可能会在注册过程中直接执行。

所以不要假设：

```text
callback 一定会在未来某个时间
由另一个线程执行
```

---

## jthread 的线程函数和生命周期操作

### `jthread` 不一定非要使用 `stop_token`

`std::jthread` 也可以像普通 `std::thread` 一样运行普通函数。

例如：

```cpp
#include <print>
#include <thread>

void work(int value)
{
    std::println("{}", value);
}

int main()
{
    std::jthread thread(work, 42);
}
```

运行结果：

```text
42
```

这里：

```cpp
void work(int value)
```

根本没有 `stop_token`。

没有关系。

`jthread` 会判断这个函数能不能接收自动提供的 stop token。

可以简单理解成：

```text
如果函数可以这样调用：

worker(stop_token, args...)

→ 自动传入 stop_token

否则如果函数只能这样调用：

worker(args...)

→ 就像普通 thread 一样调用
```

所以两种写法都可以：

```cpp
void worker(std::stop_token token);
```

以及：

```cpp
void worker();
```

---

### 带额外参数的 `stop_token` 线程函数

例如：

```cpp
#include <print>
#include <stop_token>
#include <thread>

void worker(std::stop_token token, int id)
{
    std::println("worker {}", id);

    while (!token.stop_requested())
    {
        // work
    }
}

int main()
{
    std::jthread thread(worker, 7);

    thread.request_stop();
}
```

这里：

```cpp
std::jthread thread(worker, 7);
```

可以理解成最终调用：

```cpp
worker(自动提供的 stop_token, 7);
```

所以如果函数需要 stop token，参数顺序通常是：

```cpp
void worker(std::stop_token token, 参数1, 参数2, ...)
```

例如：

```cpp
void worker(
    std::stop_token token,
    int id,
    std::string name);
```

创建：

```cpp
std::jthread thread(worker, 7, "camera");
```

相当于：

```text
worker(
    自动提供的 stop_token,
    7,
    "camera"
)
```

---

### `jthread` 仍然可以手动 `join()`

虽然 `jthread` 会自动 join，但你仍然可以自己写：

```cpp
std::jthread thread(work);

thread.join();
```

执行完：

```cpp
thread.join();
```

以后：

```cpp
thread.joinable()
```

会变成：

```text
false
```

因为这个 `jthread` 已经不再关联一个可 join 的执行线程。

所以：

```text
自动 join
```

并不代表：

```text
不能手动 join
```

它只是意味着：

> 如果你没手动处理，析构函数会帮你安全收尾。

---

### `jthread` 也可以 `detach()`，但通常不推荐

`jthread` 仍然提供：

```cpp
thread.detach();
```

但这样做以后：

```text
jthread 不再拥有这个线程

析构时也没办法再 join 它
```

于是 `jthread` 最重要的 RAII 生命周期优势基本就没有了。

所以如果你发现自己准备写：

```cpp
thread.detach();
```

最好先问几个问题：

```text
这个线程以后由谁负责？

程序退出时它怎么结束？

它访问的对象会不会已经析构？

它访问的资源能活多久？
```

除非你非常明确地知道线程生命周期怎么管理，否则一般不建议随便 detach。

---

### `condition_variable_any` 与 `stop_token`

前面提到一个问题：

如果线程正在：

```cpp
condition_variable.wait(...)
```

里面睡眠，那么只检查：

```cpp
token.stop_requested()
```

是不够的。

因为线程可能根本没机会执行到下一次检查。

C++20 的：

```cpp
std::condition_variable_any
```

提供了可以直接配合 `stop_token` 的等待方式。

例如：

```cpp
#include <condition_variable>
#include <mutex>
#include <stop_token>
#include <thread>

std::mutex mutex;
std::condition_variable_any cv;

bool ready = false;

void worker(std::stop_token token)
{
    std::unique_lock lock(mutex);

    bool condition_met = cv.wait(
        lock,
        token,
        [] {
            return ready;
        });

    if (!condition_met)
    {
        // 没等到 ready == true，
        // 而是因为停止请求结束等待
        return;
    }

    // ready == true
}
```

可以把这次等待理解成：

```text
等待两个条件中的任意一个：

1. ready == true

或者

2. 收到停止请求
```

如果：

```cpp
condition_met == true
```

表示谓词：

```cpp
ready == true
```

成立。

如果：

```cpp
condition_met == false
```

说明这次等待并不是因为 `ready` 成立而结束，通常就是因为收到了停止请求。

这样工作线程即使正在条件变量里等待，也能够响应 `jthread` 的停止协议。

---

## 选择与常见错误

### `std::thread` 和 `std::jthread` 对比

| 特性 | `std::thread` | `std::jthread` |
|---|---:|---:|
| 引入版本 | C++11 | C++20 |
| 创建线程 | ✅ | ✅ |
| 手动 `join()` | ✅ | ✅ |
| 手动 `detach()` | ✅ | ✅ |
| 析构时自动收尾 | ❌ | ✅ |
| joinable 状态析构会 `terminate()` | ✅ | ❌ |
| 自带停止请求机制 | ❌ | ✅ |
| 自动提供 `stop_token` | ❌ | ✅ |
| 适合长期可停止 worker | 一般 | ✅ |

如果项目可以使用 C++20，对于这种线程：

```text
启动
↓
持续运行
↓
程序退出时请求停止
↓
安全结束
```

通常优先考虑：

```cpp
std::jthread
```

---

### `atomic<bool> running` 和 `stop_token` 怎么选

传统代码经常这样写：

```cpp
std::atomic<bool> running{true};

void worker()
{
    while (running.load())
    {
        // work
    }
}
```

停止时：

```cpp
running.store(false);
```

这种写法没有问题。

对于非常简单的线程，它甚至很直观。

例如：

```text
running == true
→ 继续运行

running == false
→ 停止
```

但是：

```cpp
std::stop_token
```

提供的是一套标准化的停止协议。

它可以和：

```text
std::jthread

std::stop_source

std::stop_callback

std::condition_variable_any
```

直接配合。

可以理解成：

```text
atomic<bool>

→ 只是一个线程安全的 bool
→ “true / false 到底是什么意思”由你自己规定
```

而：

```text
stop_token

→ 专门表达“停止请求”
→ 标准库里的多个工具都认识这种协议
```

因此：

```text
非常简单的开关
→ atomic<bool> 完全可以

明确的线程停止生命周期
→ jthread + stop_token 更合适
```

---

### 常见错误

#### 错误：以为 `request_stop()` 会强制杀死线程

错误理解：

```text
request_stop()
→ 线程立刻消失
```

正确理解：

```text
request_stop()
→ 发出停止请求
→ 工作线程自己检查并退出
```

---

#### 错误：worker 从来不检查 `stop_requested()`

例如：

```cpp
void worker(std::stop_token)
{
    while (true)
    {
        do_work();
    }
}
```

即使 `jthread` 析构时自动：

```cpp
request_stop();
```

这个线程也不会退出。

最终析构中的：

```cpp
join();
```

会一直等。

---

#### 错误：检查了停止请求，但中间阻塞太久

例如：

```cpp
while (!token.stop_requested())
{
    blocking_operation(); // 一卡几十秒
}
```

停止请求虽然已经发出，但线程要等到阻塞操作返回后才能再次检查。

---

#### 错误：用了 `jthread` 又随手 `detach()`

这样会失去：

```text
自动 join
自动停止请求
RAII 生命周期管理
```

这些主要优势。

---

#### 错误：在 `stop_callback` 里做大量复杂工作

callback 更适合：

```text
轻量通知
设置状态
唤醒等待者
```

而不是承担复杂业务逻辑。

---

#### 错误：以为“收到停止请求”就会立刻终止线程（重要）

`request_stop()` 只是发出停止请求，并不会强制中断工作线程。

工作线程通常会在某些**安全检查点**主动检查：

```cpp
token.stop_requested()
```

例如：

```cpp
while (!token.stop_requested())
{
    finish_one_small_task();
}
```

如果停止请求恰好在 `finish_one_small_task()` 执行过程中到来，这个函数不会被强行执行到一半就终止。

线程通常会先完成当前这一小块工作，然后回到循环条件：

```cpp
token.stop_requested()
```

发现停止请求后，再退出循环并结束线程。

可以理解成：

```text
收到停止请求
↓
完成当前不能被随意打断的小任务
↓
到达安全检查点
↓
发现 stop_requested() == true
↓
退出循环
↓
完成必要的收尾
↓
线程结束
```

而 `join()` 的作用只是：

> 等待这个线程真正结束。

所以典型关系是：

```text
request_stop()
→ 请求线程停止

worker 在安全位置响应请求并退出

join()
→ 等待 worker 退出完成
```

所谓“合适、安全的位置”，就是线程状态已经完整、资源处于一致状态，此时退出不会留下半完成操作的位置。

安全点 = 可以开始退出的位置；函数 return = 线程真正退出。

所以可以这么理解例子中的worker函数 返回 ≈ 这个线程执行结束。

---

## 小结

`std::jthread` 可以理解成：

> **带 RAII 生命周期管理和标准停止协议的 `std::thread`。**

最重要的几个点：

```text
std::thread
→ 必须自己保证 join() / detach()

std::jthread
→ 析构时自动 request_stop() + join()
```

停止机制：

```text
request_stop()
→ 发出停止请求

stop_token.stop_requested()
→ 查看是否收到停止请求
```

它是：

```text
协作式停止
```

而不是：

```text
强制杀死线程
```

另外：

```text
stop_source
→ 发出停止请求

stop_token
→ 观察停止请求

stop_callback
→ 停止请求出现时执行回调
```

对于 C++20 中“启动后持续运行、退出时需要安全停止”的后台线程，一般可以优先考虑：

```cpp
std::jthread + std::stop_token
```

而不是：

```cpp
std::thread + 手写生命周期管理 + 自定义 atomic<bool> 停止协议
```
