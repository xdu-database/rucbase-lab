# Rucbase使用指南

<!-- START doctoc generated TOC please keep comment here to allow auto update -->

<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Rucbase使用指南](#rucbase使用指南)
  - [环境配置](#环境配置)
  - [项目下载](#项目下载)
    - [GoogleTest子模块安装](#googletest子模块安装)
  - [编译](#编译)
  - [测试单元](#测试单元)
  - [基本结构](#基本结构)


<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 环境配置

Rucbase需要以下依赖环境库配置：

- gcc 7.1及以上版本（要求完全支持C++17）
- cmake 3.16及以上版本
- flex
- bison
- readline

可以通过命令完成环境配置(以Debian/Ubuntu-apt为例)

```bash
sudo apt-get install build-essential  # build-essential packages, including gcc, g++, make and so on
sudo apt-get install cmake            # cmake package
sudo apt-get install flex bison       # flex & bison packages
sudo apt-get install libreadline-dev  # readline package
```

可以通过`cmake --version`命令来查看cmake版本，如果低于3.16，需要在官网下载3.16以上的版本并解压，手动进行安装。

注意,在CentOS下,编译时可能存在头文件冲突的问题,我们不建议你使用Ubuntu以外的操作系统,你可以向助教获取帮助

## 项目下载

你可以通过以下方式来完成Rucbase及其子模块的下载

```bash
git clone --recursive https://github.com/xdu-dabatase/rucbase-lab.git
```

### GoogleTest子模块安装

当本项目及子模块下载成功之后，你需要进行GoogleTest子模块的安装，具体命令如下：

```bash
cd deps
cd googletest
mkdir build
cd build
cmake ..
make
sudo make install
```

[GoogleTest框架简介](https://www.cnblogs.com/jycboy/p/6057677.html)

## 编译

为了能够对学生完成的代码进行测试，需要生成测试脚本的编译文件

```bash
mkdir build # 位于rucbase-lab目录下
cd build 
cmake .. [-DCMAKE_BUILD_TYPE=Debug]|[-DCMAKE_BUILD_TYPE=Release]
```


## 测试单元

GoogleTest框架测试

本次实验包含以下模块测试：

- 存储模块：

  - disk_manager_test
  
  - lru_replacer_test
  
  - clock_replacer_test (选做)
  
  - buffer_pool_manager_test
  
  - rm_gtest

- 索引模块：
  
  - b_plus_tree_insert_test
  - b_plus_tree_delete_test
  - b_plus_tree_concurrent_test


以lru_replacer_test为例，可以通过以下命令进行测试：

```bash
cd build   #位于rucbase-lab目录下
make lru_replacer_test
./bin/lru_replacer_test
```

## 基本结构

+ rucbase_client: 客户端源代码，无需修改

+ src: 服务端源代码
  
  + Parser: 将原始SQL语句转换为抽象语法树AST, 由ExecutionManager进一步解释和执行。
  
  + storage: 存储层, 由自己实现的BufferPoolManager和*unix提供接口的磁盘文件系统组成, 暴露给上层的单位为Page
  
  + replacer: 缓冲区替换算法
  
  + record : 管理存储了无序记录的Page, 对Page进行Tuple级操作
  
  + index : 管理存储在记录文件中的无序数据记录的持久索引
  
  + system : 处理DDL语句, 负责跟踪 创建/删除 表/索引
  
  + execution : 执行模块, 负责DML语句 查询计划树的生成与执行
  
  + transaction : 事务模块，提供事务的Begin/Commit/Abort接口, LockManager提供事务相关的锁
  
  + recovery : 日志模块，WAL算法，负责日志管理和系统恢复，目前只支持DML语句恢复

![Architecture](../pics/architecture_fixed.jpg)

本次实验只涉及storage、replacer、record和index部分。
