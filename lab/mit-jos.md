# jos lab简介及环境配置

## jos lab介绍

mit6.828在2019年发生了改革，lab变成了riscv下的xv6，所以我们采用2018年的x86架构下的[jos lab](https://pdos.csail.mit.edu/6.828/2018/labs/lab1/)。jos共有7个lab，每一个lab聚焦一个主题：
   - Lab 1: Booting a PC
   - Lab 2: Memory Management
   - Lab 3: User Environments
   - Lab 4: Preemptive Multitasking
   - Lab 5: File system, Spawn and Shell
   - Lab 6: Network Driver (default final project)
   - Lab 7: Final JOS project  

每一个lab中有多个exercises，分为编程题和主观题。我们做前4个lab，每一个lab最后需要提交一个压缩包，压缩包由一个pdf格式的实验报告和这个lab下代码题目的具体代码组成，压缩包命名为**学号+姓名+jos+labN.zip**。

实验报告的命名方式是**学号+姓名+jos+labN.pdf**。实验报告的具体内容如下：编程题目提交到评测系统后，会有一个分数，在文档中需要有这个打分的截图；主观题如果是有具体问题的话，需要大家根据情况用图文解答，比如lab1 exercise 2，如果是让大家阅读一些材料，可以略过，比如lab1 exercise 1。

有代码题的话，需要用git diff命令导出每道题目当前lab起始commit修改的内容，导出代码命名为**学号+姓名+jos+labN+exerciseM**，导出命令为“git diff origin/labN >labN-exerciseM”。

## jos lab due

|lab|due|
|--|--|
| lab1 | 2022.10.14 |
| lab2 | 2022.11.04 |
| lab3 | 2022.11.25 |
| lab4 | 2022.12.16 |
| 面试回答 | 待定 |

## 环境配置

开发推荐在Unix系列下进行（Linux、Mac），这些环境下工具链安装简单，教程比较多。开发环境选择如下：
1. Docker（**已经有一个配置好的docker [mit-jos镜像](https://hub.docker.com/repository/docker/l543306408/mit-6.8240jos)，大家只需要下载后运行即可**）
2. VMware，virtual box，可以采用vagrant管理和配置
3. 云服务器，学生有优惠
4. Linux、Mac裸机

推荐新手在前3个选项里面选一个，配置相对简单，不会扰乱自己的主机环境，误操作后可能有可以挽救的机会。

选择好开发环境后，就要开始配置具体的内容了。主要是配置编译工具链和打补丁的qemu。
不想自己配置的，可以直接运行我们提供的docker [mit-jos镜像](https://hub.docker.com/repository/docker/l543306408/mit-6.8240jos)。jos lab目录在/data/mit-6.824-labs/lab。
如果想要自己体验配置环境的“快乐”，这里提供一个镜像制作的dockerfile，可以从中参考配置环境的步骤。
需要注意的是如果上一步选择在裸机上开发，而且找不到编译好的库，需要自己编译开发链。 

``` docker
FROM ubuntu:20.04

RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list && \
        sed -i 's/security.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list

RUN apt-get update -y && DEBIAN_FRONTEND=noninteractive TZ=Asia/Shanghai apt-get install -y tzdata man git vim curl wget gcc-multilib gdb lsof net-tools build-essential make sudo python python3 pkg-config zlib1g-dev libsdl1.2-dev libtool-bin libglib2.0-dev libz-dev libpixman-1-dev

WORKDIR /data/mit-6.824-labs

# RUN git clone https://github.com/mit-pdos/6.828-qemu.git qemu && cd qemu && ./configure --disable-kvm --disable-werror --target-list="i386-softmmu x86_64-softmmu" && make -j && make install

# RUN git clone https://pdos.csail.mit.edu/6.828/2018/jos.git lab

CMD ["/bin/bash"]
```

自己配置过程中，有以下注意事项：
1. qemu编译可能会报错，cd qemu后，需要先把“#include <sys/sysmacros.h>”添加到“qga/commands-posix.c”，然后再执行“./configure...”及之后命令
2. github的连接日常波动，可以考虑镜像等多种解决方法
3. 自己编译工具链的话，可以参考[官网教程](https://pdos.csail.mit.edu/6.828/2018/tools.html)，但是要注意和已有工具链的区分，不要搞崩自己的环境，make install后如果有问题清理起来比较麻烦