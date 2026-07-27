---
title: "CMakePresets与构建安装"
---

`CMakePresets.json` 用来保存常用的 CMake 配置。它解决的问题是：不要每次都手写一长串 `cmake -S ... -B ... -G ... -D...` 命令。

本模板的 `CMakePresets.json` 如下：

```json
{
  "version": 6,
  "cmakeMinimumRequired": {
    "major": 3,
    "minor": 25,
    "patch": 0
  },
  "configurePresets": [
    {
      "name": "linux-debug",
      "displayName": "Linux Debug",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/linux-debug",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug",
        "CMAKE_EXPORT_COMPILE_COMMANDS": "ON",
        "CMAKE_INSTALL_PREFIX": "${sourceDir}/install/linux-debug"
      }
    },
    {
      "name": "linux-release",
      "displayName": "Linux Release",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/linux-release",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release",
        "CMAKE_EXPORT_COMPILE_COMMANDS": "ON",
        "CMAKE_INSTALL_PREFIX": "${sourceDir}/install/linux-release"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "linux-debug",
      "configurePreset": "linux-debug"
    },
    {
      "name": "linux-release",
      "configurePreset": "linux-release"
    }
  ]
}
```

## 根字段

### `version`

```json
"version": 6
```

表示 `CMakePresets.json` 文件格式版本，不是项目版本。

可以填什么：

| 值 | 含义 |
|:---|:---|
| `1`、`2`、`3`、... | CMake preset 文件格式版本 |
| `6` | 本模板使用的版本，适合 CMake 3.25 及以上 |

注意：

1. 这里不是 `cmake_template` 的版本号。
2. 如果想兼容更老的 CMake，要降低 `version`，但某些字段可能不能再用。
3. 如果本机 CMake 很新，也不一定要用最高 preset 版本；够用即可。

### `cmakeMinimumRequired`

```json
"cmakeMinimumRequired": {
  "major": 3,
  "minor": 25,
  "patch": 0
}
```

表示读取这个 preset 文件所需的最低 CMake 版本。

可以填什么：

| 字段 | 示例 | 含义 |
|:---|:---|:---|
| `major` | `3` | 主版本号 |
| `minor` | `25` | 次版本号 |
| `patch` | `0` | 补丁版本号 |

这个模板要求 CMake >= 3.25，是因为顶层 `CMakeLists.txt` 也写了：

```cmake
cmake_minimum_required(VERSION 3.25)
```

二者最好保持一致。

## `configurePresets`

`configurePresets` 是"配置阶段"的 preset。

配置阶段对应命令：

```bash
cmake --preset linux-debug
```

它等价于告诉 CMake：

1. 使用哪个生成器。
2. 构建目录在哪里。
3. 构建类型是什么。
4. 安装目录在哪里。
5. 是否生成 `compile_commands.json`。

### `name`

```json
"name": "linux-debug"
```

preset 的机器可读名称。

可以填什么：

| 写法 | 说明 |
|:---|:---|
| `linux-debug` | 本模板 Debug preset |
| `linux-release` | 本模板 Release preset |
| `debug` | 也可以，但不如 `linux-debug` 清楚 |
| `gcc-debug` | 如果区分编译器，可以这样命名 |
| `clang-debug` | 如果添加 Clang preset，可以这样命名 |

要求：

1. 同一个数组里不能重名。
2. `buildPresets` 里的 `configurePreset` 要引用这里的名字。
3. 命令行使用的就是这个名字。

示例：

```bash
cmake --preset linux-debug
```

### `displayName`

```json
"displayName": "Linux Debug"
```

给人看的名字，主要用于 IDE 显示，例如 VSCode CMake Tools 的 preset 列表。

可以填什么：

| 写法 | 说明 |
|:---|:---|
| `Linux Debug` | 清晰 |
| `Linux Release` | 清晰 |
| `Debug` | 可以，但不区分平台 |
| `Fedora GCC Debug` | 如果 preset 很多，可以写更具体 |

这个字段不影响实际构建逻辑。

### `generator`

```json
"generator": "Ninja"
```

指定 CMake 要生成什么构建系统文件。

Linux 常见可选值：

| 值 | 说明 |
|:---|:---|
| `Ninja` | 推荐，速度快，输出干净，适合 VSCode CMake Tools |
| `Unix Makefiles` | 传统 Makefile 工作流 |
| `Ninja Multi-Config` | 多配置 Ninja，可同时管理 Debug / Release |

本模板只支持 Linux，并且使用 Ninja，所以固定写：

```json
"generator": "Ninja"
```

如果使用 `Unix Makefiles`，构建命令仍然可以用：

```bash
cmake --build --preset linux-debug
```

但底层就不是 Ninja 了。

### `binaryDir`

```json
"binaryDir": "${sourceDir}/build/linux-debug"
```

指定构建目录。

可以填什么：

| 写法 | 说明 |
|:---|:---|
| `${sourceDir}/build/linux-debug` | 推荐，构建产物留在项目内的 build 子目录 |
| `${sourceDir}/build/debug` | 也可以，但不如 `linux-debug` 清楚 |
| `/tmp/my-build` | 可以放到项目外，但不适合作为模板默认值 |

常见宏：

| 宏 | 含义 |
|:---|:---|
| `${sourceDir}` | 项目源码根目录 |
| `${presetName}` | 当前 preset 名称 |
| `${sourceParentDir}` | 源码根目录的父目录 |

也可以写成：

```json
"binaryDir": "${sourceDir}/build/${presetName}"
```

这样 `linux-debug` 会自动生成到：

```text
build/linux-debug
```

本模板显式写出目录，是为了让初学时更直观。

### `cacheVariables`

```json
"cacheVariables": {
  "CMAKE_BUILD_TYPE": "Debug",
  "CMAKE_EXPORT_COMPILE_COMMANDS": "ON",
  "CMAKE_INSTALL_PREFIX": "${sourceDir}/install/linux-debug"
}
```

`cacheVariables` 等价于命令行中的 `-D` 参数。

例如：

```json
"CMAKE_BUILD_TYPE": "Debug"
```

等价于：

```bash
-DCMAKE_BUILD_TYPE=Debug
```

## 常用 cache 变量

### `CMAKE_BUILD_TYPE`

```json
"CMAKE_BUILD_TYPE": "Debug"
```

单配置生成器中使用的构建类型。`Ninja` 和 `Unix Makefiles` 都属于常见的单配置生成器。

可以填什么：

| 值 | 作用 |
|:---|:---|
| `Debug` | 调试构建，通常带 `-g`，优化较少 |
| `Release` | 发布构建，通常开启优化 |
| `RelWithDebInfo` | 优化 + 调试信息 |
| `MinSizeRel` | 优化体积 |

常用选择：

| 场景 | 推荐 |
|:---|:---|
| 写代码、调试断点 | `Debug` |
| 发布、性能测试 | `Release` |
| 性能测试但还想保留符号 | `RelWithDebInfo` |
| 嵌入式或体积敏感 | `MinSizeRel` |

### `CMAKE_EXPORT_COMPILE_COMMANDS`

```json
"CMAKE_EXPORT_COMPILE_COMMANDS": "ON"
```

是否生成 `compile_commands.json`。

可以填什么：

| 值 | 作用 |
|:---|:---|
| `ON` | 生成 `compile_commands.json` |
| `OFF` | 不生成 |

`compile_commands.json` 记录每个源文件真实的编译命令。`clangd`、VSCode、很多静态分析工具都会用它来获得 include 路径、宏定义、编译标准等信息。

建议模板里固定开启：

```json
"CMAKE_EXPORT_COMPILE_COMMANDS": "ON"
```

#### 让 clangd 找到 `compile_commands.json`

仅仅生成 `compile_commands.json`，不代表 clangd 一定能自动找到它。

在 VSCode 中使用这种方式前，需要安装 LLVM 官方的 clangd 扩展。
CMake Tools 负责配置、构建和选择 target，clangd 则负责代码补全、
跳转定义、语义检查等编辑功能，它们不是同一个扩展。

这个模板把 Debug 构建目录设置为：

```text
build/linux-debug/
```

因此执行：

```bash
cmake --preset linux-debug
```

之后，编译数据库位于：

```text
build/linux-debug/compile_commands.json
```

clangd 默认会从当前源文件所在目录逐级向上寻找
`compile_commands.json`。它通常能找到项目根目录或 `build/` 直属目录
中的数据库，但不一定会继续搜索 `build/linux-debug/` 这样的多层
preset 目录。

如果 clangd 没有找到数据库，它会进入 fallback 模式，只用少量默认
参数分析源文件。这时即使 CMake 可以正常编译，VSCode 中仍可能出现：

1. 项目头文件显示找不到。
2. Eigen、OpenCV 等第三方库显示找不到。
3. C++ 标准识别错误，例如工程使用 C++23，clangd 却按照 C++17 分析。
4. 跳转定义、自动补全和错误提示不准确。

推荐在项目根目录创建 `.clangd`：

```yaml
CompileFlags:
  CompilationDatabase: build/linux-debug
```

目录结构会变成：

```text
.
├── .clangd
├── CMakeLists.txt
├── CMakePresets.json
├── build/
│   └── linux-debug/
│       └── compile_commands.json
└── src/
```

`CompilationDatabase` 使用相对于 `.clangd` 所在目录的路径，所以不要
写某个用户电脑上的绝对路径。这个文件只保存项目通用配置，可以提交到
Git。

注意，`.clangd` 不会生成编译数据库。第一次使用工程，或者修改了
`CMakeLists.txt`、编译选项、依赖和 preset 后，仍然要重新执行：

```bash
cmake --preset linux-debug
```

或者在 VSCode CMake Tools 中选择 `linux-debug` 并点击 Configure。

如果 `.clangd` 创建后没有立即生效，可以在 VSCode 命令面板执行：

```text
clangd: Restart language server
```

也可以关闭并重新打开当前 `.cpp` 文件。

如果同时安装了 Microsoft C/C++ 扩展，并且编辑器出现两套重复的错误
提示，可以保留该扩展提供的 GDB 调试功能，同时关闭它的 IntelliSense，
让 clangd 独立负责代码分析。这属于个人 VSCode 设置，不需要写入 CMake
模板。

#### 检查 clangd 是否真的生效

在 VSCode 中打开：

```text
查看 -> 输出 -> clangd
```

正常情况下应当看到类似内容：

```text
Loaded compilation database from .../build/linux-debug/compile_commands.json
Compile command from CDB is: ...
```

如果看到：

```text
Failed to find compilation database
command clangd fallback
```

说明 clangd 仍然没有读到编译数据库。依次检查：

1. VSCode 打开的是否为项目根目录。
2. `.clangd` 是否位于项目根目录。
3. 是否已经执行过 `cmake --preset linux-debug`。
4. `build/linux-debug/compile_commands.json` 是否真实存在。
5. `.clangd` 中的目录名是否与 `binaryDir` 完全一致。

如果系统终端中可以直接使用 `clangd`，还可以检查单个源文件：

```bash
clangd --check=src/main.cpp
```

输出中应当显示它加载了 `build/linux-debug/compile_commands.json`，
并使用 CMake 生成的 include 路径、宏定义和 C++ 标准。

#### 使用 Release 数据库

如果希望 clangd 按照 Release 配置分析，可以改成：

```yaml
CompileFlags:
  CompilationDatabase: build/linux-release
```

并先生成对应数据库：

```bash
cmake --preset linux-release
```

日常开发通常使用 `build/linux-debug` 即可。不要同时让 `.clangd` 指向
一个尚未生成或已经过期的构建目录。

### `CMAKE_INSTALL_PREFIX`

```json
"CMAKE_INSTALL_PREFIX": "${sourceDir}/install/linux-debug"
```

指定安装目录。

可以填什么：

| 写法 | 说明 |
|:---|:---|
| `${sourceDir}/install/linux-debug` | 本模板 Debug 安装目录 |
| `${sourceDir}/install/linux-release` | 本模板 Release 安装目录 |
| `/usr/local` | 系统级安装路径，需要权限，不适合作为模板默认 |
| `/opt/my_project` | 系统级或部署路径 |

本模板把安装目录放在项目内，是为了方便学习和删除。

安装后运行：

```bash
./install/linux-debug/bin/cmake_template
```

## `buildPresets`

`buildPresets` 是"构建阶段"的 preset。

构建阶段对应命令：

```bash
cmake --build --preset linux-debug
```

本模板写法：

```json
"buildPresets": [
  {
    "name": "linux-debug",
    "configurePreset": "linux-debug"
  },
  {
    "name": "linux-release",
    "configurePreset": "linux-release"
  }
]
```

### `name`

```json
"name": "linux-debug"
```

构建 preset 名称。

一般建议和对应的 configure preset 同名，这样命令比较一致：

```bash
cmake --preset linux-debug
cmake --build --preset linux-debug
```

### `configurePreset`

```json
"configurePreset": "linux-debug"
```

表示这个 build preset 使用哪个 configure preset 生成的构建目录。

可以填什么：

| 值 | 说明 |
|:---|:---|
| `linux-debug` | 使用 Debug 构建目录 |
| `linux-release` | 使用 Release 构建目录 |

必须对应 `configurePresets` 中已经存在的 `name`。

## 常见可扩展字段

初学时可以先不用，但以后可能会见到。

### `description`

给 preset 添加更长的说明。

```json
"description": "Debug build for Linux using Ninja"
```

### `hidden`

隐藏某个 preset，不直接给用户选择，通常用于继承。

```json
"hidden": true
```

### `inherits`

继承另一个 preset，减少重复。

```json
{
  "name": "linux-debug",
  "inherits": "linux-base",
  "cacheVariables": {
    "CMAKE_BUILD_TYPE": "Debug"
  }
}
```

### `environment`

设置环境变量。

```json
"environment": {
  "CC": "/usr/bin/gcc",
  "CXX": "/usr/bin/g++"
}
```

一般不建议模板默认写死编译器，除非你的项目明确要求某个工具链。

### `targets`

指定默认构建哪些 target。

```json
"targets": ["cmake_template"]
```

如果不写，默认构建全部 target。

### `jobs`

指定并行编译数量。

```json
"jobs": 8
```

不写时由底层构建工具决定。

## 为什么没有 `installPresets`

很多人会猜测可以写：

```bash
cmake --install --preset linux-debug
```

但常见 CMake preset 文件并不使用这个根字段。这个模板采用更通用的安装命令：

```bash
cmake --install build/linux-debug
```

安装命令需要传入构建目录，因为安装规则是在 configure 阶段生成到构建目录里的。

## VSCode CMake Tools 工作流

安装 VSCode 的 CMake Tools 扩展后，它会自动识别 `CMakePresets.json`。

这一节把命令行操作和 VSCode 图形界面操作对应起来。

### 打开 CMake 面板

用 VSCode 打开工程根目录后，点击左侧边栏的 `CMake`。

如果 CMake Tools 正常识别工程，你会看到 preset、Configure、Build、运行 target 等入口。

### Configure

命令行：

```bash
cmake --preset linux-debug
```

在 VSCode 图形界面中，对应的是：

1. 点击 Configure。
2. 选择 `Linux Debug`。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780705995297.webp)

这一步会读取：

```text
CMakePresets.json
CMakeLists.txt
```

并生成：

```text
build/linux-debug/
```

如果你改了 `CMakeLists.txt`、新增了源文件、修改了第三方依赖，通常需要重新 Configure。

### Build

命令行：

```bash
cmake --build --preset linux-debug
```

在 VSCode 图形界面中，CMake Tools 会在 Configure 后识别对应的 build preset。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780706149481.webp)

然后点击左下角或 CMake 面板里的 `Build` 按钮：

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780707106120.webp)

这一步会编译 `.cpp` 文件，并生成 build 目录里的可执行文件和动态库。

本模板的 Debug 可执行文件通常在：

```text
build/linux-debug/src/cmake_template
```

如果你只是修改了普通 `.cpp` 代码，一般只需要重新 Build，不一定要重新 Configure。

### 运行 build 目录里的程序

命令行：

```bash
./build/linux-debug/src/cmake_template
```

在 VSCode 图形界面中，对应的是：

1. 选择运行/调试 target。
2. 选择 `cmake_template`。
3. 点击运行按钮。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780707152235.webp)

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780707202465.webp)

运行后，终端里会看到程序输出。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780707302786.webp)

如果你要断点调试，就点击 CMake Tools 提供的 `Debug` 按钮；如果只是直接运行程序，就点击 `Launch` 按钮。

除了上面的操作入口，VSCode 底部状态栏中，`Build` 按钮旁边也有 `Debug` 和 `Launch` 快捷按钮。选择好 `cmake_template` target 并完成构建后，可以直接点击这里运行或调试程序。

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780715507074.webp)

### Install

命令行：

```bash
cmake --install build/linux-debug
```

在 VSCode 图形界面中，可以按 `Ctrl+Shift+P` 打开命令面板，然后输入：

```text
CMake: Install
```

![alt text](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/10/05/1780707509033.webp)

如果当前选择的是 `linux-debug` preset，它等同于：

```bash
cmake --install build/linux-debug
```

安装后的程序在：

```text
install/linux-debug/bin/cmake_template
```

日常写代码、运行、调试，一般直接使用 build 目录里的程序：

```text
build/linux-debug/src/cmake_template
```

只有在验证安装布局、头文件安装、动态库 RPATH 或准备发布程序时，才需要 install。

### 对应关系总结

| 你想做的事 | 命令行 | VSCode CMake Tools |
|:---|:---|:---|
| 配置工程 | `cmake --preset linux-debug` | 选择 `Linux Debug` 后点击 Configure |
| 编译工程 | `cmake --build --preset linux-debug` | 点击 Build |
| 运行 build 产物 | `./build/linux-debug/src/cmake_template` | 选择 `cmake_template` target 后点击运行按钮 |
| 调试 build 产物 | 用 GDB 调试 `build/linux-debug/src/cmake_template` | 选择 `cmake_template` target 后点击 Debug 按钮 |
| 安装工程 | `cmake --install build/linux-debug` | 命令面板执行 `CMake: Install` |

这样不需要手写 `.vscode/tasks.json`，也不需要额外准备 VSCode 调试配置文件。

## 运行与调试注意事项

CMake Tools 可以直接运行或调试当前选择的可执行 target，所以不需要手写额外的运行脚本。

如果要调试，建议安装 Microsoft C/C++ 扩展和 GDB，因为 CMake Tools 负责选择 target，真正的 C/C++ 调试器仍然需要 GDB/LLDB 支持。

如果你只改了 `.cpp` 代码，一般重新 Build 后再运行或调试即可。

如果你改了 `CMakeLists.txt`、新增源文件、改依赖，建议先 Configure，再 Build，然后再运行或调试。

### 为什么不写 `tasks.json`

旧模板常用 `.vscode/tasks.json` 写一长串命令：

```bash
cmake ..
make install
source setup.bash
run app
```

这个模板不这样做。原因是构建、运行和调试入口都可以交给 CMake Tools 管理，命令行流程则由 `CMakePresets.json` 固化。

这样职责更清楚：

| 文件 | 负责 |
|:---|:---|
| `CMakePresets.json` | configure/build 参数 |
| VSCode CMake Tools | 选择 preset、Configure、Build、运行和调试 target |
| `CMakeLists.txt` | target、依赖、安装规则 |

### 需要安装 gdb

Ubuntu/Debian：

```bash
sudo apt install gdb
```

Fedora：

```bash
sudo dnf install gdb
```

## 常见错误

### 忘记安装 Ninja

报错可能类似：

```text
CMake was unable to find a build program corresponding to "Ninja"
```

解决：

```bash
sudo apt install ninja-build
```

或：

```bash
sudo dnf install ninja-build
```

### preset 名称写错

错误命令：

```bash
cmake --preset debug
```

如果文件里没有 `debug` 这个 preset，就会失败。

查看可用 preset：

```bash
cmake --list-presets
```

### build 前没有 configure

如果第一次直接运行：

```bash
cmake --build --preset linux-debug
```

可能会因为构建目录还没有生成而失败。

正确顺序：

```bash
cmake --preset linux-debug
cmake --build --preset linux-debug
```
