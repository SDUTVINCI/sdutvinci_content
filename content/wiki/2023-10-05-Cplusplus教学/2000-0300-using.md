---
title: "using"
---

## 本节解决什么问题

C 语言中，用 `typedef` 给类型起别名。但 `typedef` 的语法很别扭，特别是给函数指针、模板起别名时，写法不直观、难读。

C++11 引入了 `using` 别名声明，语法更清晰，还支持**模板别名**（`typedef` 做不到）。

## 这个特性是什么

`using` 有两种常见用途：

1. **类型别名**：给已有类型起新名字，类似 `typedef` 但语法更清晰。
2. **模板别名**：给模板类型起别名（`typedef` 做不到这一点）。

## C++ 标准版本

C++11（类型别名语法），C++11 模板别名。

## 需要的头文件

不需要额外头文件。`using` 是语言关键字。

## 基本语法

```cpp
// 类型别名
using 新名字 = 已有类型;

// 模板别名
template<typename T>
using 新名字 = 模板类型<T...>;
```

## 与 typedef 的对比

| 场景 | typedef | using |
|:---|:---|:---|
| 基本类型 | `typedef int Integer;` | `using Integer = int;` |
| 函数指针 | `typedef void (*Func)(int);` | `using Func = void(*)(int);` |
| 数组 | `typedef int Arr10[10];` | `using Arr10 = int[10];` |
| 模板别名 | ❌ 不支持 | `using Vec<T> = std::vector<T>;` |

## using 能做什么，不能做什么

`using` 创建的是**别名**，不是新类型。它能让复杂类型更好读，但不能阻止你把两个底层相同的类型混用。

| 需求 | using 是否适合 | 说明 |
|:---|:---|:---|
| 缩短复杂类型名 | ✅ 适合 | `using Callback = std::function<void(int)>;` |
| 统一项目里的业务名称 | ✅ 适合 | `using UserId = int;` 让代码更有语义 |
| 定义模板的简化写法 | ✅ 很适合 | `template<typename T> using Vec = std::vector<T>;` |
| 防止不同业务类型互相传错 | ❌ 不够 | `using Meter = double;` 和 `using Second = double;` 本质仍都是 double |
| 创建真正的新类型 | 用 `struct` / `class` / `enum class` | 编译器才能帮你检查混用 |

## 示例代码

### 示例 1：using 基础——替换 typedef

```cpp
#include <iostream>
#include <string>
#include <vector>

// 旧写法（typedef）
typedef std::vector<int> IntVector1;

// 新写法（using）
using IntVector2 = std::vector<int>;

int main()
{
    // 两种写法效果完全一样
    IntVector1 v1 = {1, 2, 3};
    IntVector2 v2 = {4, 5, 6};

    std::cout << "v1: ";
    for (int n : v1) std::cout << n << " ";
    std::cout << "\n";

    std::cout << "v2: ";
    for (int n : v2) std::cout << n << " ";
    std::cout << "\n";

    return 0;
}
```

**运行结果**：

```
v1: 1 2 3 
v2: 4 5 6 
```

### 示例 2：在示例 1 基础上，using 给函数指针起别名

```cpp
#include <iostream>

// typedef 的函数指针写法（别扭）
typedef int (*FuncPtr1)(int, int);

// using 的函数指针写法（直观）
using FuncPtr2 = int (*)(int, int);

int add(int a, int b)
{
    return a + b;
}

int multiply(int a, int b)
{
    return a * b;
}

int main()
{
    FuncPtr2 op1 = add;
    FuncPtr2 op2 = multiply;

    std::cout << "add(3, 5) = " << op1(3, 5) << "\n";
    std::cout << "multiply(3, 5) = " << op2(3, 5) << "\n";

    return 0;
}
```

**运行结果**：

```
add(3, 5) = 8
multiply(3, 5) = 15
```

### 示例 3：在示例 2 基础上，模板别名（typedef 做不到）

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <string>

// 模板别名：定义一个泛型的 vector
template<typename T>
using Vec = std::vector<T>;

// 模板别名：键为 string 的 map
template<typename T>
using StringMap = std::map<std::string, T>;

int main()
{
    Vec<int> vi = {1, 2, 3};
    Vec<double> vd = {1.1, 2.2, 3.3};
    StringMap<int> scores = {{"Alice", 85}, {"Bob", 92}};

    std::cout << "Vec<int>: ";
    for (int n : vi) std::cout << n << " ";
    std::cout << "\n";

    std::cout << "Vec<double>: ";
    for (double d : vd) std::cout << d << " ";
    std::cout << "\n";

    std::cout << "StringMap<int>: ";
    for (const auto& p : scores)
        std::cout << p.first << ":" << p.second << " ";
    std::cout << "\n";

    return 0;
}
```

**运行结果**：

```
Vec<int>: 1 2 3 
Vec<double>: 1.1 2.2 3.3 
StringMap<int>: Alice:85 Bob:92 
```

### 示例 4：在示例 3 基础上，using 别名不是新类型

```cpp
#include <iomanip>
#include <iostream>

using Meter = double;
using Second = double;

double speed(Meter distance, Second time)
{
    return distance / time;
}

struct MeterValue
{
    double value;
};

struct SecondValue
{
    double value;
};

double safe_speed(MeterValue distance, SecondValue time)
{
    return distance.value / time.value;
}

int main()
{
    Meter distance = 100.0;
    Second time = 9.58;

    std::cout << std::fixed << std::setprecision(2);
    std::cout << "speed = " << speed(distance, time) << "\n";

    // using 只是别名，下面这行语义错了，但编译器仍然允许
    std::cout << "wrong order = " << speed(time, distance) << "\n";

    MeterValue d{100.0};
    SecondValue t{9.58};
    std::cout << "safe speed = " << safe_speed(d, t) << "\n";

    // safe_speed(t, d); // ❌ 编译错误：SecondValue 不能当 MeterValue 用

    return 0;
}
```

**运行结果**：

```
speed = 10.44
wrong order = 0.10
safe speed = 10.44
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 基础类型别名 | `using Name = Type;` | 语法更直观，`=` 让人一眼看出"别名 = 原类型" | typedef 也能做，但 using 更清晰 |
| 示例 2 | 函数指针别名 | `using Func = int(*)(int,int);` | 名字在中间，比 typedef 的名字夹在中间更好读 | 实际项目中建议用 `std::function` 代替裸函数指针 |
| 示例 3 | 模板别名 | `template<T> using Vec = vector<T>;` | typedef 做不到模板别名，这是 using 的独特能力 | 非常适合简化嵌套模板类型 |
| 示例 4 | using 不是新类型 | `using Meter = double`、包装结构体 | 别名提升可读性，结构体提供类型安全 | 需要防止参数传反时，不要只靠 using |

## 常见错误

**错误 1：把 using 别名的 `=` 方向写反**

```cpp
using int = MyInt;  // ❌ 方向反了！应该是 using 别名 = 原类型
```

正确做法：`using MyInt = int;`

**错误 2：在头文件中不加限制地使用 using**

```cpp
// 头文件中
using namespace std;  // ❌ 污染全局/外部命名空间！
```

正确做法：头文件中避免 `using namespace`，只在 .cpp 的实现文件或函数内部使用。

**错误 3：using 声明和 using 别名混淆**

```cpp
using std::cout;       // using 声明：引入名字
using MyVec = std::vector<int>;  // using 别名：创建别名
```

这是两种不同的用法，前者是引入名字到当前作用域，后者是创建类型别名。

**错误 4：以为 using 能提供强类型检查**

```cpp
using UserId = int;
using ProductId = int;

void load_user(UserId id);

ProductId pid = 10;
load_user(pid);  // 能编译，因为 UserId 和 ProductId 本质都是 int
```

正确做法：如果必须防止混用，用 `struct UserId { int value; };` 这种包装类型，或者用 `enum class` 表达有限集合。

## 使用建议

1. **用 `using` 代替 `typedef`**：语法更清晰，还支持模板别名。
2. **善用模板别名简化代码**：例如 `using TaskMap = std::map<int, std::function<void()>>;`。
3. **不要在头文件中 `using namespace`**：避免命名空间污染。
4. **using 声明（`using std::cout`）可以用在 .cpp 或函数内部**，简化代码。
5. **需要强类型时不要只用 using**：using 改善可读性，`struct/class/enum class` 才能改变类型本身。

## 小结

- `using NewName = OldType;` 比 `typedef` 语法更直观。
- `template<typename T> using Alias = ...;` 是 typedef 做不到的模板别名。
- 函数指针别名、容器类型别名在项目中很实用。
- `using` 只是别名，不是新类型；需要类型安全时要引入包装类型。
- 别混淆 `using` 别名和 `using` 声明（命名空间引入）。
