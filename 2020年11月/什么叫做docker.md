# 什么叫做 Docker

本文写于 2020 年 11 月 4 日

## 没有人会喜欢环境配置

在去年的时候我开始学习 Python，并利用 Python 制作了一些小工具。但问题是我很难让别人去用我的软件，除非我让他们安装 Python 的运行环境。

**环境配置**是编程与软件使用的拦路虎之一，还是挺凶猛的一只。用户必须保证操作系统的设置、库的安装……各种事项必须全部安装正确，你的程序才可以正常的跑起来——即便你完全配置成功了，你也可能因为**环境变量**的设置问题而失败。

除开使用者，开发者对于环境配置也十分头疼。例如多版本，我们经常需要同时编写多个项目，每个项目依赖的库的版本是可能不同的。

总而言之，环境配置很讨厌，我们需要解决这个痛点，**软件他到底可不可以带着环境安装呢？**

## Docker 是什么？

Docker 是一个虚拟环境容器，可以让我们把开发环境、代码、配置文件打包到一个容器中，并且发布到任意平台。

如果我们在本地使用 Django 开发网站后台，开发测试完成后，就可以将我们依赖包、数据库、Nginx 等等打包到一个容器中，在进行部署。

Docker 有三个基础概念：Image（镜像）、Container（容器）、Repository（仓库）。

### 镜像

Image 类似于虚拟机中的「镜像」，它是一个包含有文件系统的、面向 Docker 引擎的**只读模板**。

程序的运行是需要环境的，而 Image 就是用来提供环境的。例如，一个 mysql 镜像就是一个包含 mysql 运行环境的模板，如果我们再在此镜像上装上 Apache，我们就可以叫它 Apache + mysql 镜像。

### 容器

Container 是 **Image 的实例**，是一个沙盒。可以认为他就是**一个极简的 Linux 系统环境**，包括：root 权限、进程空间、用户空间、网络空间、其中的应用程序等等。

Docker 引擎利用 Container 来运行程序，不同的 Container 之间不可以互相影响。

我们可以创建、启动、停止、删除 Container——注意，Image 依然是只读的。

## 仓库

Repository 类似于 GitHub 的代码仓库，在 Docker 中是镜像仓库，用来存放镜像文件。

我们要注意，Repository 与 Registry 的区别在于：

Registry 是「存放仓库」的地方，一般会有很多个仓库；Repository 是「存放镜像」的地方，一般仓库会存放一类镜像，利用 tag 进行区分，例如 nodejs 9.x / 11.x / 12.x / 15.x 等。

## Docker 的安装与换源

Docker 可以安装在 Windows、Linux、Mac 等各个平台上，具体可以查看文档 [Install Docker](https://docs.docker.com/engine/install/)。

安装完成之后，可以使用 `docker --version` 查看版本信息。

我们从 Registry 下载 Image 的时候会发现比较慢，所以需要换源至中国，19.03 的换源方法，需要在设置中的 Docker Engine 输入

```json
"registry-mirrors": [
  "https://docker.mirrors.ustc.edu.cn"
]
```

即可。