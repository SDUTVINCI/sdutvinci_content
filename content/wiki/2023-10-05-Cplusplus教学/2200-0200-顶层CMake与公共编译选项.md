---
title: "顶层CMake与公共编译选项"
---

本节讲两个文件：

1. 顶层 `CMakeLists.txt`
2. `cmake/ProjectOptions.cmake`

这两个文件决定了整个工程的基本身份和公共编译规则。

## 顶层 `CMakeLists.txt`

本模板的顶层文件如下：

```cmake
cmake_minimum_required(VERSION 3.25)

project(cmake_template VERSION 1.0.0 LANGUAGES C CXX)

include(GNUInstallDirs)

include("${CMAKE_CURRENT_LIST_DIR}/cmake/ProjectOptions.cmake")

add_subdirectory(src)
```

顶层 `CMakeLists.txt` 的原则是：只做总控，不堆具体业务和第三方依赖。

## `cmake_minimum_required`

```cmake
cmake_minimum_required(VERSION 3.25)
```

作用：

1. 指定项目需要的最低 CMake 版本。
2. 告诉 CMake 使用对应版本的策略行为。
3. 太老的 CMake 会直接报错，不会继续配置。

可以填什么：

```cmake
cmake_minimum_required(VERSION 3.20)
cmake_minimum_required(VERSION 3.25)
cmake_minimum_required(VERSION 3.28)
```

选择建议：

| 写法 | 说明 |
|:---|:---|
| `3.10` | 太老，很多现代 CMake 写法不方便 |
| `3.16` | Ubuntu 20.04 常见版本，兼容性好 |
| `3.22` | Ubuntu 22.04 常见版本 |
| `3.25` | 本模板使用，和 preset version 6 搭配 |
| `3.28+` | 适合较新的系统，但模板通用性下降 |

教学模板建议不要无限追新。只要满足当前写法即可。

## `project`

```cmake
project(cmake_template VERSION 1.0.0 LANGUAGES C CXX)
```

作用：

1. 设置项目名。
2. 设置项目版本。
3. 声明项目使用哪些语言。
4. 初始化一批 CMake 变量。

### 项目名

```cmake
project(cmake_template)
```

项目名会影响这些变量：

| 变量 | 值 |
|:---|:---|
| `PROJECT_NAME` | `cmake_template` |
| `CMAKE_PROJECT_NAME` | 顶层项目名，通常也是 `cmake_template` |

本模板在 `src/CMakeLists.txt` 中使用：

```cmake
add_executable(${PROJECT_NAME}
  ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
)
```

所以最终可执行文件名就是：

```text
cmake_template
```

如果把项目名改成：

```cmake
project(robot_app VERSION 1.0.0 LANGUAGES C CXX)
```

最终可执行文件就会变成：

```text
robot_app
```

### `VERSION`

```cmake
VERSION 1.0.0
```

项目版本号。

可以填什么：

```cmake
VERSION 0.1.0
VERSION 1.0.0
VERSION 2.3.4
```

CMake 会生成这些变量：

| 变量 | 示例值 |
|:---|:---|
| `PROJECT_VERSION` | `1.0.0` |
| `PROJECT_VERSION_MAJOR` | `1` |
| `PROJECT_VERSION_MINOR` | `0` |
| `PROJECT_VERSION_PATCH` | `0` |

以后如果做安装包、导出 CMake package、生成版本头文件，这些变量会很有用。

### `LANGUAGES`

```cmake
LANGUAGES C CXX
```

声明项目启用哪些语言。

常见值：

| 值 | 说明 |
|:---|:---|
| `C` | C 语言 |
| `CXX` | C++ |
| `CUDA` | CUDA |
| `ASM` | 汇编 |
| `Fortran` | Fortran |

如果项目只有 C++，也可以写：

```cmake
project(cmake_template VERSION 1.0.0 LANGUAGES CXX)
```

本模板保留 `C CXX`，是为了允许 `src/*.c` 和 `src/*.cpp` 都能参与构建。

## `include(GNUInstallDirs)`

```cmake
include(GNUInstallDirs)
```

作用：加载 GNU 风格的安装目录变量。

常用变量：

| 变量 | 常见值 | 用途 |
|:---|:---|:---|
| `CMAKE_INSTALL_BINDIR` | `bin` | 可执行文件 |
| `CMAKE_INSTALL_LIBDIR` | `lib` 或 `lib64` | 动态库、静态库 |
| `CMAKE_INSTALL_INCLUDEDIR` | `include` | 头文件 |
| `CMAKE_INSTALL_DATADIR` | `share` | 数据文件 |
| `CMAKE_INSTALL_DOCDIR` | `share/doc/项目名` | 文档 |

为什么不用手写：

```cmake
install(TARGETS app DESTINATION bin)
install(TARGETS lib DESTINATION lib)
```

因为不同 Linux 发行版可能使用 `lib` 或 `lib64`。用 `GNUInstallDirs` 后，CMake 会根据系统习惯设置。

模板中的使用示例：

```cmake
install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
```

库安装：

```cmake
install(TARGETS ${PREFIX}_src_lib
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
```

头文件安装：

```cmake
install(DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/inc/"
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
```

## `include("${CMAKE_CURRENT_LIST_DIR}/cmake/ProjectOptions.cmake")`

```cmake
include("${CMAKE_CURRENT_LIST_DIR}/cmake/ProjectOptions.cmake")
```

作用：读取另一个 CMake 文件。

### `include(...)`

`include` 会在当前作用域执行指定的 CMake 脚本。

可以写：

```cmake
include(GNUInstallDirs)
include(cmake/ProjectOptions.cmake)
include("${CMAKE_CURRENT_LIST_DIR}/cmake/ProjectOptions.cmake")
```

区别：

| 写法 | 说明 |
|:---|:---|
| `include(GNUInstallDirs)` | 加载 CMake 内置模块 |
| `include(cmake/ProjectOptions.cmake)` | 相对路径写法 |
| `include("${CMAKE_CURRENT_LIST_DIR}/cmake/ProjectOptions.cmake")` | 更稳，不受当前工作目录影响 |

### `CMAKE_CURRENT_LIST_DIR`

表示当前正在处理的 CMake 文件所在目录。

在顶层 `CMakeLists.txt` 中，它就是项目根目录。

所以：

```cmake
"${CMAKE_CURRENT_LIST_DIR}/cmake/ProjectOptions.cmake"
```

表示：

```text
项目根目录/cmake/ProjectOptions.cmake
```

## `add_subdirectory(src)`

```cmake
add_subdirectory(src)
```

作用：进入 `src` 子目录，继续读取 `src/CMakeLists.txt`。

可以填什么：

```cmake
add_subdirectory(src)
add_subdirectory(test)
add_subdirectory(third_party/some_lib)
```

常见写法：

```cmake
add_subdirectory(<源码目录>)
```

也可以指定构建目录：

```cmake
add_subdirectory(src src_build)
```

但一般不需要。

顶层只写：

```cmake
add_subdirectory(src)
```

表示具体的可执行文件、库、依赖，都交给 `src` 下面的 CMake 文件管理。

## `cmake/ProjectOptions.cmake`

本模板内容如下：

```cmake
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_library(project_options INTERFACE)
target_compile_features(project_options INTERFACE cxx_std_17)

add_library(project_warnings INTERFACE)
target_compile_options(project_warnings INTERFACE
  -Wall
  -Wextra
  -Wpedantic
)
```

这个文件只放项目公共选项，不放 Eigen、OpenCV 等第三方依赖。

## `CMAKE_CXX_STANDARD`

```cmake
set(CMAKE_CXX_STANDARD 17)
```

设置默认 C++ 标准。

常见值：

| 值 | 标准 |
|:---|:---|
| `11` | C++11 |
| `14` | C++14 |
| `17` | C++17 |
| `20` | C++20 |
| `23` | C++23 |

本模板使用 C++17：

```cmake
set(CMAKE_CXX_STANDARD 17)
```

如果想用 C++20：

```cmake
set(CMAKE_CXX_STANDARD 20)
target_compile_features(project_options INTERFACE cxx_std_20)
```

注意两个地方最好一起改。

## `CMAKE_CXX_STANDARD_REQUIRED`

```cmake
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

作用：要求编译器必须支持指定的 C++ 标准。

可以填什么：

| 值 | 说明 |
|:---|:---|
| `ON` | 必须使用指定标准，不支持就报错 |
| `OFF` | 不支持时可能退回较低标准 |

模板建议写 `ON`。否则你以为在用 C++17，实际编译器可能退回旧标准，错误会更隐蔽。

## `CMAKE_CXX_EXTENSIONS`

```cmake
set(CMAKE_CXX_EXTENSIONS OFF)
```

作用：控制是否使用编译器扩展标准。

可以填什么：

| 值 | 编译选项倾向 | 说明 |
|:---|:---|:---|
| `ON` | `gnu++17` | 允许 GNU 扩展 |
| `OFF` | `c++17` | 更标准、更可移植 |

Linux-only 项目也建议先写 `OFF`，这样代码更接近标准 C++。

## C 标准相关选项

```cmake
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)
```

含义和 C++ 对应选项类似。

常见 C 标准：

| 值 | 标准 |
|:---|:---|
| `99` | C99 |
| `11` | C11 |
| `17` | C17 |
| `23` | C23 |

如果项目没有 `.c` 文件，也可以只启用 C++：

```cmake
project(cmake_template VERSION 1.0.0 LANGUAGES CXX)
```

然后删除 C 标准相关设置。

## `CMAKE_EXPORT_COMPILE_COMMANDS`

```cmake
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
```

作用：生成 `compile_commands.json`。

可以填：

| 值 | 说明 |
|:---|:---|
| `ON` | 生成 |
| `OFF` | 不生成 |

这个选项在 `CMakePresets.json` 中也写了：

```json
"CMAKE_EXPORT_COMPILE_COMMANDS": "ON"
```

两个地方都写不冲突。preset 的好处是 IDE 和命令行都能明确看到这个配置；`ProjectOptions.cmake` 的好处是即使不用 preset，也默认开启。

## `add_library(project_options INTERFACE)`

```cmake
add_library(project_options INTERFACE)
```

创建一个 interface target。

`INTERFACE` target 没有源码，不会生成 `.a` 或 `.so`。它只用来携带编译特性、宏定义、include 路径、链接依赖等"使用要求"。

`add_library` 常见类型：

| 类型 | 是否有源码 | 是否生成产物 | 用途 |
|:---|:---|:---|:---|
| `STATIC` | 有 | `.a` | 静态库 |
| `SHARED` | 有 | `.so` | 动态库 |
| `MODULE` | 有 | 插件式动态模块 |
| `OBJECT` | 有 | `.o` 集合 | 复用对象文件 |
| `INTERFACE` | 无 | 无 | 传递配置 |

本模板用：

```cmake
project_options
```

保存 C++ 标准要求。

## `target_compile_features`

```cmake
target_compile_features(project_options INTERFACE cxx_std_17)
```

作用：声明使用这个 target 的目标需要 C++17。

可以填什么：

| 写法 | 说明 |
|:---|:---|
| `cxx_std_11` | 至少 C++11 |
| `cxx_std_14` | 至少 C++14 |
| `cxx_std_17` | 至少 C++17 |
| `cxx_std_20` | 至少 C++20 |
| `cxx_std_23` | 至少 C++23 |

为什么这里又写了一遍 C++17？

```cmake
set(CMAKE_CXX_STANDARD 17)
```

是全局默认值。

```cmake
target_compile_features(project_options INTERFACE cxx_std_17)
```

是目标级要求。现代 CMake 更推荐用目标级要求，因为它能随着 target 链接关系传播。

## `add_library(project_warnings INTERFACE)`

```cmake
add_library(project_warnings INTERFACE)
```

创建一个专门保存 warning 选项的 interface target。

这样每个 target 可以选择是否链接：

```cmake
target_link_libraries(my_target
  PRIVATE
    project_warnings
)
```

## `target_compile_options`

```cmake
target_compile_options(project_warnings INTERFACE
  -Wall
  -Wextra
  -Wpedantic
)
```

作用：给目标添加编译选项。

本模板选项：

| 选项 | 作用 |
|:---|:---|
| `-Wall` | 开启一组常见警告 |
| `-Wextra` | 开启更多警告 |
| `-Wpedantic` | 提醒不符合标准的写法 |

常见可追加选项：

| 选项 | 说明 |
|:---|:---|
| `-Wconversion` | 类型转换可能丢失信息时警告 |
| `-Wshadow` | 变量名遮蔽时警告 |
| `-Werror` | 把 warning 当 error |

不建议模板默认加 `-Werror`。因为不同编译器、不同第三方头文件可能产生不同 warning，初学阶段容易被卡住。

## `PUBLIC`、`PRIVATE`、`INTERFACE` 的直觉

后面的 CMake 文件会大量出现：

```cmake
PUBLIC
PRIVATE
INTERFACE
```

它们控制"属性是否传递给使用者"。

| 关键字 | 自己用 | 传给别人 |
|:---|:---:|:---:|
| `PRIVATE` | 是 | 否 |
| `PUBLIC` | 是 | 是 |
| `INTERFACE` | 否 | 是 |

例如：

```cmake
target_link_libraries(lib1_src_lib
  PUBLIC
    project_options
  PRIVATE
    project_warnings
)
```

含义：

1. `lib1_src_lib` 自己需要 `project_options`。
2. 链接 `lib1_src_lib` 的目标也会得到 `project_options`。
3. `project_warnings` 只用于编译 `lib1_src_lib` 自己，不强迫下游也开启相同 warning。

