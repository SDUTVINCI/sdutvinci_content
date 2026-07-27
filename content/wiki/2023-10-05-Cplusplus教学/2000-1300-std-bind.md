---
title: "std::bind"
---

## 本节解决什么问题

有时你已经有一个函数，但它的参数和目标接口不匹配：

- 想提前固定某些参数。
- 想调整参数顺序。
- 想把成员函数绑定到某个对象上，变成普通回调。

`std::bind` 可以把已有可调用对象适配成新的可调用对象。

现代 C++ 中，很多 `bind` 场景可以用 Lambda 写得更清楚。不过 `std::bind` 在旧项目、ROS2 示例、Boost.Asio 回调、成员函数适配中仍然经常出现，值得掌握。

## 这个特性是什么

`std::bind` 接收一个可调用对象和一组绑定参数，返回一个新的可调用对象。调用新对象时，占位符 `_1`、`_2`、`_3` 表示"调用时传进来的第 1、2、3 个参数"。

`std::bind` 需要头文件 `<functional>`，占位符在命名空间 `std::placeholders` 中。

| 写法 | 含义 |
|:---|:---|
| `std::bind(f, 10, _1)` | 固定 `f` 的第一个参数为 `10` |
| `std::bind(f, _2, _1)` | 调换调用时两个参数的顺序 |
| `std::bind(&C::m, &obj, _1)` | 把成员函数绑定到对象 `obj` |
| `std::bind(f, std::ref(x), _1)` | 绑定引用，而不是拷贝 |

## 示例代码

### 示例 1：固定普通函数的部分参数

```cpp
#include <functional>
#include <iostream>
#include <string>

void print_student(const std::string& name, int age, int score)
{
    std::cout << name << " age=" << age << " score=" << score << "\n";
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    using std::placeholders::_1;
    using std::placeholders::_2;

    print_student("Alice", 20, 95);

    // bind 会把函数和部分参数提前绑定成一个可调用对象。
    auto print_bob = std::bind(print_student, "Bob", _1, _2);
    print_bob(21, 88);

    auto print_charlie_score = std::bind(print_student, "Charlie", 22, _1);
    print_charlie_score(91);

    return 0;
}
```

**运行结果**：

```text
Alice age=20 score=95
Bob age=21 score=88
Charlie age=22 score=91
```

### 示例 2：调整参数顺序

占位符的位置决定了原函数接收到的参数顺序。

```cpp
#include <functional>
#include <iostream>

void divide(int a, int b)
{
    std::cout << a << " / " << b << " = " << (a / b) << "\n";
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    using std::placeholders::_1;
    using std::placeholders::_2;

    divide(10, 2);

    // bind 会把函数和部分参数提前绑定成一个可调用对象。
    auto swapped = std::bind(divide, _2, _1);
    swapped(2, 10);

    auto half = std::bind(divide, _1, 2);
    half(10);
    half(20);

    return 0;
}
```

**运行结果**：

```text
10 / 2 = 5
10 / 2 = 5
10 / 2 = 5
20 / 2 = 10
```

### 示例 3：绑定成员函数

非静态成员函数需要对象才能调用。`std::bind` 的第二个参数通常就是对象指针、对象引用或智能指针。

```cpp
#include <functional>
#include <iostream>
#include <string>

class Printer
{
    std::string prefix_;

public:
    explicit Printer(const std::string& prefix) : prefix_(prefix) {}

    void print(int id, const std::string& text) const
    {
        std::cout << prefix_ << " #" << id << ": " << text << "\n";
    }
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    using std::placeholders::_1;
    using std::placeholders::_2;

    Printer printer("LOG");

    // bind 会把函数和部分参数提前绑定成一个可调用对象。
    auto print_any = std::bind(&Printer::print, &printer, _1, _2);
    print_any(7, "ready");

    auto print_ok = std::bind(&Printer::print, &printer, _1, "ok");
    print_ok(8);

    return 0;
}
```

**运行结果**：

```text
LOG #7: ready
LOG #8: ok
```

这里 `&printer` 必须在 `print_any` 和 `print_ok` 使用期间一直有效。如果绑定后的回调会被保存到更久的地方，通常要考虑智能指针或更明确的生命周期管理。

### 示例 4：bind 和 Lambda 的对比

同一个参数适配任务，Lambda 往往更直观，因为参数名和调用关系都写得很清楚。

```cpp
#include <functional>
#include <iostream>

int weighted_sum(int base, int x, int y)
{
    return base + x * 10 + y;
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    using std::placeholders::_1;
    using std::placeholders::_2;

    // bind 会把函数和部分参数提前绑定成一个可调用对象。
    auto by_bind = std::bind(weighted_sum, 100, _1, _2);
    auto by_lambda = [](int x, int y) {
        return weighted_sum(100, x, y);
    };

    std::cout << "bind result = " << by_bind(3, 4) << "\n";
    std::cout << "lambda result = " << by_lambda(3, 4) << "\n";

    auto swapped_bind = std::bind(weighted_sum, 100, _2, _1);
    auto swapped_lambda = [](int x, int y) {
        return weighted_sum(100, y, x);
    };

    std::cout << "swapped bind result = " << swapped_bind(3, 4) << "\n";
    std::cout << "swapped lambda result = " << swapped_lambda(3, 4) << "\n";

    return 0;
}
```

**运行结果**：

```text
bind result = 134
lambda result = 134
swapped bind result = 143
swapped lambda result = 143
```

### 示例 5：bind 默认拷贝参数，需要引用时用 std::ref

`std::bind` 保存普通参数时默认拷贝。如果你要绑定引用，必须显式使用 `std::ref` 或 `std::cref`。

```cpp
#include <functional>
#include <iostream>

void add_to(int& total, int value)
{
    total += value;
    std::cout << "inside total = " << total << "\n";
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    int total = 0;

    // bind 会把函数和部分参数提前绑定成一个可调用对象。
    auto add_copy = std::bind(add_to, total, 5);
    add_copy();
    std::cout << "outside after copy bind = " << total << "\n";

    auto add_ref = std::bind(add_to, std::ref(total), 5);
    add_ref();
    std::cout << "outside after ref bind = " << total << "\n";

    return 0;
}
```

**运行结果**：

```text
inside total = 5
outside after copy bind = 0
inside total = 5
outside after ref bind = 5
```

## 关键语法解释

| 写法 | 说明 | 注意事项 |
|:---|:---|:---|
| `_1` | 调用新函数时传入的第一个参数 | 来自 `std::placeholders` |
| `_2` | 调用新函数时传入的第二个参数 | 可以用来调整顺序 |
| 固定值 | 直接写在 `bind` 参数里 | 默认会被拷贝保存 |
| `&Class::method` | 成员函数指针 | 后面还要绑定对象 |
| `std::ref(x)` | 按引用绑定 `x` | 不写 `std::ref` 通常会拷贝 |

## bind 和 Lambda 怎么选

| 场景 | 推荐 |
|:---|:---|
| 简单固定参数 | Lambda 或 `bind` 都可以 |
| 参数关系需要读得很清楚 | Lambda |
| 绑定成员函数给旧接口 | `bind` 很常见，也可以用 Lambda |
| 需要复杂逻辑、条件判断 | Lambda |
| 阅读旧代码或 ROS2/Boost.Asio 示例 | 必须看懂 `bind` |

一句话：新代码优先考虑 Lambda；遇到已有接口适配、旧项目代码、成员函数回调时，要能熟练读懂 `std::bind`。

## 常见错误

1. 忘记占位符来自 `std::placeholders`。
2. 把 `_1`、`_2` 的顺序看反，导致实参顺序错误。
3. 绑定成员函数时忘记绑定对象。
4. 绑定对象指针后，对象先被销毁，回调还在使用。
5. 以为 `bind` 默认按引用保存参数。默认是拷贝，需要引用时用 `std::ref`。

## 使用建议

1. 新代码中，参数适配优先尝试 Lambda。
2. 读 ROS2、Boost.Asio、旧项目代码时，要能看懂 `std::bind(&Class::method, this, _1, _2)`。
3. 绑定成员函数时最先考虑对象生命周期。
4. 需要引用语义时明确写 `std::ref` 或 `std::cref`。
5. 不要为了使用 `bind` 写出难读的占位符组合，清晰比炫技重要。

## 小结

- `std::bind` 用来固定参数、调整参数顺序、绑定成员函数。
- `_1`、`_2` 表示调用新可调用对象时传入的参数位置。
- `bind` 默认拷贝绑定参数，需要引用时用 `std::ref`。
- 现代 C++ 中很多 `bind` 用法可以用 Lambda 替代。
- 理解 `bind` 对阅读 ROS2、Boost.Asio 和旧 C++ 项目很有帮助。
