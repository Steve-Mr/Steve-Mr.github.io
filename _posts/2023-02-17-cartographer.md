---
layout: post
title: "在 Ubuntu 20.04 上使用 Cartographer 建图"
date: 2023-02-17 22:00:00 +0800
sitemap:
  lastmod: 
toc: true 
excerpt_separator: <!--more-->
tags:
  - ROS
  - Cartographer
---

Cartographer 是 Google 开发的一款实时同步定位与建图 (SLAM) 库，有不错的性能。关于 Cartographer 的特点不赘述了，这方面的文章不少了。   

这篇文章关注的问题是在 ROS Noetic 上（准确来说是在 Ubuntu 20.04 上）要如何使用 Cartographer 的问题：官方文档虽然给出了安装方法，但是很容易出现关于 `abseil` 库的问题。  

不过这篇是从之前的文档里翻出来并整理的，质量上确实差些。（甚至也无法确定是否现在仍然可用，只能说提供一种可能的思路。）另外这篇文章中可能存在一些时效性的问题，需要自己根据实际情况调整。

<!--more-->

## 准备工作

新建工作空间文件夹（本文档将使用 `carto_ws`，名称可自由选择），`git clone` `cartographer` 和 `cartographer_ros` 到工作空间的 `src` 文件夹下。  

安装编译需要的工具：  

```sh
sudo apt-get update
sudo apt-get install -y python3-wstool python3-rosdep ninja-build libgmock-dev
```

## 编译 Abseil

1. 克隆 Abseil 仓库并且选取 Cartographer 需要的版本。  
  首先还是需要选定要克隆的位置，这里位置随意选取。  
  ```sh 
  git clone https://github.com/abseil/abseil-cpp.git
  cd abseil-cpp
  git checkout 215105818dfde3174fe799600bb0f3cae233d0bf # version 20211102.0 which is specified in cartographer/scripts/install_abseil.sh
  ```

2. 编译和安装  
  我们需要一个 `build` 文件夹，因此下面第一步就是创建文件夹并且 cd 进去。  
  ```sh
  mkdir build && cd build
  cmake -DABSL_BUILD_TESTING=ON -DABSL_USE_GOOGLETEST_HEAD=ON -DCMAKE_POSITION_INDEPENDENT_CODE=ON -DCMAKE_CXX_STANDARD=17 ..
  cmake --build . --target all
  sudo make install
  ```

## 安装 Cartographer

### 一些（可能不必要）的修改

- 必须进行的修改

    在 cartographer 文件夹下删除 `package.xml` 中的 `libabsl-dev` 这一行。

- 可能不是必须的修改

    1. 在 `cartographer` 文件夹下的 `CMakeLists.txt` 顶部增加 `set(CMAKE_CXX_STANDARD 17)`
    2. 在 `cartographer_ros` 文件夹下的 `CMakeLists.txt` 顶部增加 `set(CMAKE_CXX_STANDARD 17)` 和 `set(CMAKE_POSITION_INDEPENDENT_CODE ON)`
    3. 在 `cartographer_ros` 中的 `cartographer_rviz` 中的 `CMakeLists.txt` 中增加 `set(CMAKE_POSITION_INDEPENDENT_CODE ON)` 和 `add_compile_options(-fPIC)`

### 安装依赖

在当前工作空间的根目录（此环境下为 `~/carto_ws`）执行下面的代码，安装 cartographer 有关包指定的依赖。  

```sh
sudo rosdep init 
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
```

### 执行编译

同样在当前工作空间的根目录：  

```sh
catkin_make_isolated --install --use-ninja
```

需要注意的是使用 catkin_make_isolated 命令编译后，首先需要使用 ```source install_isolated/setup.bash``` 命令来加载环境，其次**对 src 文件夹任何内容进行修改后都需要重新编译**，即使用 ```catkin_make_isolated --install --use-ninja``` 命令，包括对 launch 文件的修改。  

## 建图 Demo

首先加载环境：  

```sh
source install_isolated/setup.bash
```

然后进行建图，注意使用时将 bag 地址修改为本机上的地址（这里使用了 ```'/home/usr/test.bag```）：  

```sh
roslaunch cartographer_ros demo_my_robot.launch bag_filename:='/home/usr/test.bag'
```

当然具体的参数有很多，既需要啃官方文档也需要自己不断的尝试才能逐渐找到合适的设置。  
最后，happy coding!
