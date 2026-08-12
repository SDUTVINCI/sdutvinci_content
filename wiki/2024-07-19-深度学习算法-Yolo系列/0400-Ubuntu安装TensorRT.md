---
title: 在Ubuntu环境下通过tar包方式安装TensorRT
authors:
- 崔桐汇
contributors:
- shangfanxing
publishedAt: '2024-07-19T00:00:00.000Z'
updatedAt: '2024-08-21T12:30:46.000Z'
---

TensorRT是NVIDIA开发的一种高性能深度学习推理库，能够优化和加速基于NVIDIA GPU的推理过程。以下是如何在Ubuntu上通过tar包方式安装TensorRT的步骤。



## **前提条件**



在开始之前，请确保你满足以下前提条件：



1. 已安装支持TensorRT的NVIDIA GPU。

2. 已安装正确版本的CUDA和cuDNN。可以通过以下命令检查CUDA版本：

    ```Bash
    nvcc --version
    ```

3. 已安装相应版本的NVIDIA驱动程序。



## **步骤 1：下载TensorRT tar包**



4. 访问 [NVIDIA TensorRT 下载页面](https://developer.nvidia.com/tensorrt)。

5. 登录你的NVIDIA账户。如果没有账户，需要注册一个。

6. 在下载页面选择适用于你的CUDA版本和操作系统（Linux）的TensorRT tar包版本。

7. 下载对应的tar包文件到你的本地计算机。



## **步骤 2：解压TensorRT tar包**



8. 打开终端。

9. 导航到下载TensorRT tar包的目录：

    ```Bash
    cd /path/to/your/downloaded/tar
    ```

10. 使用以下命令解压tar包（假设文件名为`TensorRT-<version>-Linux.tar.gz`）：

    ```Bash
    tar -xzvf TensorRT-<version>-Linux.tar.gz
    ```



解压后的文件夹将包含以下内容：

- `bin`: 可执行文件

- `include`: 头文件

- `lib`: 库文件

- `python`: Python绑定

- `samples`: 示例代码

- `doc`: 文档



## **步骤 3：设置环境变量**



为了让系统正确找到TensorRT库文件，你需要将其路径添加到环境变量中。



11. 打开终端并编辑你的bash配置文件（例如`.bashrc`或`.bash_profile`）：

    ```Bash
    vim ~/.bashrc
    ```

12. 在文件末尾添加以下行（假设TensorRT解压目录为`/path/to/TensorRT`）：

    ```Bash
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/TensorRT/lib
    export CPATH=$CPATH:/path/to/TensorRT/include
    export LIBRARY_PATH=$LIBRARY_PATH:/path/to/TensorRT/lib
    ```

13. 保存并关闭文件，然后通过以下命令使更改生效：

    ```Bash
    source ~/.bashrc
    ```



## **步骤 4：安装Python绑定（可选）**



如果你计划使用Python进行TensorRT开发，可以安装Python绑定。



14. 导航到TensorRT解压目录的Python文件夹：

    ```Bash
    cd /path/to/TensorRT/python
    ```

15. 使用pip安装对应Python版本的TensorRT绑定（假设为Python 3\.8）：

    ```Bash
    pip install tensorrt-<version>-cp38-none-linux_x86_64.whl
    ```



你也可以选择安装`uff`、`graphsurgeon`和`onnx-graphsurgeon`，这些工具通常用于模型转换和优化：

```Bash
pip install uff-<version>-py2.py3-none-any.whl
pip install graphsurgeon-<version>-py2.py3-none-any.whl
pip install onnx_graphsurgeon-<version>-py2.py3-none-any.whl
```



## **步骤 5：验证安装**



16. 打开终端并输入以下命令检查TensorRT库是否正确安装：

    ```Bash
    dpkg -l | grep nvinfer
    ```



17. 你还可以运行TensorRT提供的示例程序来验证安装。例如：

    ```Bash
    cd /path/to/TensorRT/samples/sampleMNIST
    make
    ./sample_mnist
    ```



这将编译并运行一个简单的MNIST模型推理示例，如果一切正常，你将看到推理结果输出。
