---
title: "std::filesystem"
---

## 本节解决什么问题

在 C++17 之前，操作文件和目录（遍历、创建、删除、检查是否存在）需要依赖平台特定的 API（如 POSIX 的 `<dirent.h>`、Windows 的 `FindFirstFile`），代码不能跨平台。

`std::filesystem` 提供了**跨平台的文件系统操作**，一套代码在 Linux、macOS、Windows 上都能用。

## 这个特性是什么

`std::filesystem` 是 C++17 引入的文件系统库，提供了路径处理、文件/目录操作、遍历、权限查询等功能。核心类是 `std::filesystem::path`。

## C++ 标准版本

C++17

## 需要的头文件

```cpp
#include <filesystem>
```

## 基本语法

```cpp
namespace fs = std::filesystem;

fs::path p = "/home/user/data.txt";  // 路径对象

// 检查文件
fs::exists(p);          // 是否存在
fs::is_regular_file(p); // 是否是普通文件
fs::is_directory(p);    // 是否是目录
fs::file_size(p);       // 文件大小

// 操作
fs::create_directory(p);     // 创建目录
fs::copy(src, dst);           // 拷贝文件
fs::remove(p);                // 删除文件/目录
fs::rename(old, new);         // 重命名

// 遍历
for (const auto& entry : fs::directory_iterator(p)) { ... }      // 非递归
for (const auto& entry : fs::recursive_directory_iterator(p)) {} // 递归
```

## 常用操作

| 操作 | 说明 |
|:---|:---|
| `fs::exists(p)` | 路径是否存在 |
| `fs::is_directory(p)` | 是否目录 |
| `fs::is_regular_file(p)` | 是否普通文件 |
| `fs::create_directory(p)` | 创建目录 |
| `fs::create_directories(p)` | 递归创建目录（类似 mkdir -p） |
| `fs::copy(src, dst)` | 拷贝文件 |
| `fs::remove(p)` | 删除文件或空目录 |
| `fs::remove_all(p)` | 递归删除（类似 rm -rf） |
| `fs::rename(old, new)` | 重命名/移动 |
| `p.filename()` | 获取文件名 |
| `p.extension()` | 获取扩展名 |
| `p.parent_path()` | 获取父目录 |
| `p / "subdir"` | 路径拼接（用 `/` 运算符） |

## 示例代码

### 示例 1：检查文件/目录是否存在和大小的信息

```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main()
{
    // 用当前目录做演示
    fs::path current = fs::current_path();
    std::cout << "Current path: " << current << "\n";
    std::cout << "Exists: " << std::boolalpha << fs::exists(current) << "\n";
    std::cout << "Is directory: " << fs::is_directory(current) << "\n";

    // 获取路径的各部分
    std::cout << "Filename: " << current.filename() << "\n";
    std::cout << "Parent path: " << current.parent_path() << "\n";
    std::cout << "Root path: " << current.root_path() << "\n";

    return 0;
}
```

**运行结果**（路径因机器而异）：

```
Current path: "/home/user/MySource/my-blog"
Exists: true
Is directory: true
Filename: "my-blog"
Parent path: "/home/user/MySource"
Root path: "/"
```

### 示例 2：在示例 1 基础上，遍历目录中的所有文件

```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main()
{
    fs::path dir = fs::current_path();

    std::cout << "Contents of " << dir << ":\n";
    std::cout << "--------------------------------\n";

    // 遍历当前目录（不递归子目录）
    for (const auto& entry : fs::directory_iterator(dir))
    {
        std::string type = entry.is_directory() ? "[DIR] " : "[FILE]";
        std::cout << type << " " << entry.path().filename().string() << "\n";
    }

    return 0;
}
```

**运行结果**（因目录内容而异）：

```
Contents of "/home/user/MySource/my-blog":
--------------------------------
[DIR]  .git
[DIR]  app
[DIR]  content
[FILE] nuxt.config.ts
[FILE] package.json
[FILE] README.md
...
```

### 示例 3：在示例 2 基础上，过滤特定扩展名的文件

```cpp
#include <iostream>
#include <filesystem>
#include <vector>

namespace fs = std::filesystem;

// 查找指定目录下所有 .md 文件
std::vector<fs::path> find_md_files(const fs::path& dir)
{
    std::vector<fs::path> result;
    for (const auto& entry : fs::recursive_directory_iterator(dir))
    {
        if (entry.is_regular_file() && entry.path().extension() == ".md")
        {
            result.push_back(entry.path());
        }
    }
    return result;
}

int main()
{
    fs::path dir = fs::current_path() / "content";

    if (!fs::exists(dir))
    {
        std::cout << "Directory not found: " << dir << "\n";
        return 1;
    }

    auto md_files = find_md_files(dir);

    std::cout << "Found " << md_files.size() << " .md files:\n";
    for (const auto& f : md_files)
    {
        std::cout << "  " << f.filename().string() << "\n";
    }

    return 0;
}
```

**运行结果**（因文件内容而异）：

```
Found 15 .md files:
  index.md
  ch2-前言.md
  2000-现代C++.md
  ...
```

### 示例 4：在示例 3 基础上，创建目录、文件并拷贝

```cpp
#include <iostream>
#include <filesystem>
#include <fstream>

namespace fs = std::filesystem;

int main()
{
    fs::path test_dir = fs::temp_directory_path() / "cpp_test_demo";

    // 创建目录（如果已存在也不报错）
    if (fs::create_directory(test_dir))
    {
        std::cout << "Created: " << test_dir << "\n";
    }
    else
    {
        std::cout << "Already exists: " << test_dir << "\n";
    }

    // 创建一个文件
    fs::path file_path = test_dir / "hello.txt";
    {
        std::ofstream out(file_path);
        out << "Hello, filesystem!\n";
    }
    std::cout << "Created file: " << file_path << "\n";
    std::cout << "File size: " << fs::file_size(file_path) << " bytes\n";

    // 拷贝文件
    fs::path copy_path = test_dir / "hello_copy.txt";
    fs::copy(file_path, copy_path);
    std::cout << "Copied to: " << copy_path << "\n";

    // 检查两个文件都存在
    std::cout << "hello.txt exists: " << fs::exists(file_path) << "\n";
    std::cout << "hello_copy.txt exists: " << fs::exists(copy_path) << "\n";

    // 清理：删除测试目录
    fs::remove_all(test_dir);
    std::cout << "Cleaned up: " << test_dir << "\n";

    return 0;
}
```

**运行结果**：

```
Created: "/tmp/cpp_test_demo"
Created file: "/tmp/cpp_test_demo/hello.txt"
File size: 21 bytes
Copied to: "/tmp/cpp_test_demo/hello_copy.txt"
hello.txt exists: true
hello_copy.txt exists: true
Cleaned up: "/tmp/cpp_test_demo"
```

## 运行结果

见上方每个示例的"运行结果"。

## 示例中的关键语法解释

| 示例 | 讲了什么 | 新出现的语法 | 为什么这样写 | 注意事项 |
|:---|:---|:---|:---|:---|
| 示例 1 | 路径信息查询 | `fs::current_path()`、`exists()`、`filename()` | 获取当前路径和各组成部分 | 所有路径操作都是跨平台的 |
| 示例 2 | 遍历目录 | `fs::directory_iterator` | 遍历当前目录下的所有条目 | 仅遍历一级，不会递归进入子目录 |
| 示例 3 | 递归遍历和过滤 | `fs::recursive_directory_iterator`、`extension()` | 递归找特定后缀的文件 | 递归遍历可能很慢，注意目录深度 |
| 示例 4 | 创建、拷贝、删除 | `create_directory()`、`copy()`、`remove_all()` | 常见文件操作，跨平台 | `remove_all` 类似 rm -rf，小心使用 |

## 抛异常版本 vs error_code 版本

很多 filesystem 函数有两种用法：

```cpp
auto size = fs::file_size(path);      // 失败时抛异常

std::error_code ec;
auto size = fs::file_size(path, ec);  // 失败时不抛异常，错误写入 ec
```

| 写法 | 优点 | 适合场景 |
|:---|:---|:---|
| 抛异常版本 | 代码短，失败自动中断 | 文件理应存在，失败就是异常 |
| `error_code` 版本 | 不抛异常，便于继续处理 | 扫描大量文件、权限不确定、允许跳过失败项 |

### 示例 5：扫描目录时用 error_code 跳过失败项

```cpp
#include <filesystem>
#include <iostream>
#include <system_error>

// filesystem 用来跨平台处理路径、文件和目录。
namespace fs = std::filesystem;

int main()
{
    // 程序从 main 函数开始执行，下面的语句会按顺序运行。
    fs::path dir = fs::current_path();

    for (const auto& entry : fs::directory_iterator(dir))
    {
        std::error_code ec;
        auto size = fs::file_size(entry.path(), ec);

        if (ec)
        {
            std::cout << "[skip] " << entry.path().filename().string()
                      << " reason: " << ec.message() << "\n";
            continue;
        }

        std::cout << entry.path().filename().string()
                  << " size = " << size << "\n";
    }

    return 0;
}
```

**运行结果**（因目录内容而异）：

```
[skip] content reason: Is a directory
package.json size = 1234
README.md size = 2048
```

扫描目录时，遇到目录、权限不足、符号链接异常都很常见。用 `error_code` 可以让程序跳过坏项继续跑，而不是第一个失败就退出。

## 常见错误

**错误 1：忘记检查 exists 就直接操作**

```cpp
std::cout << fs::file_size("/nonexistent");  // ❌ 抛异常！
```

正确做法：先 `exists()` 检查，或使用 try-catch。

**错误 2：`create_directory` 和 `create_directories` 混淆**

```cpp
fs::create_directory("a/b/c");  // ❌ 如果 a/b 不存在则失败！
```

正确做法：`fs::create_directories("a/b/c");` 会递归创建。

**错误 3：用 `==` 比较路径**

```cpp
if (path1 == path2)  // 只是字符串比较！
```

路径 `/home/user` 和 `/home/user/` 可能不相等，用 `fs::equivalent(p1, p2)` 做规范比较。

**错误 4：链接时需要 `-lstdc++fs`（旧编译器）**

在 GCC 8 及以前，需要链接 `stdc++fs` 库：`g++ -std=c++17 file.cpp -lstdc++fs`。GCC 9+ 和 Clang 不需要。

## 使用建议

1. **用 `namespace fs = std::filesystem;` 简化代码**：这个缩写几乎是社区标准。
2. **路径用 `/` 运算符拼接**：`auto p = base / "subdir" / "file.txt";`，简洁且跨平台。
3. **遍历大目录用 `directory_iterator`**：避免一次性加载所有文件。
4. **文件操作注意异常安全**：很多 filesystem 函数在失败时会抛 `fs::filesystem_error`。
5. **C++17 的 filesystem 基本够用**：C++20/23 增加了一些便利函数但核心不变。
6. **扫描大量文件时考虑 error_code 重载**：避免一个异常路径中断整个扫描。

## 小结

- `std::filesystem` 提供跨平台的文件系统操作。
- `fs::path` 是路径类，支持 `/` 拼接、拆分（`filename()`/`extension()`/`parent_path()`）。
- `exists()` 检查存在，`create_directory()` 创建目录，`copy()`/`remove()` 等操作文件。
- `directory_iterator` 遍历目录，`recursive_directory_iterator` 递归遍历。
- 操作前检查 `exists()`，注意异常处理。
