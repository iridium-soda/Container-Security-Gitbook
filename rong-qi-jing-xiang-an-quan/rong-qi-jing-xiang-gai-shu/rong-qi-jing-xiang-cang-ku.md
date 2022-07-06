# 容器镜像仓库

之前在容器的三大运行概念章节提到了容器镜像仓库的概念。容器镜像仓库是一种存储库，用于存储基于容器的应用开发的容器镜像。

## 镜像仓库、镜像仓库服务器

仓库是集中存储和分发镜像的地方，一般被称作Repository。而镜像仓库服务器（Registry）是运行仓库的平台。

比较常用的就是Registry有Docker Hub，即Docker官方提供的镜像托管分发平台。此外同样应用比较广泛的Registry还有Red Hat 的Quay.io，Google 的 Google Container Registry，Kubernetes 的镜像拉取使用的就是这个服务。还有代码托管平台 GitHub 推出的 ghcr.io。

在国内访问这些仓库可能比较慢，从而导致镜像拉取失败。目前国内一些云服务商也提供了镜像加速服务，即Registry Mirror。注意mirror和image虽然中文名都叫镜像，实际上是两个完全不同的概念。通过这些仓库镜像拉取镜像，速度会快很多。

同样，还可以根据自身需要部署不公开的、有访问权限控制的私有镜像仓库服务器。Docker registry项目同样提供了开源的管理架构供配置使用，不包含一些高级功能，比如可视化管理，访问权限管理等，但足以满足常规的镜像拉取和管理需求。

### 镜像仓库的原理

{% embed url="https://blog.csdn.net/weixin_43238194/article/details/105026340" %}

### 镜像仓库的存储机制

{% embed url="https://supereagle.github.io/2018/04/24/docker-registry/" %}

{% embed url="http://dockone.io/article/2757" %}

## 镜像的命名规范

之前提到镜像在镜像仓库中做持续集成，仓库中保存了镜像上传的每一个版本。而镜像名称的混乱会加大镜像拉取的复杂度，因此使用标准化的命名相当重要。一般而言，标准的镜像路径应该如下所示：

```
<Username>/<Imagename>:tag
```

其中，\<username>是多用户环境下存放该镜像的用户仓库名；\<Imagename>为镜像的名称，比如Ubuntu；\<tag>为镜像的版本标签，例如16.04，18.04等。操作时如果不指定tag，默认为latest，即最新版本的镜像。

