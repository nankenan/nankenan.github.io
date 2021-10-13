---
layout:     post
title:      "Unity3D安装AirSim以及相关问题解决"
subtitle:   "AirSim"
date:       2021-10-12 14:00:00
author:     "sword.zhang"
header-img: "img/home-bg.jpg"
tags:
    - AirSim
---

## 一、下载和环境搭建

### 1、安装Unity3D，我安装的是2021.1.15f。

![image-20211012143011679](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211012143011679.png)

### 2、安装Visual Studio，我安装的是2019版。记住要勾选 C++。

![image-20211012142956783](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211012142956783.png)



### 3、GitHub上克隆AirSim的代码仓库。

链接地址为：https://github.com/microsoft/AirSim；

克隆下来之后，找到Unity文件夹，下面有两个工程。

一个c++工程：**AirLibWrapper**

一个是Unity3D工程：**UnityDemo**

首先，我们打开AirsimWrapper，双击AirsimWrapper.sln 即可打开工程。

我这边直接运行会失败，检查了一下，主要是缺少两个库矩阵处理库**Eigen** 和 RPC库 **RPClib**。

**Eigen**安装：

进入Eigen官网:http://eigen.tuxfamily.org/index.php?title=Main_Page#Download

![image-20211012144043142](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211012144043142.png)

我选择了最新的Eigen 3.4.0 released 版本，下载并且解压。

打开 AirSim -> AirLib文件夹，新建一个名为 deps 的文件夹。

![image-20211012144304262](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211012144304262.png)

打开deps文件夹，将解压完的Eigen 文件夹拷贝到 deps 文件夹下，并且重命名为：eigen3

![image-20211012144417148](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211012144417148.png)



当然，也可以不重名，更改此处的附加包含目录 也可以。![image-20211013103909432](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013103909432.png)

**RPClib**的安装：

GitHub仓库地址为：https://github.com/rpclib/rpclib

克隆下来，并且rpclib文件夹拷贝到上面的deps文件夹中。

![image-20211012145441344](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211012145441344.png)



这个工程还需要编译一下，我们需要生成一个 rpc.lib.

但是这个工程没有sln解决方案文件，我们需要通过cmake生成，去cmake官网https://cmake.org/download/ 下载一个最新的cmake，

![image-20211013103002809](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013103002809.png)

 安装后打开即可。

如下图，我们在deps/rpclib 下面新建一个 build 文件夹，将输出地址放在此处。



![image-20211013103234013](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013103234013.png)

然后依次点击config、Generate、Open Project。



![image-20211013103331464](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013103331464.png)

此时打开了一个RPC的工程，不要犹豫，右键ALL_BUILD，点击重新生成，就会得到**rpc.lib** 了。



![image-20211013103538415](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013103538415.png)

由于AirSimWrapper 工程下依赖的rpc.lib 路径为：deps\rpclib\lib\$(Platform)\$(Configuration)，我们使用的是x64 下的 Release ， 即deps\rpclib\lib\x64\Release。

所有我们要么改该工程的依赖地址，我们新建文件夹 deps\rpclib\lib\x64\Release ，然后把 rpc.lib 放入该文件夹。



### 4、生成 AirsimWrapper.dll

双加build.cmd 即可。



![image-20211013104549554](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013104549554.png)

成功的话，会在Unity3D的目录下生成 一个AirsimWrapper.dll。



![image-20211013104705837](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013104705837.png)

如果没有该dll,建议 AirsimWrapper 工程下 重新生成 ，看报啥错，再逐步解决就好。



# 二、运行Unity3D demo.

打开场景文件夹，选择 SimModeSelector ，运行。

![image-20211013105713323](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013105713323.png)

这时我们可以选择 Car 或者 Drone Mode.

![image-20211013105920427](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013105920427.png)



打开Car 是酱紫的。

![image-20211013110003807](D:\SoftProject\GitHub\nankenan.github.io\img\AirsimSetup\image-20211013110003807.png)



按住WASD 就可以愉快的玩耍了。