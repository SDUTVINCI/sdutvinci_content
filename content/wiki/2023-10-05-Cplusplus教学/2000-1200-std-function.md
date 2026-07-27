---
title: "std::function"
---

## 本节解决什么问题

普通函数、函数指针、Lambda、函数对象都可以"被调用"，它们统称为可调用对象。但它们的具体类型并不一样：

- 普通函数有函数类型。
- 函数指针是指针类型。
- 每个 Lambda 都有编译器生成的独立类型。
- 函数对象是自定义类类型。

如果你只是立刻调用一次，类型不同通常不是问题；但如果你要把回调保存到成员变量里，或者把多个不同回调放进同一个容器里，就需要一个统一类型。

`std::function` 就是标准库提供的通用可调用对象包装器。

## 这个特性是什么

`std::function<返回值(参数列表)>` 可以保存任何签名匹配的可调用对象。

例如 `std::function<int(int, int)>` 表示：保存一个可以用两个 `int` 调用，并返回 `int` 的可调用对象。

## C++ 标准版本

`std::function` 从 C++11 开始提供，需要头文件 `<functional>`。

## 常见场景

| 场景 | 是否适合 `std::function` | 原因 |
|:---|:---|:---|
| 函数里立刻调用一次回调 | 不一定 | 模板参数通常更轻量 |
| 类成员变量保存一个回调 | 适合 | 成员变量需要稳定类型 |
| `vector` 保存多个不同 lambda | 适合 | 容器元素必须是同一种类型 |
| 高频性能热点里的小函数 | 谨慎 | `std::function` 有类型擦除开销 |
| 只保存无捕获函数 | 函数指针也可以 | 但函数指针不能保存有捕获 lambda |

## 示例代码

### 示例 1：统一保存不同类型的可调用对象

```cpp
#include <functional>
#include <iostream>

int add(int a, int b)
{
    return a + b;
}

struct Multiply
{
    int operator()(int a, int b) const
    {
        return a * b;
    }
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // std::function 可以保存普通函数、lambda 或函数对象。
    std::function<int(int, int)> op;

    op = add;
    std::cout << "add: " << op(3, 4) << "\n";

    int (*function_pointer)(int, int) = add;
    op = function_pointer;
    std::cout << "function pointer: " << op(5, 6) << "\n";

    op = [](int a, int b) {
        return a - b;
    };
    std::cout << "lambda: " << op(10, 3) << "\n";

    op = Multiply{};
    std::cout << "function object: " << op(7, 8) << "\n";

    return 0;
}
```

**运行结果**：

```text
add: 7
function pointer: 11
lambda: 7
function object: 56
```

### 示例 2：把 std::function 作为参数传递回调

这个例子中，`calculate` 不关心具体传进来的是普通函数还是 lambda，只要求签名是 `int(int, int)`。

```cpp
#include <functional>
#include <iostream>

int add(int a, int b)
{
    return a + b;
}

// std::function 可以保存普通函数、lambda 或函数对象。
int calculate(int a, int b, const std::function<int(int, int)>& op)
{
    return op(a, b);
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    int x = 10;
    int y = 5;

    int r1 = calculate(x, y, add);
    std::cout << "add result = " << r1 << "\n";

    int r2 = calculate(x, y, [](int a, int b) {
        return a * b;
    });
    std::cout << "multiply result = " << r2 << "\n";

    int offset = 100;
    int r3 = calculate(x, y, [offset](int a, int b) {
        return a + b + offset;
    });
    std::cout << "with offset result = " << r3 << "\n";

    return 0;
}
```

**运行结果**：

```text
add result = 15
multiply result = 50
with offset result = 115
```

### 示例 3：把回调保存到类成员变量

函数指针不能保存有捕获的 lambda；`std::function` 可以，所以它非常适合保存事件回调。

```cpp
#include <functional>
#include <iostream>
#include <string>
#include <utility>

class Button
{
    // std::function 可以保存普通函数、lambda 或函数对象。
    std::function<void()> on_click_;

public:
    void set_on_click(std::function<void()> callback)
    {
        on_click_ = std::move(callback);
    }

    void click() const
    {
        if (on_click_)
        {
            on_click_();
        }
        else
        {
            std::cout << "no callback\n";
        }
    }
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    Button button;

    button.click();

    std::string name = "Save";
    int count = 0;

    button.set_on_click([name, &count]() {
        ++count;
        std::cout << name << " clicked, count = " << count << "\n";
    });

    button.click();
    button.click();

    return 0;
}
```

**运行结果**：

```text
no callback
Save clicked, count = 1
Save clicked, count = 2
```

这里按值捕获 `name`，按引用捕获 `count`。因为 `button` 和 `count` 都在 `main` 中，`count` 活得比回调调用更久，所以这个例子是安全的。真实异步场景中，保存回调时要更谨慎地处理生命周期。

### 示例 4：把多个不同回调放进同一个容器

每个 Lambda 的类型都不同，不能直接放进同一个 `vector`。用 `std::function<void()>` 之后，它们就有了统一类型。

```cpp
#include <functional>
#include <iostream>
#include <string>
#include <vector>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // std::function 可以保存普通函数、lambda 或函数对象。
    // vector 是动态数组，元素数量可以在运行时变化。
    std::vector<std::function<void()>> tasks;

    int total = 0;
    std::string label = "task";

    tasks.push_back([label]() {
        std::cout << label << " A\n";
    });

    tasks.push_back([&total]() {
        total += 10;
        std::cout << "add 10, total = " << total << "\n";
    });

    tasks.push_back([&total]() {
        total *= 2;
        std::cout << "double, total = " << total << "\n";
    });

    for (const auto& task : tasks)
    {
        task();
    }

    std::cout << "final total = " << total << "\n";

    return 0;
}
```

**运行结果**：

```text
task A
add 10, total = 10
double, total = 20
final total = 20
```

### 示例 5：只立刻调用时，模板参数也可以接收回调

`std::function` 的优势是"保存和统一类型"。如果只是立刻调用，不需要保存，函数模板通常更轻量。

```cpp
#include <iostream>

template<typename Callback>
void repeat(int times, Callback callback)
{
    for (int i = 0; i < times; ++i)
    {
        callback(i);
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    repeat(3, [](int i) {
        std::cout << "i = " << i << "\n";
    });

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
i = 0
i = 1
i = 2
```

## 关键语法解释

| 写法 | 含义 | 注意事项 |
|:---|:---|:---|
| `std::function<void()>` | 保存无参数、无返回值的回调 | 常用于按钮、定时器、任务队列 |
| `std::function<int(int, int)>` | 保存两个 `int` 参数、返回 `int` 的可调用对象 | 签名必须匹配 |
| `if (callback)` | 判断 `std::function` 是否为空 | 空回调不能直接调用 |
| `std::move(callback)` | 把传入的回调移动到成员变量 | 避免不必要拷贝 |
| `vector<std::function<void()>>` | 保存多个不同类型的回调 | 容器元素类型统一 |

## std::function 和 Lambda 的关系

Lambda 是一种可调用对象，`std::function` 是用来保存可调用对象的包装器。它们不是互相替代的关系。

| 需求 | 推荐 |
|:---|:---|
| 就地写一段短逻辑 | Lambda |
| 把回调保存成变量或成员 | `std::function` |
| 把多个不同 lambda 放入容器 | `std::function` |
| 调用一次且性能敏感 | 函数模板 |

## 常见错误

1. 空的 `std::function` 直接调用。调用前先判断 `if (callback)`。
2. 签名不匹配。比如回调需要 `int(int, int)`，就不能传入只接收一个参数的可调用对象。
3. 保存回调时引用捕获了已经销毁的局部变量。
4. 只需要临时调用一次，也把参数写成 `std::function`，在性能敏感代码中会产生不必要开销。
5. 以为成员函数可以直接赋给 `std::function<void(int)>`。非静态成员函数还需要对象，下一节会用 `std::bind` 和 Lambda 解决这个问题。

## 使用建议

1. 需要保存回调时，用 `std::function`。
2. 需要统一不同类型的回调时，用 `std::function`。
3. 只是立刻调用一次回调时，可以优先考虑模板参数。
4. 保存回调时特别注意捕获对象的生命周期。
5. 不要把 `std::function` 当成所有回调场景的唯一答案，它是清晰性和灵活性的工具，也有一定运行期开销。

## 小结

- 可调用对象包括普通函数、函数指针、Lambda、函数对象等。
- `std::function<返回值(参数...)>` 用统一类型保存签名匹配的可调用对象。
- `std::function` 适合保存回调、作为成员变量、放入容器。
- 空的 `std::function` 不能调用。
- 只临时调用一次的回调，模板参数通常更轻量。
