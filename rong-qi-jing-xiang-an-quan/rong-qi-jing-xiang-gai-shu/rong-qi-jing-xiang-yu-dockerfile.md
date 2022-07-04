# 容器镜像与Dockerfile

## 镜像的加载原理

{% embed url="https://cloud.tencent.com/developer/article/1733928" %}

{% embed url="https://cloud.tencent.com/developer/article/1681523?from=article.detail.1733928" %}



## DockerFile：镜像的构建方式

上一章我们提到，镜像的构建是分层的，即在Base layer上一层层添加镜像，上层的镜像依赖于下层的镜像。这样可以避免大量的重复工作。

![](<../../.gitbook/assets/Image layers.png>)

但一般实际应用中的镜像远不止几层，为了进一步减少工作量并且保证镜像的标准化生成和交付，DockerFile应运而生。DockerFile是构建标准镜像的脚本也是一个Docker镜像的描述文件，使用DockerFile是最基本的创建镜像的方式。文件的每一行构建一层镜像，就像一层层地盖房子一样。因此每一条指令的内容，就是描述该层应当如何构建。

## DockerFile的构建实例

Dockerfile结构主要分为四个部分：

1. 基础镜像（Base layer）信息
2. 维护者信息
3. 镜像操作指令
4. 容器启动时执行指令。

Dockerfile每行支持一条指令，并且支持使用以#号开头的注释。下面的示例代码基于Centos，构建了一个带有httpd包的镜像。

```docker
#基于centos镜像
FROM centos

#维护人的信息
MAINTAINER AuthorName

#安装httpd软件包
RUN yum -y update
RUN yum -y install httpd

#开启80端口
EXPOSE 80

#复制网站首页文件至镜像中web站点下
ADD index.html /var/www/html/index.html

#复制该脚本至镜像根目录下，并修改其权限
ADD script.sh /script.sh
RUN chmod 775 /script.sh

#当启动容器时执行的脚本文件
CMD ["/script.sh"]
```

下面是一些构建DockerFile的常用命令：

* `FROM`：指名Base layer的镜像名称。
* `MAINTAINER`：维护人的信息。
* `RUN`：在镜像内安装新应用的指令。比如`yum install`,`apt get`等。
* `EXPOSE`：创建容器时暴露端口。
* `ADD`：向镜像内添加文件。
* `WORKDIR`：设置当前工作目录。
* `CMD`：启动容器时执行的Shell命令。注意这些命令会被`docker run`命令的参数覆盖。

## 扩展阅读

{% embed url="https://zhuanlan.zhihu.com/p/262005517" %}

{% embed url="https://www.cnblogs.com/along21/p/10243761.html" %}
更多的指令说明
{% endembed %}
