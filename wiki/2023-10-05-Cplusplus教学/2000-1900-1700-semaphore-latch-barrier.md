---
vinciId: 04d13471-0030-469c-941d-87f33c75d0e4
title: semaphore、latch 与 barrier
description: C++20 在：
authors:
  - dongjiahui
publishedAt: 2026-10-06T09:22:17.332Z
updatedAt: 2026-10-06T09:22:17.332Z
---

C++20 在：

```cpp
<semaphore>
<latch>
<barrier>
```

中加入了几种更高层的同步工具：

```cpp
std::counting_semaphore
std::binary_semaphore
std::latch
std::barrier
```

它们和 `mutex` 解决的问题不一样。

可以先用一句话记住：

```text
mutex
→ 一次只允许一个线程进入临界区

semaphore
→ 一共有多少张“通行证”

latch
→ 等大家一次性全部到齐

barrier
→ 每一轮都要等大家到齐后再进入下一轮
```

所以：

```text
semaphore
→ 许可数量控制

latch
→ 一次性同步

barrier
→ 可重复阶段同步
```

这些工具很多都可以用：

```text
mutex + condition_variable + counter
```

手动实现。

但既然标准库已经提供了现成抽象，就没必要每次自己重新设计状态、计数和通知协议。

---

## semaphore：许可数量

### semaphore：控制“许可数量”

### `std::counting_semaphore`

`std::counting_semaphore` 内部维护一个计数。

这个计数可以理解成：

> **当前还有多少张可用通行证。**

例如：

```text
当前计数 = 3
```

就可以理解成：

```text
目前还有 3 个线程可以获得许可
```

线程想进入受限制区域之前调用：

```cpp
semaphore.acquire();
```

如果当前计数：

```text
> 0
```

那么：

```text
计数减 1
线程继续执行
```

如果当前计数：

```text
== 0
```

那么：

```text
暂时没有许可
线程阻塞等待
```

等使用完以后：

```cpp
semaphore.release();
```

表示：

> **归还一个许可。**

于是：

```text
计数加 1
```

并且可能唤醒一个正在等待许可的线程。

所以 semaphore 最核心的流程就是：

```text
acquire()
→ 拿一张通行证

执行受限制的工作

release()
→ 把通行证还回去
```

---

### counting_semaphore 最典型的用途：限制并发数量

例如：

> 最多只允许 2 个线程同时执行某段任务。

```cpp
#include <chrono>
#include <print>
#include <semaphore>
#include <thread>
#include <vector>

using namespace std::chrono_literals;

std::counting_semaphore<2> slots(2);

void worker(int id)
{
    slots.acquire();

    std::println("start {}", id);

    std::this_thread::sleep_for(500ms);

    std::println("end {}", id);

    slots.release();
}

int main()
{
    std::vector<std::thread> threads;

    for (int i = 0; i < 5; ++i)
    {
        threads.emplace_back(worker, i);
    }

    for (auto& thread : threads)
    {
        thread.join();
    }
}
```

可能输出：

```text
start 0
start 1
end 0
start 2
end 1
start 3
end 2
start 4
end 3
end 4
```

虽然我们创建了：

```text
5 个线程
```

但：

```cpp
std::counting_semaphore<2> slots(2);
```

初始只有 2 个许可。

所以最多只有：

```text
2 个线程
```

能同时处于：

```cpp
slots.acquire();
...
slots.release();
```

之间。

可以理解成：

```text
一开始有 2 张通行证

线程 A 拿走一张
剩 1 张

线程 B 拿走一张
剩 0 张

线程 C 来了
没票
只能等

A 或 B 归还一张
↓
C 获得许可继续
```

---

### semaphore 和 mutex 最大的区别

`mutex` 通常表达：

> **同一时刻只能有一个线程进入临界区。**

而：

```cpp
std::counting_semaphore<N>
```

可以表达：

> **同一时刻最多允许 N 个线程进入。**

所以：

```text
mutex
≈ 只有 1 把钥匙

counting_semaphore
≈ 有 N 张通行证
```

但它们还有一个更重要的语义区别：

```text
mutex
→ 强调锁的所有权

semaphore
→ 强调许可数量
```

对于 mutex：

```text
谁 lock
通常就应该由谁 unlock
```

而 semaphore：

```text
谁 acquire
不要求必须由同一个线程 release
```

也就是说：

> semaphore 的许可可以由一个线程获取，由另一个线程归还。

这使它不仅可以限制并发数量，也可以做线程间信号通知。

---

### 模板参数和初始计数不是一回事

例如：

```cpp
std::counting_semaphore<10> semaphore(3);
```

这里：

```text
10
```

不是：

```text
当前有 10 个许可
```

它表示的是：

> **这个 semaphore 所支持的最大计数上界至少为 10。**

而：

```text
3
```

才是：

> **初始计数。**

所以这个对象刚创建时：

```cpp
semaphore.acquire();
semaphore.acquire();
semaphore.acquire();
```

前三次可以直接成功。

第四次：

```cpp
semaphore.acquire();
```

就需要等待别人：

```cpp
semaphore.release();
```

所以记住：

```text
std::counting_semaphore<10> semaphore(3);

10
→ 计数上限相关参数

3
→ 当前初始许可数量
```

不要把它们混在一起。

---

### `try_acquire()`

如果你不想：

```text
没有许可就一直阻塞
```

可以用：

```cpp
if (semaphore.try_acquire())
{
    // 成功获得许可

    semaphore.release();
}
```

它的逻辑是：

```text
有许可
→ 拿走一个
→ 返回 true

没许可
→ 立刻返回 false
→ 不等待
```

这和：

```cpp
mutex.try_lock();
```

很像。

---

### 带超时的 acquire

还可以：

```cpp
semaphore.try_acquire_for(...);
```

或者：

```cpp
semaphore.try_acquire_until(...);
```

可以理解成：

```text
try_acquire_for()
→ 最多等一段时间

try_acquire_until()
→ 最多等到某个时间点
```

例如：

```text
最多等 100ms

拿到许可
→ 继续

100ms 还没拿到
→ 返回失败
→ 执行备用逻辑
```

适合：

```text
资源池暂时没资源

连接数已满

并发额度暂时耗尽

不能无限等待
```

这类场景。

---

### `std::binary_semaphore`

C++20 还提供：

```cpp
std::binary_semaphore
```

它可以理解成：

> **计数最多只有 1 的 semaphore。**

也就是它只有两种状态：

```text
0
→ 没有许可

1
→ 有一个许可
```

例如：

```cpp
std::binary_semaphore signal(0);
```

初始：

```text
0 个许可
```

所以：

```cpp
signal.acquire();
```

会阻塞。

另一个线程执行：

```cpp
signal.release();
```

以后：

```text
许可变成 1
↓
一个等待线程可以继续
```

---

### binary_semaphore 可以理解成一个“门”

例如：

```cpp
std::binary_semaphore signal(0);
```

可以想成：

```text
门一开始是关着的
```

线程 A：

```cpp
signal.acquire();
```

相当于：

```text
门没开
→ 在门口等
```

线程 B：

```cpp
signal.release();
```

相当于：

```text
发放一个许可
→ 让一个等待线程通过
```

所以 binary semaphore 很适合表达：

```text
线程 A 等一个信号

线程 B 发出信号
```

---

### binary_semaphore 不是 mutex

虽然 binary semaphore 的计数最多为 1，看起来很像：

```cpp
std::mutex
```

但它们不是一回事。

`mutex`：

```text
强调所有权

哪个线程获得锁
通常由哪个线程释放
```

而 `binary_semaphore`：

```text
强调“有没有许可”

释放许可的线程
可以不是 acquire 的那个线程
```

可以这样记：

```text
mutex
→ 谁锁门，谁开门

semaphore
→ 系统里有几张通行证
```

所以不要因为：

```text
binary_semaphore 的值只有 0 / 1
```

就把它当成 mutex 的完全替代品。

---

### semaphore 不会自动让共享对象线程安全

例如：

```cpp
std::vector<int> data;
```

多个线程同时：

```cpp
data.push_back(...);
```

可能产生数据竞争。

即使你用了：

```cpp
std::counting_semaphore<4> semaphore(4);
```

它也不会神奇地让：

```cpp
std::vector
```

变成线程安全。

semaphore 只解决：

```text
最多允许多少个线程获得许可
```

并不自动解决：

```text
共享对象的一致性保护
```

所以很常见的组合是：

```text
semaphore
→ 控制最多多少个任务同时运行

mutex
→ 保护任务访问的共享数据结构
```

两者职责完全不同。

---

### acquire 以后一定要记得 release

例如：

```cpp
semaphore.acquire();

// 工作

semaphore.release();
```

如果中间发生：

```text
return

异常

复杂分支
```

导致：

```cpp
release()
```

没有执行，那么：

```text
许可被拿走以后永远没有归还
```

可用许可会越来越少。

例如本来：

```text
2 个许可
```

漏掉一次 release 后：

```text
永远只剩 1 个
```

再漏一次：

```text
永远变成 0
```

后续线程全部可能卡住。

所以 semaphore 也要认真考虑：

```text
异常安全

提前 return

许可归还
```

---

## latch：一次性同步

### latch：一次性倒计时同步

`std::latch` 可以理解成：

> **一个只能使用一次的倒计时门。**

例如：

```cpp
std::latch done(3);
```

表示：

```text
初始计数 = 3
```

每完成一个参与者：

```cpp
done.count_down();
```

计数减 1。

过程类似：

```text
3
↓
2
↓
1
↓
0
```

当计数变成：

```text
0
```

等待 latch 的线程就可以继续。

---

### latch 最适合表达什么

它最适合这种需求：

> **等 N 件事情全部做完，然后继续。**

例如：

```text
启动 3 个初始化任务

任务 1 完成
→ count_down()

任务 2 完成
→ count_down()

任务 3 完成
→ count_down()

计数变成 0
↓
主线程继续
```

---

### latch 示例

```cpp
#include <latch>
#include <print>
#include <thread>
#include <vector>

std::latch done(3);

void worker(int id)
{
    std::println("worker {} done", id);

    done.count_down();
}

int main()
{
    std::vector<std::thread> threads;

    for (int i = 0; i < 3; ++i)
    {
        threads.emplace_back(worker, i);
    }

    done.wait();

    std::println("all workers reached the latch");

    for (auto& thread : threads)
    {
        thread.join();
    }
}
```

可能输出：

```text
worker 1 done
worker 0 done
worker 2 done
all workers reached the latch
```

前三行顺序不确定。

但：

```text
all workers reached the latch
```

一定发生在：

```text
3 次 count_down()
```

全部完成以后。

所以主线程根本不关心：

```text
哪个 worker 先完成
```

它只关心：

```text
计数归零了吗？
```

---

### `count_down()`

```cpp
done.count_down();
```

表示：

> **我完成了自己的这一份工作，把计数减掉。**

例如：

```text
初始 3

第一个线程 count_down()
→ 2

第二个线程 count_down()
→ 1

第三个线程 count_down()
→ 0
```

当计数到：

```text
0
```

latch 被永久打开。

---

### `wait()`

```cpp
done.wait();
```

表示：

> **如果 latch 计数还没有归零，就等待。**

如果此时：

```text
count > 0
```

线程阻塞。

如果已经：

```text
count == 0
```

那么：

```text
直接返回
```

而且 latch 一旦变成 0：

```text
以后一直保持“打开”
```

不会重新关上。

---

### `arrive_and_wait()`

如果当前线程：

```text
既是参与者

又要等待其他参与者
```

可以使用：

```cpp
latch.arrive_and_wait();
```

它可以理解成：

```text
我已经到达
↓
计数减一
↓
然后我也等待其他人到达
```

也就是类似：

```cpp
latch.count_down();
latch.wait();
```

不过 `arrive_and_wait()` 表达意图更直接。

---

### latch 是一次性的

这是 latch 最重要的特点：

```text
只能用一次
```

例如：

```cpp
std::latch done(3);
```

最终：

```text
3 → 2 → 1 → 0
```

到 0 以后：

```text
不会重新回到 3
```

所以 latch 特别适合：

```text
等待一批初始化任务完成

等待几个线程启动就绪

等待一次性并行任务全部结束
```

而不适合：

```text
第一轮同步
第二轮同步
第三轮同步
...
```

这种场景应该用：

```cpp
std::barrier
```

---

### latch 可以理解成“一次性开门”

例如：

```text
初始 latch(3)

还差 3 人
门关着

还差 2 人
门关着

还差 1 人
门关着

最后 1 人到达
计数变 0

门打开

以后永远保持打开
```

所以：

```text
latch
≈ 一次性会合点
```

---

## barrier：可重复阶段同步

### barrier：可重复的阶段同步

`std::barrier` 解决的是：

> **多个线程需要一轮一轮同步。**

例如有三个线程：

```text
线程 A
线程 B
线程 C
```

每个线程都要执行：

```text
第 1 阶段
↓
等待所有人完成

第 2 阶段
↓
等待所有人完成

第 3 阶段
```

这时：

```cpp
std::barrier
```

非常合适。

---

### barrier 可以理解成“每轮都要集合”

假设：

```cpp
std::barrier sync_point(3);
```

表示：

```text
每一轮都要等 3 个参与者到达
```

第一轮：

```text
A 到了
B 到了
C 到了
↓
3 人到齐
↓
全部放行进入下一轮
```

然后 barrier 自动重新准备：

```text
下一轮继续等 3 人
```

所以：

```text
latch
→ 只集合一次

barrier
→ 每一轮都重新集合
```

---

### barrier 示例

```cpp
#include <barrier>
#include <print>
#include <thread>
#include <vector>

std::barrier sync_point(3);

void worker(int id)
{
    std::println("phase 1: {}", id);

    sync_point.arrive_and_wait();

    std::println("phase 2: {}", id);

    sync_point.arrive_and_wait();

    std::println("phase 3: {}", id);
}

int main()
{
    std::vector<std::thread> threads;

    for (int i = 0; i < 3; ++i)
    {
        threads.emplace_back(worker, i);
    }

    for (auto& thread : threads)
    {
        thread.join();
    }
}
```

可能输出：

```text
phase 1: 2
phase 1: 0
phase 1: 1

phase 2: 1
phase 2: 2
phase 2: 0

phase 3: 0
phase 3: 2
phase 3: 1
```

每个阶段内部：

```text
谁先打印
```

不确定。

但是一定满足：

```text
所有 phase 1 都完成
↓
才可能出现 phase 2

所有 phase 2 都完成
↓
才可能出现 phase 3
```

所以不会出现：

```text
线程 A 已经进入 phase 2

但线程 B 连 phase 1 的 barrier 都还没到
```

---

### `arrive_and_wait()`

barrier 最常见的操作：

```cpp
sync_point.arrive_and_wait();
```

表示：

```text
我已经完成这一阶段
↓
记录一次到达
↓
然后等其他参与者
```

当本阶段所有参与者都到达以后：

```text
所有等待线程一起进入下一阶段
```

所以：

```cpp
arrive_and_wait();
```

非常符合：

```text
“这一轮我做完了，现在等大家。”
```

这种语义。

---

### barrier 会自动进入下一轮

这是它和 latch 最大的不同。

latch：

```text
计数归零
↓
永久打开
↓
结束
```

barrier：

```text
本轮所有人到达
↓
本轮完成
↓
自动开始下一轮
↓
重新等待参与者
```

所以：

```text
latch
→ 一次性同步

barrier
→ 循环同步
```

---

### completion function

`std::barrier` 还可以设置：

> **阶段完成函数（completion function）**

它会在：

```text
本轮所有参与者都到达
```

以后执行一次。

概念上：

```text
所有线程完成第 N 阶段
        ↓
completion function
        ↓
进入第 N+1 阶段
```

这适合：

```text
所有线程完成本轮计算
↓
统一更新一次共享状态
↓
所有线程开始下一轮
```

例如并行算法可能需要：

```text
第 1 轮：
每个线程算自己那部分

所有人到 barrier

completion：
交换 / 汇总本轮结果

第 2 轮：
基于新结果继续计算
```

---

### completion function 不要做太重的事情

因为：

```text
所有参与者都在等这个阶段真正结束
```

所以 completion function 如果：

```text
执行很慢

做长时间 IO

sleep

等待其他锁

执行复杂阻塞操作
```

那么所有参与者都会一起被拖住。

所以 completion function 更适合：

```text
很短的状态更新

交换阶段数据

维护轮次

轻量汇总
```

---

### `arrive_and_drop()`

假设 barrier 初始有：

```text
3 个参与者
```

但某个线程执行完当前阶段以后：

> 后面几轮不再参加了。

这时不能直接：

```text
退出线程
```

因为 barrier 还以为：

```text
下一轮仍然要等 3 人
```

但实际上以后只有：

```text
2 人
```

于是其他两个线程永远等不到第三个。

所以需要：

```cpp
barrier.arrive_and_drop();
```

表示：

```text
我完成了本轮到达

并且：

以后每一轮都不用再等我
```

也就是把未来参与者数量永久减掉。

---

## 选择与对比

### latch 和 barrier 最核心的区别

可以直接这样记：

```text
latch
→ 一次性的

barrier
→ 可重复的
```

例如：

#### latch

```text
等 4 个模块初始化完成
↓
程序正式启动
```

初始化只发生一次。

所以：

```cpp
std::latch
```

很合适。

#### barrier

```text
4 个线程一起做第 1 轮
↓
集合

一起做第 2 轮
↓
集合

一起做第 3 轮
↓
集合
```

每一轮都要同步。

所以：

```cpp
std::barrier
```

更合适。

---

### semaphore、latch、barrier 怎么选

可以直接看这张表：

| 需求 | 推荐工具 |
|---|---|
| 最多允许 N 个线程同时获得资源 | `std::counting_semaphore` |
| 一个简单的 0 / 1 许可或线程间信号 | `std::binary_semaphore` |
| 等 N 个参与者一次性全部完成 | `std::latch` |
| 多个线程每轮都要同步 | `std::barrier` |
| 保护共享对象一致性 | `std::mutex` |
| 等复杂共享条件成立 | `std::condition_variable` |

---

### 和 mutex 的区别

`mutex` 关注的是：

```text
谁能进入临界区
```

例如：

```cpp
std::scoped_lock lock(mutex);
```

表达：

```text
这段共享数据同时只允许一个线程操作
```

而 semaphore 关注：

```text
还有多少许可
```

latch 关注：

```text
还有多少参与者没完成
```

barrier 关注：

```text
这一轮所有参与者到齐了吗
```

所以这些工具：

```text
不是 mutex 的不同写法
```

而是：

```text
解决不同同步模式
```

---

### 和 condition_variable 的关系

理论上很多同步器都可以自己手搓。

例如 latch 可以大概用：

```text
counter

mutex

condition_variable
```

实现：

```text
counter 每完成一个任务减 1

counter == 0
↓
notify_all()
```

barrier 也可以自己维护：

```text
当前轮次
参与者数量
已到达数量
condition_variable
```

实现。

但这很容易产生：

```text
计数错误

边界条件错误

通知丢失

轮次状态错误

异常退出后人数不一致
```

所以：

```text
condition_variable
→ 通用零件

semaphore / latch / barrier
→ 已经封装好的常见同步模式
```

如果标准库已经有你想表达的模型：

```text
优先使用标准抽象
```

通常会让代码：

```text
更短

更清楚

更不容易写错

更容易让别人看懂
```

---

### semaphore 的典型场景

`counting_semaphore` 常见于：

```text
限制同时下载任务数量

限制数据库连接数

限制 GPU / 加速器任务并发数

资源池可用对象数量

限制同时进行的网络请求

控制同时执行的重任务数量
```

例如：

```text
线程可以有 100 个

但数据库连接池只有 10 个连接

→ semaphore 初始许可 = 10
```

这就很自然。

---

### latch 的典型场景

latch 常见于：

```text
多个模块并行初始化

主线程等待所有初始化结束

多个并行任务全部完成后统一继续

启动多个 worker，等待全部 ready
```

关键词是：

```text
一次性
```

---

### barrier 的典型场景

barrier 常见于：

```text
并行数值计算

迭代算法

分块处理

多线程仿真

每一轮都依赖上一轮完整结果
```

关键词是：

```text
一轮一轮同步
```

---

## 常见错误

### 错误：把 semaphore 当 mutex

错误理解：

```text
用了 semaphore
→ 共享 vector 自动线程安全
```

不对。

semaphore 只控制：

```text
许可数量
```

共享对象的一致性仍然可能需要：

```cpp
std::mutex
```

---

### 错误：acquire 以后忘记 release

例如：

```cpp
semaphore.acquire();

if (error)
{
    return; // 忘了 release
}
```

这样会永久损失一个许可。

---

### 错误：认为 binary semaphore 有 mutex 的所有权规则

binary semaphore 虽然只有：

```text
0 / 1
```

但它仍然：

```text
不是 mutex
```

许可可以由不同线程 acquire / release。

---

### 错误：把 latch 当成可以重置的工具

`std::latch`：

```text
只能归零一次
```

归零以后不会：

```text
重新恢复初始计数
```

需要循环同步时用：

```cpp
std::barrier
```

---

### 错误：barrier 的参与人数写错

例如：

```cpp
std::barrier barrier(4);
```

但实际上只有：

```text
3 个线程
```

会调用：

```cpp
arrive_and_wait();
```

那么：

```text
永远差第 4 个参与者
```

所有线程都可能永久卡住。

---

### 错误：线程退出了，却没 `arrive_and_drop()`

如果某个 barrier 参与者：

```text
以后不再参加后续轮次
```

却直接退出，那么 barrier 仍然会：

```text
在后续每一轮等它
```

应该：

```cpp
arrive_and_drop();
```

---

### 错误：completion function 太慢

completion function 执行期间：

```text
所有参与者都在等阶段完成
```

所以里面不适合：

```text
长时间 IO

sleep

复杂阻塞

重计算

危险的锁嵌套
```

---

## 最后总结

这三个 C++20 同步工具可以这样记：

```text
semaphore
→ 有几张通行证

latch
→ 一次性等大家到齐

barrier
→ 每一轮都等大家到齐
```

更具体一点：

```text
std::counting_semaphore
→ 允许同时存在多个许可

std::binary_semaphore
→ 只有 0 / 1 个许可

std::latch
→ 一次性倒计时，归零后永久打开

std::barrier
→ 每轮所有参与者到达后，自动开始下一轮
```

它们和 mutex 的关系：

```text
mutex
→ 保护共享状态

semaphore / latch / barrier
→ 表达更高层的同步模式
```

如果能用标准同步原语直接表达你的需求：

> **优先使用标准工具，而不是手写 `mutex + condition_variable + counter`。**

最后可以用一句话彻底区分：

```text
mutex
→ “谁能进？”

semaphore
→ “还能进几个？”

latch
→ “这一次人到齐了吗？”

barrier
→ “这一轮人到齐了吗？”
```
