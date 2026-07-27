---
title: "src与lib模块CMake详解"
---

本节讲三个层次：

1. `src/CMakeLists.txt` 如何创建主程序。
2. `src/lib1/CMakeLists.txt` 如何创建库。
3. `src/lib2/CMakeLists.txt` 为什么和 `lib1` 类似但 target 不能重名。

## `src/CMakeLists.txt`

文件内容：

```cmake
add_executable(${PROJECT_NAME}
  ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
)

target_link_libraries(${PROJECT_NAME}
  PRIVATE
    project_options
    project_warnings
)

add_subdirectory(lib1)
add_subdirectory(lib2)

target_link_libraries(lib1_src_lib
  PRIVATE
    lib2_src_lib
)

target_link_libraries(${PROJECT_NAME}
  PRIVATE
    lib1_src_lib
    lib2_src_lib
)

set_target_properties(${PROJECT_NAME} PROPERTIES
  INSTALL_RPATH "$ORIGIN/../${CMAKE_INSTALL_LIBDIR}"
)

install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
```

这个文件负责最终可执行文件。

## `add_executable`

```cmake
add_executable(${PROJECT_NAME}
  ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
)
```

作用：创建可执行文件 target。

常见写法：

```cmake
add_executable(app main.cpp)
add_executable(robot_main main.cpp robot.cpp)
add_executable(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp)
```

第一个参数是 target 名，也通常是生成的可执行文件名。

本模板使用：

```cmake
${PROJECT_NAME}
```

而 `PROJECT_NAME` 来自顶层：

```cmake
project(cmake_template VERSION 1.0.0 LANGUAGES C CXX)
```

所以可执行文件叫：

```text
cmake_template
```

### `CMAKE_CURRENT_SOURCE_DIR`

```cmake
${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
```

表示当前 `CMakeLists.txt` 所在源码目录。

在 `src/CMakeLists.txt` 中：

```text
CMAKE_CURRENT_SOURCE_DIR = 项目根目录/src
```

所以：

```cmake
${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
```

就是：

```text
项目根目录/src/main.cpp
```

## 给主程序链接公共选项

```cmake
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    project_options
    project_warnings
)
```

虽然 `project_options` 和 `project_warnings` 不是普通库，但它们是 CMake target，所以也通过 `target_link_libraries` 传递。

这里用 `PRIVATE`，表示：

1. 主程序自己使用 C++17 编译要求。
2. 主程序自己开启 warning。
3. 主程序不需要把这些要求再传给别人，因为可执行文件一般不会被其他 target 链接。

## 添加子目录

```cmake
add_subdirectory(lib1)
add_subdirectory(lib2)
```

作用：进入 `src/lib1` 和 `src/lib2`，读取它们各自的 `CMakeLists.txt`。

执行完这两行之后，下面两个 target 才存在：

```cmake
lib1_src_lib
lib2_src_lib
```

所以通常先：

```cmake
add_subdirectory(lib1)
add_subdirectory(lib2)
```

再设置库之间的依赖和主程序要链接的库：

```cmake
target_link_libraries(lib1_src_lib
  PRIVATE
    lib2_src_lib
)

target_link_libraries(${PROJECT_NAME}
  PRIVATE
    lib1_src_lib
    lib2_src_lib
)
```

## 让 lib1 单向调用 lib2

本模板现在用 `lib1` 调用 `lib2` 做一个单向依赖示例。`src/main.cpp` 只直接调用 `lib1`：

```cpp
#include "lib1/eigen3_test.hpp"

int main()
{
    lib1::run_eigen_vector_example();
    return 0;
}
```

然后在 `src/lib1/src/eigen3_test.cpp` 中，`lib1` 再调用 `lib2`：

```cpp
#include "lib1/eigen3_test.hpp"
#include "lib2/eigen3_test.hpp"

#include <Eigen/Dense>
#include <iostream>

namespace lib1 {

void run_eigen_vector_example()
{
    const Eigen::Vector3d vector(1.0, 2.0, 3.0);

    std::cout << "[lib1] Vector v = " << vector.transpose() << '\n';
    std::cout << "[lib1] Norm = " << vector.norm() << '\n';

    lib2::run_eigen_matrix_example();
}

}  // namespace lib1
```

因此 `lib1_src_lib` 必须显式链接 `lib2_src_lib`：

```cmake
target_link_libraries(lib1_src_lib
  PRIVATE
    lib2_src_lib
)
```

这句话建议放在：

```cmake
add_subdirectory(lib1)
add_subdirectory(lib2)
```

之后。因为执行完 `add_subdirectory(lib1)` 和 `add_subdirectory(lib2)` 后，`lib1_src_lib`、`lib2_src_lib` 这两个 target 才都已经存在。

这里用 `PRIVATE`，表示 `lib2` 只是 `lib1` 自己实现时需要用到的库。CMake 会让 `lib1` 在编译时找到 `lib2/eigen3_test.hpp`，也会在链接时找到 `lib2` 里的函数实现，但不会强迫所有链接 `lib1` 的目标都自动使用 `lib2` 的头文件。

如果以后 `lib1` 的公开头文件里直接包含了 `lib2` 的头文件，或者公开接口里使用了 `lib2` 的类型，就应该改成 `PUBLIC`：

```cmake
target_link_libraries(lib1_src_lib
  PUBLIC
    lib2_src_lib
)
```

注意，这里是单向依赖：

```text
lib1 -> lib2
```

不要再反过来让 `lib2` 链接 `lib1`。如果两个库真的需要使用同一段代码，通常应该把公共部分抽到 `common` 之类的新库中。

## 链接 lib1 和 lib2

```cmake
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    lib1_src_lib
    lib2_src_lib
)
```

作用：让主程序链接模板中的两个示例库。

当前 `main.cpp` 只直接调用：

```cpp
lib1::run_eigen_vector_example();
```

而 `lib1::run_eigen_vector_example()` 内部又会调用：

```cpp
lib2::run_eigen_matrix_example();
```

所以这个模板的实际调用路径是：

```text
main.cpp -> lib1 -> lib2
```

函数实现位于：

```text
src/lib1/src/eigen3_test.cpp
src/lib2/src/eigen3_test.cpp
```

它们分别被编译进：

```text
lib1_src_lib
lib2_src_lib
```

所以主程序至少需要链接 `lib1_src_lib`，而 `lib1_src_lib` 又需要链接 `lib2_src_lib`。

本模板这里仍然让主程序同时链接 `lib1_src_lib` 和 `lib2_src_lib`，是为了保留"主程序挂载两个示例库"的直观结构。实际项目中，如果 `main.cpp` 完全不直接调用 `lib2`，也可以只让主程序链接 `lib1_src_lib`，由 `lib1 -> lib2` 这条依赖关系带出 `lib2`。

## `INSTALL_RPATH`

```cmake
set_target_properties(${PROJECT_NAME} PROPERTIES
  INSTALL_RPATH "$ORIGIN/../${CMAKE_INSTALL_LIBDIR}"
)
```

作用：设置安装后的可执行文件运行时如何找到动态库。

本模板的库安装到：

```text
install/linux-debug/lib64/
```

可执行文件安装到：

```text
install/linux-debug/bin/
```

运行：

```bash
./install/linux-debug/bin/cmake_template
```

程序需要找到：

```text
install/linux-debug/lib64/liblib1_src_lib.so
install/linux-debug/lib64/liblib2_src_lib.so
```

`$ORIGIN` 表示可执行文件所在目录，也就是：

```text
install/linux-debug/bin
```

所以：

```text
$ORIGIN/../lib64
```

就是：

```text
install/linux-debug/lib64
```

其中 `${CMAKE_INSTALL_LIBDIR}` 可能是 `lib`，也可能是 `lib64`。

常见可填值：

| 写法 | 说明 |
|:---|:---|
| `"$ORIGIN/../${CMAKE_INSTALL_LIBDIR}"` | 推荐，安装后相对查找库 |
| `"/usr/local/lib"` | 写死系统路径，不适合模板 |
| `""` | 不设置 RPATH，需要依赖系统库路径或 `LD_LIBRARY_PATH` |

因为本模板已经设置 RPATH，所以不需要旧脚本去 `source` 环境变量。

## 安装主程序

```cmake
install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
```

作用：安装可执行文件。

`install(TARGETS ...)` 常见分类：

| 类型 | 对应产物 | 常用安装目录 |
|:---|:---|:---|
| `RUNTIME` | 可执行文件，Windows DLL | `${CMAKE_INSTALL_BINDIR}` |
| `LIBRARY` | Linux 动态库 `.so` | `${CMAKE_INSTALL_LIBDIR}` |
| `ARCHIVE` | 静态库 `.a` | `${CMAKE_INSTALL_LIBDIR}` |

主程序是可执行文件，所以使用：

```cmake
RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
```

安装后路径通常是：

```text
install/linux-debug/bin/cmake_template
```

## lib1 的 CMakeLists

`src/lib1/CMakeLists.txt`：

```cmake
set(PREFIX "lib1")

file(GLOB_RECURSE ${PREFIX}_SRC_LIST CONFIGURE_DEPENDS
  "${CMAKE_CURRENT_LIST_DIR}/src/*.c"
  "${CMAKE_CURRENT_LIST_DIR}/src/*.cpp"
)

add_library(${PREFIX}_src_lib SHARED
  ${${PREFIX}_SRC_LIST}
)

target_include_directories(${PREFIX}_src_lib
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/inc>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_link_libraries(${PREFIX}_src_lib
  PUBLIC
    project_options
  PRIVATE
    project_warnings
)

# ========================
# Third-party dependencies
# ========================
find_package(Eigen3 REQUIRED)

target_link_libraries(${PREFIX}_src_lib
  PUBLIC
    Eigen3::Eigen
)

# =======
# Install
# =======
install(TARGETS ${PREFIX}_src_lib
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/inc/"
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
```

## `set(PREFIX "lib1")`

```cmake
set(PREFIX "lib1")
```

作用：设置一个局部变量，方便复用。

后面这些名字都会由 `PREFIX` 生成：

| 表达式 | 展开后 |
|:---|:---|
| `${PREFIX}_SRC_LIST` | `lib1_SRC_LIST` |
| `${PREFIX}_src_lib` | `lib1_src_lib` |

如果复制这个文件给 `lib3`，只需要改：

```cmake
set(PREFIX "lib3")
```

target 名就会变成：

```text
lib3_src_lib
```

注意：target 名不能重名。`lib1` 和 `lib2` 的 `.cpp` 文件可以都叫 `eigen3_test.cpp`，但 CMake target 不能都叫 `src_lib`。

## `file(GLOB_RECURSE ...)`

```cmake
file(GLOB_RECURSE ${PREFIX}_SRC_LIST CONFIGURE_DEPENDS
  "${CMAKE_CURRENT_LIST_DIR}/src/*.c"
  "${CMAKE_CURRENT_LIST_DIR}/src/*.cpp"
)
```

作用：收集源码文件。

参数解释：

| 参数 | 作用 |
|:---|:---|
| `GLOB_RECURSE` | 递归查找子目录 |
| `${PREFIX}_SRC_LIST` | 保存结果的变量名 |
| `CONFIGURE_DEPENDS` | 源文件列表变化时，触发 CMake 重新配置 |
| `"src/*.c"` | 匹配 C 文件 |
| `"src/*.cpp"` | 匹配 C++ 文件 |

可以填什么：

```cmake
file(GLOB_RECURSE MY_SOURCES CONFIGURE_DEPENDS
  "${CMAKE_CURRENT_LIST_DIR}/src/*.cpp"
)
```

也可以不用 `GLOB_RECURSE`，手动列文件：

```cmake
set(lib1_SRC_LIST
  src/eigen3_test.cpp
)
```

两种方式对比：

| 写法 | 优点 | 缺点 |
|:---|:---|:---|
| 手动列文件 | 明确、可控 | 新增文件要手动改 CMake |
| `GLOB_RECURSE` | 模板方便，新增文件自动收集 | 文件列表不如手写直观 |

本模板为了易用，使用 `GLOB_RECURSE CONFIGURE_DEPENDS`。

## 双层变量展开

```cmake
add_library(${PREFIX}_src_lib SHARED
  ${${PREFIX}_SRC_LIST}
)
```

这里有一个容易困惑的写法：

```cmake
${${PREFIX}_SRC_LIST}
```

如果：

```cmake
PREFIX = lib1
```

那么：

```cmake
${PREFIX}_SRC_LIST
```

先变成：

```text
lib1_SRC_LIST
```

再取变量：

```cmake
${lib1_SRC_LIST}
```

最终得到源码文件列表。

## `add_library`

```cmake
add_library(${PREFIX}_src_lib SHARED
  ${${PREFIX}_SRC_LIST}
)
```

作用：创建库 target。

常见类型：

| 类型 | 产物 | 说明 |
|:---|:---|:---|
| `SHARED` | `.so` | 动态库，本模板使用 |
| `STATIC` | `.a` | 静态库 |
| `MODULE` | `.so` | 插件模块，不用于普通链接 |
| `OBJECT` | `.o` | 对象库 |
| 不写 | 由 `BUILD_SHARED_LIBS` 决定 |

如果想改成静态库：

```cmake
add_library(${PREFIX}_src_lib STATIC
  ${${PREFIX}_SRC_LIST}
)
```

如果希望用户用 preset 控制静态或动态，可以写：

```cmake
add_library(${PREFIX}_src_lib
  ${${PREFIX}_SRC_LIST}
)
```

然后在 preset 中设置：

```json
"BUILD_SHARED_LIBS": "ON"
```

## `target_include_directories`

```cmake
target_include_directories(${PREFIX}_src_lib
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/inc>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
```

作用：告诉编译器头文件在哪里。

这里使用 `PUBLIC`，因为：

1. `lib1` 自己编译时需要找到自己的头文件。
2. 链接 `lib1_src_lib` 的主程序也需要找到 `lib1/eigen3_test.hpp`。

### `BUILD_INTERFACE`

```cmake
$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/inc>
```

表示在源码构建阶段，include 路径是：

```text
src/lib1/inc
```

所以源码里可以写：

```cpp
#include "lib1/eigen3_test.hpp"
```

对应真实文件：

```text
src/lib1/inc/lib1/eigen3_test.hpp
```

### `INSTALL_INTERFACE`

```cmake
$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
```

表示安装后的 include 路径是：

```text
include
```

安装后头文件路径：

```text
install/linux-debug/include/lib1/eigen3_test.hpp
```

下游仍然可以写：

```cpp
#include "lib1/eigen3_test.hpp"
```

### 为什么头文件放 `inc/lib1/`

如果两个库都把头文件放成：

```text
inc/eigen3_test.hpp
```

主程序写：

```cpp
#include "eigen3_test.hpp"
```

就会分不清到底来自 `lib1` 还是 `lib2`。

所以模板使用：

```text
lib1/inc/lib1/eigen3_test.hpp
lib2/inc/lib2/eigen3_test.hpp
```

主程序写：

```cpp
#include "lib1/eigen3_test.hpp"
#include "lib2/eigen3_test.hpp"
```

这是一种很常见的工程写法。

## 链接公共选项和 warning

```cmake
target_link_libraries(${PREFIX}_src_lib
  PUBLIC
    project_options
  PRIVATE
    project_warnings
)
```

含义：

1. `project_options` 是库的使用要求，使用 `PUBLIC`。
2. `project_warnings` 只是编译本库时使用，使用 `PRIVATE`。

为什么 warning 不用 `PUBLIC`？

如果库未来被其他项目使用，`PUBLIC` warning 会强迫下游也使用你的 warning 设置。作为模板，`PRIVATE` 更温和。

## `Third-party dependencies`

```cmake
# ========================
# Third-party dependencies
# ========================
find_package(Eigen3 REQUIRED)

target_link_libraries(${PREFIX}_src_lib
  PUBLIC
    Eigen3::Eigen
)
```

这个区块集中写当前模块使用的第三方库。

原则：

1. 谁用第三方库，谁 `find_package`。
2. 不把所有第三方库塞到顶层。
3. 删除某个依赖时，只看这个区块就能删干净。

如果 `lib1` 用 Eigen，而 `lib2` 不用，那么只在 `lib1/CMakeLists.txt` 写 Eigen 即可。

## 安装库

```cmake
install(TARGETS ${PREFIX}_src_lib
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
```

作用：安装库文件。

不同平台、不同库类型会用到不同分类：

| 分类 | Linux 常见产物 | 目录 |
|:---|:---|:---|
| `LIBRARY` | `.so` 动态库 | `${CMAKE_INSTALL_LIBDIR}` |
| `ARCHIVE` | `.a` 静态库 | `${CMAKE_INSTALL_LIBDIR}` |
| `RUNTIME` | 可执行文件 | `${CMAKE_INSTALL_BINDIR}` |

虽然本模板只支持 Linux，但保留三类写法没有坏处，也方便以后改静态库。

## 安装头文件

```cmake
install(DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/inc/"
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
```

作用：安装整个 `inc/` 目录里的头文件。

注意路径末尾的 `/`：

```cmake
"${CMAKE_CURRENT_LIST_DIR}/inc/"
```

表示安装 `inc` 目录里面的内容，而不是安装 `inc` 目录本身。

源文件：

```text
src/lib1/inc/lib1/eigen3_test.hpp
```

安装后：

```text
install/linux-debug/include/lib1/eigen3_test.hpp
```

如果没有末尾 `/`，路径结构可能会多一层 `inc`，变成不想要的形式。

## lib2 和 lib1 的关系

`lib2/CMakeLists.txt` 和 `lib1/CMakeLists.txt` 基本一样，只是：

```cmake
set(PREFIX "lib2")
```

所以 target 变成：

```text
lib2_src_lib
```

头文件路径变成：

```text
lib2/eigen3_test.hpp
```

C++ 命名空间也要分开：

```cpp
namespace lib1 {
void run_eigen_vector_example();
}
```

```cpp
namespace lib2 {
void run_eigen_matrix_example();
}
```

避免两个库都定义全局函数：

```cpp
void main_test();
```

否则链接时可能发生符号冲突。

## 添加新库 lib3

复制 `src/lib1` 为 `src/lib3` 后，需要改这些地方：

1. `src/lib3/CMakeLists.txt`

```cmake
set(PREFIX "lib3")
```

2. 头文件目录：

```text
src/lib3/inc/lib3/xxx.hpp
```

3. C++ 命名空间：

```cpp
namespace lib3 {
}
```

4. `src/CMakeLists.txt` 添加：

```cmake
add_subdirectory(lib3)

target_link_libraries(${PROJECT_NAME}
  PRIVATE
    lib3_src_lib
)
```

实际项目中，建议把所有 `add_subdirectory` 放在一起，所有主程序链接库也放在一起，文件会更清楚。
