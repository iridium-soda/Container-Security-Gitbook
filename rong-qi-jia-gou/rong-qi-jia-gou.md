---
description: Still Unchecked.
---

# 容器架构

## 容器的沿革发展

虚拟化容器技术最早可以追溯到上世纪70年代末。随着硬件的发展和需求的变化，容器化技术和思想也在不断演进和革新。

### chroot：最早的容器化思想

chroot最早出现在Unix V7中。当时的系统开发十分复杂，因此急需一个**高效稳定可重建**的测试环境，因为程序编译调试之后，测试环境就被“污染”了，下一次构建测试需要重新安装和配置环境。而以当时的计算资源，不断新建和销毁测试环境十分困难。

于是chroot应运而生。该功能为每个进程提供一个独立的磁盘空间，将一个进程及其子进程的根目录改变到文件系统中的新位置，让这些进程只能访问到该目录。这可以粗略理解为将机器的根目录切换到当前目录上（所以叫chroot）。这项技术被称为 Chroot Jail。这是最早的进程隔离技术，可以通过切换根目录等方法限制进程的文件访问权限。

### FreeBSD Jails

2000年发布的FreeBSD系统附带了FreeBSD Jail组件作为系统管理员用来增强FreeBSD系统安全性的工具。该组件扩展了chroot的概念和作用范围，创建了一个独立于系统其余部分的安全环境。在jail内的应用程序不能访问外部的文件资源，从而实现了隔离，破坏在jail中运行的服务不会损害整个系统。Jails 通过虚拟化对文件系统、用户和网络子系统的访问，改进了chroot技术。

![FreeBSD Jail的logo](<../.gitbook/assets/freeBSD Jail>)

### LinuxVServer的隔离机制

Linux VServer于2001年发布，并且使用了和FreeBSD Jail一致的技术，可以对计算机系统上的物理资源和系统资源，比如文件系统，网络地址，内存等进行分区。这种分区就称为安全上下文。安全上下文中的虚拟操作系统叫做虚拟私有服务器，即VPS。

### Process Containers和CGroup

Process Containers由 Google 在 2006 年推出，用于管理和限制隔离环境下资源的使用。2007年，为了避免和Linux内核中的Container混淆，改名为Control Groups并且合并到Linux的2.6.24版本内核中。

### Docker

2013年发布的Docker是容器技术的一次革命性飞跃。Docker最初由dotCloud公司开发并维护，于2013年3月以Apache协议开源并在Github上维护。

Docker开源后受到了广泛的关注和讨论， 越来越多的互联网企业开始向其贡献代码并参与修补。包括Google在内的一系列互联网厂家都将Docker整合进自己的产品。

Docker最初是基于Ubuntu 12.04LTS，并使用新兴的go语言实现的。Docker基于上文提到的cgroup和namespace技术对进程进行封装和资源隔离，属于操作系统层面的虚拟化技术。底层部分起初是基于LXC实现的，0.7版本以后开始移除LXC转而使用libcontainer。从1.11开始则使用runC和containerd作为底层驱动。

Docker是容器技术的一次飞跃，通过对容器进一步的封装和文件系统、网络资源等的虚拟化，极大地简化了容器的新建、部署、管理、维护和销毁。从此容器技术被更加广泛地使用，并且比虚拟机技术拥有更加优越的性能。

### Windows Containers

2015年，微软在Windows Server 2015上添加了基于该系统的容器支持，称为Windows Containers。该应用使得Docker可以在Windows Server上原生运行，而不需要启动一个Linux虚拟机来运行Docker。

### Kubernetes

Kubernetes最初是Google的内部项目。在经过用Go语言重写并且优化后，被正式命名为Kubernetes并于2014年开源。该应用致力于高效便捷地管理数以万计的物理机器。由于Google的业界声望，该应用开源之初就人气颇高，大量互联网厂家都加入了社区贡献者的行列。在经历两年多的市场争夺后，Kubernetes 凭借各厂商全力支持以及 77%的市场份额获得全面胜利，成为容器编排领域的事实标准。

\


## 扩展阅读

{% embed url="https://www.infoq.cn/article/r1p3h3_29f4tyimexsyw" %}

{% embed url="https://kubernetes.io/docs/home/" %}
