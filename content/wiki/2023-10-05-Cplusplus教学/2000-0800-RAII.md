---
title: "RAII"
---

## 本节解决什么问题

在 C 语言中，我们经常看到这种代码：

```cpp
FILE* f = fopen("data.txt", "r");
// ... 使用文件 ...
fclose(f);  // 容易忘记！
```

如果中间提前 return，或是抛出了异常，`fclose` 就不会执行，造成资源泄漏。类似的问题也出现在内存（`malloc/free`）、锁（`lock/unlock`）、套接字等所有需要"获取-释放"的资源上。

RAII 利用 C++ 对象生命周期确定性，**自动管理资源**，让你不再需要手动释放。

## 这个特性是什么

RAII（Resource Acquisition Is Initialization，资源获取即初始化）是 C++ 中最重要的资源管理惯用法：

- **构造函数**中获取资源。
- **析构函数**中释放资源。
- 当对象离开作用域时，析构函数**一定**会被调用。

这是 C++ 区别于 C 和其他语言的**核心设计理念之一**，智能指针、lock_guard、fstream 都是 RAII 的实现。

## C++ 标准版本

C++98（RAII 从 C++ 诞生之初就存在，智能指针、lock_guard 等现代 RAII 工具在 C++11 成熟）。

## 需要的头文件

RAII 是编程理念，不需要特定头文件。但 RAII 的实现分散在各处：`<memory>`（智能指针）、`<mutex>`（锁）、`<fstream>`（文件流）等。

## 基本语法

```cpp
class RAIIExample
{
    Resource* res;  // 管理的资源

public:
    RAIIExample() : res(获取资源) { }   // 构造：获取资源
    ~RAIIExample() { 释放资源; }        // 析构：释放资源
};
```

## 常用 RAII 实现

| RAII 类型 | 管理资源 | 头文件 |
|:---|:---|:---|
| `std::unique_ptr` | 动态内存 | `<memory>` |
| `std::shared_ptr` | 动态内存（共享） | `<memory>` |
| `std::lock_guard` | 互斥锁 | `<mutex>` |
| `std::unique_lock` | 互斥锁（灵活） | `<mutex>` |
| `std::fstream` | 文件句柄 | `<fstream>` |
| `std::thread` | 线程（需 join/detach） | `<thread>` |

## 示例代码

### 示例 1：没有 RAII 的问题 vs 有 RAII

```cpp
#include <iostream>

// 模拟一个资源
class Resource
{
public:
    void open()  { std::cout << "Resource opened\n"; }
    void close() { std::cout << "Resource closed\n"; }
    void use()   { std::cout << "Resource used\n"; }
};

// ❌ 手动管理：容易忘记 close
void no_raii()
{
    Resource r;
    r.open();
    r.use();
    // 如果这里抛异常或提前 return，close 不会执行！
    r.close();
}

// ✅ RAII：利用析构函数自动释放
class ResourceGuard
{
    Resource& r;
public:
    ResourceGuard(Resource& res) : r(res)
    {
        r.open();  // 构造时获取资源
    }
    ~ResourceGuard()
    {
        r.close(); // 析构时释放资源
    }
};

void with_raii()
{
    Resource r;
    ResourceGuard guard(r);  // 构造时 open
    r.use();
    // guard 离开作用域，析构函数自动 close
}

int main()
{
    std::cout << "=== no_raii ===\n";
    no_raii();

    std::cout << "\n=== with_raii ===\n";
    with_raii();

    return 0;
}
```

**运行结果**：

```
=== no_raii ===
Resource opened
Resource used
Resource closed

=== with_raii ===
Resource opened
Resource used
Resource closed
```

### 示例 2：在示例 1 基础上，用 lock_guard 理解 RAII 锁管理

```cpp
#include <iostream>
#include <mutex>
#include <thread>

std::mutex mtx;  // 共享互斥量
int counter = 0;

// ❌ 手动加锁解锁（容易出问题）
void manual_lock()
{
    mtx.lock();
    ++counter;
    // 如果这里抛异常，unlock 永远不会被执行！
    mtx.unlock();
}

// ✅ RAII 风格：lock_guard 自动管理锁
void raii_lock()
{
    std::lock_guard<std::mutex> lock(mtx);  // 构造时 lock
    ++counter;
    // lock 离开作用域，析构函数自动 unlock
}

int main()
{
    std::thread t1([] {
        for (int i = 0; i < 1000; ++i)
            raii_lock();
    });
    std::thread t2([] {
        for (int i = 0; i < 1000; ++i)
            raii_lock();
    });

    t1.join();
    t2.join();

    std::cout << "counter = " << counter << " (expected 2000)\n";

    return 0;
}
```

**运行结果**：

```
counter = 2000 (expected 2000)
```

### 示例 3：在示例 2 基础上，用 fstream 理解文件 RAII

```cpp
#include <iostream>
#include <fstream>
#include <string>

// RAII：fstream 在析构时会自动关闭文件
void write_and_read()
{
    // 写文件
    {
        std::ofstream out("raii_test.txt");
        out << "Hello RAII!\n";
        out << "This is line 2.\n";
        // out 离开作用域，文件自动关闭
    }

    // 读文件
    {
        std::ifstream in("raii_test.txt");
        std::string line;
        while (std::getline(in, line))
        {
            std::cout << line << "\n";
        }
        // in 离开作用域，文件自动关闭
    }
}

int main()
{
    write_and_read();
    std::cout << "File was automatically closed by RAII\n";
    return 0;
}
```

**运行结果**：

```
Hello RAII!
This is line 2.
File was automatically closed by RAII
```

### 示例 4：在示例 3 基础上，提前 return 时才看出 RAII 的价值

下面这个例子故意模拟"处理到一半发现数据不合法，提前返回"。正常流程里手动释放和 RAII 看起来差不多，但一旦有多个返回路径，区别就很明显。

```cpp
#include <iostream>
#include <string>
#include <utility>
#include <vector>

class Connection
{
    std::string name_;

public:
    explicit Connection(std::string name) : name_(std::move(name))
    {
        std::cout << name_ << " connected\n";
    }

    ~Connection()
    {
        std::cout << name_ << " disconnected\n";
    }

    void send(const std::string& msg)
    {
        std::cout << name_ << " send: " << msg << "\n";
    }
};

bool upload_with_raii(const std::vector<std::string>& lines)
{
    Connection conn("server"); // 构造时连接，函数结束时自动断开

    for (const auto& line : lines)
    {
        if (line.empty())
        {
            std::cout << "empty line, stop upload\n";
            return false; // conn 仍然会析构
        }

        conn.send(line);
    }

    return true; // conn 也会析构
}

int main()
{
    std::vector<std::string> data = {"hello", "world", "", "after error"};

    bool ok = upload_with_raii(data);
    std::cout << "upload ok = " << std::boolalpha << ok << "\n";

    return 0;
}
```

**运行结果**：

```
server connected
server send: hello
server send: world
empty line, stop upload
server disconnected
upload ok = false
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | RAII 基本原理 | 构造函数获取，析构函数释放 | 展示了手动管理的问题和 RAII 的解决方案 | RAII 的"一定执行析构"是 C++ 的核心保证 |
| 示例 2 | lock_guard 是 RAII | `std::lock_guard<std::mutex>` | 构造时加锁，析构时解锁，异常安全 | 比手动 lock/unlock 安全得多 |
| 示例 3 | fstream 是 RAII | `std::ofstream`、`std::ifstream` | 构造时打开文件，析构时关闭文件 | 不需要显式写 close |
| 示例 4 | 多返回路径中的 RAII | 构造/析构、提前 `return` | 真实工程里经常有提前返回，RAII 能保证资源仍被释放 | 资源对象要放在正确的作用域里 |

## 常见错误

**错误 1：在析构函数中抛出异常**

```cpp
~MyRAII()
{
    cleanup();  // 如果 cleanup 抛异常...
}
```

如果析构函数抛异常，且同时有另一个异常正在传播，程序会直接 `std::terminate`。析构函数应标记 `noexcept` 并捕获所有异常。

**错误 2：把 RAII 对象创建在堆上**

```cpp
auto* guard = new std::lock_guard<std::mutex>(mtx);  // ❌ 永远不会自动析构！
```

正确做法：RAII 对象必须在栈上创建，才能利用离开作用域自动析构的特性。

**错误 3：忘记 RAII 对象的作用域**

```cpp
void func()
{
    std::lock_guard<std::mutex> lock(mtx);
    // 锁在这里面生效
}  // 离开作用域，解锁

// 在外面访问共享数据没有保护！
```

正确做法：确保在锁的作用域内访问共享数据。

## 使用建议

1. **"需要配对的获取/释放"就用 RAII**：这是 C++ 的资源管理第一原则。
2. **永远不要手动 new/delete、lock/unlock、open/close**：用智能指针、lock_guard、fstream。
3. **RAII 对象必须在栈上**：利用作用域自动触发析构。
4. **析构函数永远不要抛异常**：标记 `noexcept`。
5. **理解 RAII 就理解了 C++ 的核心设计哲学**：后续智能指针、并发编程都建立在 RAII 之上。
6. **用作用域控制资源持有时间**：想早点释放资源，就把 RAII 对象放进更小的 `{}` 作用域。

## 小结

- RAII = 资源获取即初始化，是 C++ 最核心的资源管理惯用法。
- 构造时获取资源，析构时释放资源，离开作用域保证释放。
- `std::unique_ptr`、`std::lock_guard`、`std::fstream` 都是 RAII。
- 多个 `return`、异常、复杂分支下，RAII 的价值最明显。
- 永远不要在析构函数中抛异常。
- 理解了 RAII，就为理解智能指针和并发编程打好了基础。
