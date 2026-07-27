---
title: "C++类型转换"
---

## 本节解决什么问题

C++ 中经常会遇到“把一种类型当成另一种类型使用”的场景，例如：

- 把 `double` 转成 `int`。
- 把 `enum class` 转成整数。
- 把基类指针安全地转成派生类指针。
- 临时去掉 `const` 限定，适配旧接口。
- 在底层代码里把指针、整数、字节视图互相转换。

C 风格强转写法 `(目标类型)表达式` 能做很多事，但问题也在这里：它太“万能”，读代码的人很难一眼看出这次转换到底是普通数值转换、继承层级转换、去掉 `const`，还是危险的底层重解释。

C++ 提供了四个更明确的类型转换运算符，让转换意图写在代码里。

## 这个特性是什么

C++ 的四种显式类型转换是：

| 转换方式 | 主要用途 | 安全程度 |
|:---|:---|:---|
| `static_cast<T>(expr)` | 常规类型转换，如数值、枚举、父子类上行转换 | 常用，编译期检查 |
| `dynamic_cast<T>(expr)` | 多态类型的安全向下转换 | 运行期检查，失败返回 `nullptr` 或抛异常 |
| `const_cast<T>(expr)` | 添加或移除 `const` / `volatile` 限定 | 要非常克制 |
| `reinterpret_cast<T>(expr)` | 底层二进制重解释，如指针和整数互转 | 最危险，少用 |

原则：能不用强转就不用；必须强转时，优先选择语义最窄、最能说明意图的转换。

## C++ 标准版本

C++98 就已经提供这四种转换写法，实际工程中依然推荐使用它们替代 C 风格强转。

## 需要的头文件

类型转换本身不需要额外头文件。示例代码使用到的库需要对应头文件，例如：

```cpp
#include <cstdint>
#include <iostream>
```

**运行/观察结果：** 这段是头文件示例，放到完整程序顶部即可。

## 基本语法

```cpp
目标类型 value = static_cast<目标类型>(表达式);
目标类型 value = dynamic_cast<目标类型>(表达式);
目标类型 value = const_cast<目标类型>(表达式);
目标类型 value = reinterpret_cast<目标类型>(表达式);
```

**运行/观察结果：** 这段是语法格式说明，重点看四种转换的写法差异。

## 示例代码

### 示例 1：static_cast 处理常规转换

`static_cast` 适合明确、常规、编译期能检查的转换。比如数值转换、`enum class` 转整数、父类指针指向子类对象等。

```cpp
#include <iostream>

enum class Status
{
    ok = 200,
    not_found = 404
};

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    double score = 89.7;
    int integer_score = static_cast<int>(score);

    Status status = Status::not_found;
    int status_code = static_cast<int>(status);

    std::cout << "integer_score = " << integer_score << "\n";
    std::cout << "status_code = " << status_code << "\n";

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
integer_score = 89
status_code = 404
```

### 示例 2：dynamic_cast 安全判断真实对象类型

`dynamic_cast` 常用于多态基类。向下转换时，它会在运行期检查对象真实类型。

```cpp
#include <iostream>

struct Animal
{
    virtual ~Animal() = default;
};

struct Cat : Animal
{
    void meow() const
    {
        std::cout << "cat: meow\n";
    }
};

struct Dog : Animal
{
};

void try_meow(Animal* animal)
{
    Cat* cat = dynamic_cast<Cat*>(animal);

    if (cat != nullptr)
    {
        cat->meow();
    }
    else
    {
        std::cout << "not a cat\n";
    }
}

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    Cat cat;
    Dog dog;

    try_meow(&cat);
    try_meow(&dog);

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
cat: meow
not a cat
```

> 注意：`dynamic_cast` 用在指针上，转换失败会得到 `nullptr`；用在引用上，转换失败会抛出 `std::bad_cast`。它要求基类至少有一个虚函数，通常是虚析构函数。

### 示例 3：const_cast 只改变 const 限定

`const_cast` 只能添加或移除 `const` / `volatile`，不能把 `int` 转成 `double`，也不能把不相关类型互转。

```cpp
#include <iostream>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    int value = 10;
    const int& readonly_ref = value;

    int& writable_ref = const_cast<int&>(readonly_ref);
    writable_ref = 20;

    std::cout << "value = " << value << "\n";

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
value = 20
```

这个例子能修改成功，是因为真正的原始对象 `value` 不是 `const`，只是通过 `const int&` 观察它。如果原始对象本身就是 `const`，再用 `const_cast` 强行修改就是未定义行为。

### 示例 4：reinterpret_cast 做底层重解释

`reinterpret_cast` 表示“把这段二进制数据按另一种类型解释”。它绕过了很多类型系统保护，通常只应该出现在非常底层的代码里。

```cpp
#include <cstdint>
#include <iostream>

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    int value = 42;
    int* p = &value;

    std::uintptr_t raw = reinterpret_cast<std::uintptr_t>(p);
    int* again = reinterpret_cast<int*>(raw);

    std::cout << "*again = " << *again << "\n";
    std::cout << std::boolalpha;
    std::cout << "same pointer = " << (p == again) << "\n";

    // 返回 0 表示程序正常结束。
    return 0;
}
```

**运行结果**：

```text
*again = 42
same pointer = true
```

这个例子只用于说明“指针可以转成能容纳指针的整数，再转回来”。不要随便拿转换后的整数做地址计算，也不要把不相关对象强行解释成另一个对象类型。

## 四种转换怎么选

| 需求 | 推荐写法 | 说明 |
|:---|:---|:---|
| `double` 转 `int` | `static_cast<int>(x)` | 明确可能丢失小数 |
| `enum class` 转整数 | `static_cast<int>(e)` | 枚举类不会隐式转整数 |
| 基类指针转派生类指针，并且不确定真实类型 | `dynamic_cast<Derived*>(p)` | 转换失败可判断 `nullptr` |
| 去掉 `const` 适配旧接口 | `const_cast<T*>(p)` | 前提是旧接口不会修改真正的 const 对象 |
| 指针和整数互转、底层字节解释 | `reinterpret_cast<T>(x)` | 非常底层，优先避免 |

## C风格强转为什么不推荐

C 风格强转写起来短：

```cpp
int n = (int)3.14;
```

**运行/观察结果：** 这段会把 `3.14` 转成 `3`，但读代码时看不出它属于哪类转换。

更推荐写成：

```cpp
int n = static_cast<int>(3.14);
```

**运行/观察结果：** 这段同样会得到 `3`，但转换意图更明确：这是常规数值转换。

C 风格强转可能在背后组合多种转换能力，既可能像 `static_cast`，也可能像 `const_cast`，甚至接近 `reinterpret_cast`。代码越底层、类型越复杂，这种“不说明白”的风险就越大。

## 常见错误

### 用 static_cast 做不安全向下转换

```cpp
Animal* animal = new Dog;
Cat* cat = static_cast<Cat*>(animal);  // 危险：编译可能通过，但真实对象不是 Cat
```

**运行/观察结果：** 这段是错误示例，真实对象类型不匹配时继续使用 `cat` 会产生未定义行为。

如果不确定真实类型，应该使用 `dynamic_cast`。

### 修改真正的 const 对象

```cpp
const int value = 10;
int& ref = const_cast<int&>(value);
ref = 20;  // 未定义行为
```

**运行/观察结果：** 这段是错误示例，`const_cast` 可以骗过编译器，但不能让真正的常量安全地变成可修改对象。

### 滥用 reinterpret_cast

```cpp
double d = 3.14;
int* p = reinterpret_cast<int*>(&d);  // 危险：把 double 对象当 int 对象访问
```

**运行/观察结果：** 这段是错误示例，违反类型别名规则或对象表示假设时，行为不可预测。

## 使用建议

1. 普通转换优先用 `static_cast`。
2. 多态向下转换优先用 `dynamic_cast`，并检查失败情况。
3. `const_cast` 只用于适配旧接口，不要用它修改真正的 `const` 对象。
4. `reinterpret_cast` 只放在非常底层、边界清晰的代码里，并集中封装。
5. 不要为了少打几个字使用 C 风格强转。

## 小结

- `static_cast`：常规转换，最常用。
- `dynamic_cast`：多态类型安全向下转换。
- `const_cast`：只改变 `const` / `volatile` 限定。
- `reinterpret_cast`：底层重解释，风险最高。

一句话记忆：能不用就不用；必须用时，把转换意图写清楚。
