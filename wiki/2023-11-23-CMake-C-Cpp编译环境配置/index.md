---
vinciId: 2ef96f33-b6d7-4fd7-83ed-e8a134e3d210
title: CMake C/C++编译环境配置
description: 参考资料
authors:
  - dongjiahui
contributors:
  - sulihao
  - shangfanxing
  - fangzihao
  - cuigongyan
  - cuitonghui
publishedAt: 2023-11-23T00:00:00.000Z
updatedAt: 2026-05-16T02:48:46.000Z
---

## 参考资料

[https://gitee.com/unlimited13/cpp/blob/master/cmake/cmake.md]()

【基于VSCode和CMake实现C/C\+\+开发 \| Linux篇\-哔哩哔哩】

[https://www.bilibili.com/video/BV1fy4y1b7TC]()

[https://xbing.notion.site/VSCode-CMake-C-C-Linux-c330a94669a84c2480a59ba708fd4ece]()

## **Vinci机器人队\_CMake工程标准:**

### 标准式

#### 仓库链接

[https://github.com/tungchiahui/Cmake_Template]()

#### 详解CMake命令

\(肝不动了，看github里边的readme和注释吧\)





### 工作空间式

#### 仓库链接

\(没必要自己搞，因为ROS2有非常成熟的工作空间模板，可以参考colcon，使用colcon当做工程模板\)

[https://colcon.readthedocs.io/en/released/index.html#]()

[基础视觉算法\-OpenCV实现](/wiki/2026-04-13-opencv-jiao-cheng)这整个教程都是拿colcon当工程模板的，可以参考参考怎么用的。





#### **配置json文件并调试项目（没完全敲完，不要看）**

首先，先把C/C\+\+插件退回到1\.8\.4版本（因为最新版有可能无法正常生成launch\.json）

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/001-57d4f970da21.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/002-be980feea526.webp)

这个Program是我们要调试的可执行文件的绝对路径。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/003-cdce4fd84092.webp)

这个prelaunchtask是我们执行前要做的任务

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/004-b577c6ac4fb7.webp)

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/005-c380228a6a2e.webp)

注释掉prelaunchtask，然后随便设置一个断点，按F5进行调试

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/006-4726327d7592.webp)

发现正常命中了断点。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/007-0a8926e3101c.webp)

然后下面可以正常进行debug的监视和程序的正常调试。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/008-9adda544f602.webp)

配置task\.json文件

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/009-40774556a5af.webp)

label是接口，标签的意思。然后给label命名，最后在dependsOn里写入这两个label。

```JSON
{
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build"    //需要进入到我们执行tasks任务的文件夹中
    },
    "tasks": [    //tasks包含三个小任务
        {
            "type": "shell",
            "label": "cmake",    //第一个任务的名字叫cmake
            "command": "cmake",    //它要执行的命令是cmake
            "args": [
                ".."    //参数是..
            ]
        },
        {
            "label": "make",    //第二个任务的名字叫make
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "make",    //它要执行的命令是make
            "args": [

            ]
        },
        {
            "label": "Build",    //第三个任务的名字叫Build
            "dependsOrder": "sequence",    //顺序执行依赖项
            "dependsOn":[    //依赖的两个项为cmake和make
                "cmake",    //即第一个任务的label
                "make"      //即第二个任务的label
            ]
        }
    ]
}

```

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/010-deaf7e0d43f3.webp)

launch文件中把prelaunchtask修改为task\.json里的build。

```JSON
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++ - 生成和调试活动文件",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/bin/main",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "将反汇编风格设置为 Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "Build",
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```

这样的话，我们的launch\.json和task\.json就配置好了。



为了能够输出cmake和make的log，我们进阶，先将cmake的log输出一下。

![image\.png](https://cdn.sdutvincirobot.top/site-assets/images/wiki/2023/11/23/011-79afecc922bf.webp)

```JSON
{
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build",    //需要进入到我们执行tasks任务的文件夹中
    },
    "tasks": [    //tasks包含三个小任务
        {
            "type": "shell",
            "label": "cmake",    //第一个任务的名字叫cmake
            "command": "cmake",    //它要执行的命令是cmake
            "args": [
                "..",    //参数是..
                ">>",
                "../log/cmake.log",    //这里是为了输出log    log的命令为 cmake .. >> ../log/log.txt
                "2>&1"    //将其他错误信息也输出
            ],
            "problemMatcher": []    //这里需要添加一个空的问题匹配器，否则会报错
        },
        {
            "label": "make",    //第二个任务的名字叫make
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "make",    //它要执行的命令是make
            // "args": [
            //  ">>",
            //  "../log/make_build.log",
            //  "2>&1"
            // ],
            "problemMatcher": [],    //这里也需要添加一个空的问题匹配器，否则会报错
        },
        {
            "label": "Build",    //第三个任务的名字叫Build
            "dependsOrder": "sequence",    //顺序执行依赖项
            "dependsOn":[    //依赖的两个项为cmake和make
                "cmake",    //即第一个任务的label
                "make"      //即第二个任务的label
            ]
        }
    ]
}

```





## 常用库的配置

### Eigen3矩阵库

```CMake
# 引用第三方库(例如引用Eigen3矩阵库)
# 查找 库 的包
find_package(Eigen3 REQUIRED)
# 创建一个INTERFACE库(创建了一个不包含源代码的目标，专门用于传递编译选项、包含路径和链接库。)
add_library(eigen3_lib INTERFACE)
# 查找 库 头文件
target_include_directories(eigen3_lib INTERFACE ${EIGEN3_INCLUDE_DIRS})
# 将 库 链接给目标
target_link_libraries(${PREFIX}_src_lib PUBLIC eigen3_lib)
# 提示是否找到 库
message(STATUS "Eigen3 include dirs: ${EIGEN3_INCLUDE_DIRS}")
```

### OpenCV4

```cmake
# 引用第三方库(例如引用OpenCV4库)
# 查找 库 的包
find_package(OpenCV REQUIRED)
# 创建一个INTERFACE库(创建了一个不包含源代码的目标，专门用于传递编译选项、包含路径和链接库。)
add_library(opencv_lib INTERFACE)
# 链接 库
target_link_libraries(opencv_lib INTERFACE ${OpenCV_LIBS})
# 查找 库 头文件
target_include_directories(opencv_lib INTERFACE ${OpenCV_INCLUDE_DIRS})
# 将 库 链接给目标
target_link_libraries(${PREFIX}_src_lib PUBLIC opencv_lib)
# 提示是否找到 库
message(STATUS "OpenCV library status:")
message(STATUS "> version: ${OpenCV_VERSION}")
message(STATUS "> include: ${OpenCV_INCLUDE_DIRS}")
message(STATUS "> libraries: ${OpenCV_LIBS}")
```



### QT5\(未测试\)

```cmake
# 引用 Qt5 库（假设需要 Core、Gui、Widgets 模块）
find_package(Qt5 COMPONENTS Core Gui Widgets OpenGL Tools REQUIRED)

# 设置 UIC 查找路径为 form 目录
set(CMAKE_AUTOUIC_SEARCH_PATHS "${CMAKE_CURRENT_LIST_DIR}/form")

# 查找 src 目录及其子目录中的所有.ui 文件添加到列表(获取的是完整的文件绝对路径)
file(GLOB ${PREFIX}_UI_FILES "${CMAKE_CURRENT_LIST_DIR}/form/*.ui")

# 生成 UI 头文件（自动生成 ui_*.h）
qt5_wrap_ui(${PREFIX}_UI_HEADERS ${${PREFIX}_UI_FILES})
# 查看.ui是否被转化
message(STATUS "UI Files: ${${PREFIX}_UI_FILES}")
message(STATUS "Generated UI Headers: ${${PREFIX}_UI_HEADERS}")

# 添加库 将${SRC_LIST}中的库全部创建为src_lib动态库
add_library(${PREFIX}_src_lib SHARED
    ${${PREFIX}_SRC_LIST}
    ${${PREFIX}_UI_HEADERS}  # 必须包含生成的 UI 头文件
    )


# 创建唯一命名的 INTERFACE 库（例如前缀_qt5_lib）
add_library(${PREFIX}_qt5_lib INTERFACE)
# 链接 Qt5 库到 INTERFACE 目标（注意使用 Qt5:: 命名空间）
target_link_libraries(${PREFIX}_qt5_lib INTERFACE
    Qt5::Core
    Qt5::Gui
    Qt5::Widgets
    Qt5::OpenGL
)
# 让动态库能够找到头文件（特别是 Qt5 生成的 `ui_*.h` 头文件）
target_include_directories(${PREFIX}_src_lib PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}  # Qt5 生成的 `ui_*.h` 头文件会放这里
)
# 将 Qt5 库链接到当前模块的动态库
target_link_libraries(${PREFIX}_src_lib PUBLIC ${PREFIX}_qt5_lib)
# 输出信息
message(STATUS "Qt5 library status:")
message(STATUS "> version: ${Qt5_VERSION}")
```

在 **顶层 CMakeLists\.txt** 添加：

```CMake
# 启用 MOC/UIC/RCC 自动化
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)
```



### QT6

```bash
# 引用 Qt6 库（假设需要 Core、Gui、Widgets 模块）
find_package(Qt6 COMPONENTS Core Gui Widgets OpenGL REQUIRED)

# 设置 UIC 查找路径为 form 目录
set(CMAKE_AUTOUIC_SEARCH_PATHS "${CMAKE_CURRENT_LIST_DIR}/form")

# 查找 src 目录及其子目录中的所有.ui 文件添加到列表(获取的是完整的文件绝对路径)
file(GLOB ${PREFIX}_UI_FILES "${CMAKE_CURRENT_LIST_DIR}/form/*.ui")

# 生成 UI 头文件（自动生成 ui_*.h）
qt6_wrap_ui(${PREFIX}_UI_HEADERS ${${PREFIX}_UI_FILES})
# 查看.ui是否被转化
message(STATUS "UI Files: ${${PREFIX}_UI_FILES}")
message(STATUS "Generated UI Headers: ${${PREFIX}_UI_HEADERS}")

# 添加库 将${SRC_LIST}中的库全部创建为src_lib动态库
add_library(${PREFIX}_src_lib SHARED
    ${${PREFIX}_SRC_LIST}
    ${${PREFIX}_UI_HEADERS}  # 必须包含生成的 UI 头文件
    )



# 创建唯一命名的 INTERFACE 库（例如前缀_qt6_lib）
add_library(${PREFIX}_qt6_lib INTERFACE)
# 链接 Qt6 库到 INTERFACE 目标（注意使用 Qt6:: 命名空间）
target_link_libraries(${PREFIX}_qt6_lib INTERFACE
    Qt6::Core
    Qt6::Gui
    Qt6::Widgets
    Qt6::OpenGL
)
# 让动态库能够找到头文件（特别是 Qt6 生成的 `ui_*.h` 头文件）
target_include_directories(${PREFIX}_src_lib PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}  # Qt6 生成的 `ui_*.h` 头文件会放这里
)
# 将 Qt6 库链接到当前模块的动态库
target_link_libraries(${PREFIX}_src_lib PUBLIC ${PREFIX}_qt6_lib)
# 输出信息
message(STATUS "Qt6 library status:")
message(STATUS "> version: ${Qt6_VERSION}")
```

在 **顶层 CMakeLists\.txt** 添加：

```CMake
# 启用 MOC/UIC/RCC 自动化
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)
```

我这里有个配置好的QT6环境，你可以clone下来使用。

[https://github.com/tungchiahui/QT_Projects/tree/main/QT6/QT6_Template]()























Windows配置

[https://www.bilibili.com/video/BV13K411M78v]()

[https://xbing.notion.site/VSCode-CMake-C-C-Windows-a5285b44a76f450f9c2907d852e78377]()

配置json

*launch\.json* \-\- for debug

作用：配置调试信息，用来调试编译好的文件：

program：可执行文件的路径；

preLaunchTask：执行调试前所执行的task

*tasks\.json* \-\- for build before debug

作用：包含调试前的操作指令，用来做调试前的编译工作

可以避免每次修改代码后，手动编译；即tasks\.json其实是和手动编译的作用等价的。

tasks\.json包含了某个task的编译命令: 编译代码，并生成可执行文件。

label 应与launch\.json中的preLaunchTask名字一致

视频教程中给出的CMake工程的tasks\.json

```JSON
{
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build"
    },
    "tasks": [
        {
            "type": "shell",
            "label": "cmake",
            "command": "cmake",
            "args": [
                ".."
            ]
        },
        {
            "label": "make",
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "mingw32-make.exe",
            "args": [

            ]
        },
        {
            "label": "Build",
            "dependsOn":[
                "cmake",
                "make"
            ]
        }
    ],

}
```
