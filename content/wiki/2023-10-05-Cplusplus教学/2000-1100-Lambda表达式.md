---
title: "Lambda 表达式"
---

## 本节解决什么问题

很多地方需要传入一小段逻辑：排序规则、过滤条件、按钮回调、定时器回调。旧写法通常有三种：

1. 写一个普通函数。
2. 写一个函数指针。
3. 写一个函数对象，也就是带 `operator()` 的类或结构体。

这些写法都能工作，但小逻辑会被迫挪到远处，或者要额外写一个类型。Lambda 表达式让你可以在使用现场直接写一个小函数，而且可以捕获外部变量。

## 这个特性是什么

Lambda 是匿名可调用对象。它看起来像一个函数，但本质上是编译器生成的一个类对象。

基本结构是：

| 部分 | 例子 | 含义 |
|:---|:---|:---|
| 捕获列表 | `[x, &y]` | 从外部作用域拿哪些变量 |
| 参数列表 | `(int a, int b)` | 调用 lambda 时传入什么 |
| 返回值 | `-> int` | 返回类型，通常可以省略 |
| 函数体 | `{ return a + b; }` | 真正执行的代码 |

完整形式可以写成 `[x](int n) -> int { return x + n; }`，常见情况下返回值类型可以省略。

## C++ 标准版本

- C++11：基础 Lambda。
- C++14：泛型 Lambda，也就是参数可以写 `auto`。
- C++17：`constexpr` Lambda、`[*this]` 捕获。

Lambda 是语言特性，不需要额外头文件。只有配合 STL 算法、`std::function` 等库工具时，才需要包含对应头文件。

## 捕获列表速查

| 写法 | 含义 | 适用场景 |
|:---|:---|:---|
| `[]` | 不捕获外部变量 | 只用参数或局部临时变量 |
| `[x]` | 按值捕获 `x` | 保存一份副本，适合保存回调 |
| `[&x]` | 按引用捕获 `x` | 需要修改外部变量，且能保证生命周期 |
| `[x, &y]` | 混合捕获 | 明确表达哪些拷贝、哪些引用 |
| `[=]` | 默认按值捕获用到的变量 | 小例子方便，工程中不建议滥用 |
| `[&]` | 默认按引用捕获用到的变量 | 异步或保存回调中尤其危险 |
| `[this]` | 捕获当前对象指针 | 对象必须比 lambda 活得久 |
| `[*this]` | 捕获当前对象副本 | C++17 起可用，适合避免悬空 `this` |

## 示例代码

### 示例 1：旧回调写法和 lambda 的区别

普通函数和函数对象都可以作为算法条件，但 Lambda 更适合写简短的局部逻辑。

```cpp
#include <algorithm>
#include <iostream>
#include <vector>

bool is_even(int n)
{
    return n % 2 == 0;
}

struct GreaterThan
{
    int limit;

    bool operator()(int n) const
    {
        return n > limit;
    }
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // vector 是动态数组，元素数量可以在运行时变化。
    std::vector<int> numbers = {1, 2, 3, 4, 5, 6};

    int even_count = std::count_if(numbers.begin(), numbers.end(), is_even);
    std::cout << "even count = " << even_count << "\n";

    int greater_count1 = std::count_if(numbers.begin(), numbers.end(), GreaterThan{3});
    std::cout << "> 3 count (functor) = " << greater_count1 << "\n";

    int limit = 3;
    int greater_count2 = std::count_if(numbers.begin(), numbers.end(),
                                       [limit](int n) {
                                           return n > limit;
                                       });
    std::cout << "> 3 count (lambda) = " << greater_count2 << "\n";

    return 0;
}
```

**运行结果**：

```text
even count = 3
> 3 count (functor) = 3
> 3 count (lambda) = 3
```

### 示例 2：最基本的 lambda 语法、参数和返回值

```cpp
#include <iostream>
#include <string>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    auto add = [](int a, int b) {
        return a + b;
    };

    auto describe_score = [](int score) -> std::string {
        if (score >= 60)
        {
            return "pass";
        }
        return "fail";
    };

    std::cout << "add(3, 5) = " << add(3, 5) << "\n";
    std::cout << "score 80 is " << describe_score(80) << "\n";
    std::cout << "score 40 is " << describe_score(40) << "\n";

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
add(3, 5) = 8
score 80 is pass
score 40 is fail
```

### 示例 3：按值捕获和按引用捕获

按值捕获会保存定义 lambda 时的副本；按引用捕获会访问外部变量本身。

```cpp
#include <iostream>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    int score = 10;

    auto add_by_value = [score](int bonus) {
        return score + bonus;
    };

    auto add_by_ref = [&score](int bonus) {
        score += bonus;
        return score;
    };

    score = 20;

    std::cout << "value capture result = " << add_by_value(5) << "\n";
    std::cout << "ref capture result = " << add_by_ref(5) << "\n";
    std::cout << "score after ref capture = " << score << "\n";

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
value capture result = 15
ref capture result = 25
score after ref capture = 25
```

### 示例 4：mutable 允许修改按值捕获的副本

按值捕获的变量默认在 lambda 内是只读的。加上 `mutable` 后，可以修改 lambda 自己保存的副本，但不会修改外部变量。

```cpp
#include <iostream>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    int start = 0;

    auto counter = [start]() mutable {
        ++start;
        return start;
    };

    std::cout << "counter() = " << counter() << "\n";
    std::cout << "counter() = " << counter() << "\n";
    std::cout << "outside start = " << start << "\n";

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
counter() = 1
counter() = 2
outside start = 0
```

### 示例 5：lambda 配合 STL 算法

Lambda 和 STL 算法配合时最常见：排序、查找、计数、转换都可以把局部逻辑直接写在调用处。

```cpp
#include <algorithm>
#include <iostream>
#include <string>
#include <vector>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    // vector 是动态数组，元素数量可以在运行时变化。
    std::vector<std::string> names = {"Bob", "Alice", "Charlie", "David"};

    std::sort(names.begin(), names.end(),
              [](const std::string& a, const std::string& b) {
                  if (a.size() == b.size())
                  {
                      return a < b;
                  }
                  return a.size() < b.size();
              });

    std::cout << "sort by length: ";
    for (const auto& name : names)
    {
        std::cout << name << " ";
    }
    std::cout << "\n";

    int min_length = 6;
    auto it = std::find_if(names.begin(), names.end(),
                           [min_length](const std::string& name) {
                               return name.size() >= static_cast<std::size_t>(min_length);
                           });

    if (it != names.end())
    {
        std::cout << "first long name = " << *it << "\n";
    }

    return 0;
}
```

**运行结果**：

```text
sort by length: Bob Alice David Charlie
first long name = Charlie
```

### 示例 6：保存回调时要注意捕获生命周期

如果 lambda 只是立刻调用，引用捕获通常看起来没问题；如果保存到容器、线程、定时器或异步回调里，lambda 可能晚于局部变量执行。保存回调时优先按值捕获需要的数据。

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
    std::vector<std::function<void()>> callbacks;

    {
        std::string name = "Alice";
        int score = 95;

        auto print_now = [&name, &score]() {
            std::cout << "now: " << name << " " << score << "\n";
        };
        print_now();

        callbacks.push_back([name, score]() {
            std::cout << "saved: " << name << " " << score << "\n";
        });
    }

    for (const auto& callback : callbacks)
    {
        callback();
    }

    return 0;
}
```

**运行结果**：

```text
now: Alice 95
saved: Alice 95
```

这里 `print_now` 立刻调用，所以引用捕获没问题。保存到 `callbacks` 的 lambda 用按值捕获，因为离开内部作用域后，`name` 和 `score` 已经销毁。

## 关键语法解释

| 示例 | 重点 | 说明 |
|:---|:---|:---|
| 示例 1 | 旧回调写法对比 | 普通函数、函数对象都能做回调，但 lambda 更适合局部短逻辑 |
| 示例 2 | 参数和返回值 | 返回类型通常可推导，分支返回不同类型时要显式写清楚 |
| 示例 3 | 捕获列表 | `[x]` 拷贝，`[&x]` 引用 |
| 示例 4 | `mutable` | 修改的是 lambda 内部副本，不影响外部变量 |
| 示例 5 | STL 算法 | `sort`、`find_if`、`count_if` 常和 lambda 配合 |
| 示例 6 | 生命周期 | 保存回调、异步回调、线程回调中不要随便引用捕获局部变量 |

## 常见错误

1. 保存回调时使用默认引用捕获 `[&]`，导致局部变量销毁后仍被访问。
2. 以为按值捕获会跟着外部变量变化。按值捕获保存的是定义 lambda 时的副本。
3. 想修改按值捕获的副本，却忘记加 `mutable`。
4. 把有捕获的 lambda 当成函数指针使用。有捕获的 lambda 需要用模板参数、`auto` 变量或 `std::function` 保存。
5. 异步场景捕获 `this` 后，对象先被销毁。需要保证对象生命周期，或使用智能指针、`[*this]` 等更明确的方式。

## 使用建议

1. 小而局部的逻辑优先用 lambda。
2. 捕获列表尽量显式写 `[x, &y]`，少用默认 `[=]` 和 `[&]`。
3. 保存回调、线程、定时器、异步操作中优先按值捕获需要的数据。
4. 只调用一次、不需要保存的回调，可以直接把 lambda 传给算法或函数模板。
5. 需要统一保存不同 lambda 时，再使用下一节的 `std::function`。

## 小结

- Lambda 是匿名可调用对象，写法是 `[捕获](参数) { 函数体 }`。
- 捕获列表决定 lambda 如何使用外部变量。
- `[x]` 是按值捕获，`[&x]` 是按引用捕获。
- `mutable` 允许修改按值捕获的内部副本。
- Lambda 最常用于 STL 算法、回调和异步任务。
- 生命周期是 Lambda 最容易出错的地方，尤其是保存回调和异步回调。
