---

---

### Docker概述

#### Dicker为什么会出现？

一款产品 - 开发-- 上线 两套环境

开发 -- 运维。 问题，我的电脑上可以运行，版本更新，导致服务不可用！对于运维考验大

环境配置十分麻烦，每一个机器都要部署环境（集群Redis、Hadoop） 费时费力。

发布一个项目（jar + Redis Mysql jdk）,项目能不能带上环境安装打包

不能跨平台， window开发，最后发不到Linux。



传统：开发jar,剩余运维来做

现在：开发打包部署上线，一套流程做完。



java - apk -发布（应用商店）- 使用apk -安装

Java - jar -- 打包带上环境（镜像） -- （Docker 仓库：商店） -- 下载镜像 -- 直接运行



Docker给以上问题，提出了解决方案！



Docker

![Screenshot 2020-10-24 at 17.29.08](/Users/guohuanjie/Documents/programming/LearnNote/image/Screenshot 2020-10-24 at 17.29.08.png)

Docker思想来源于集装箱

JRE - 多个应用（端口冲突） -- 原来都是交叉的

隔离： Docker核心思想！打包装箱！每个箱子都是隔离的。

Docker通过隔离机制，可以将服务器用到极致！



#### Docker历史

2013年 Docker开源！

Docker为什么这么火？十分轻巧！

容器技术出来之前，都是用虚拟机！

虚拟机： 笨重！

虚拟机也是属于虚拟化技术，Docker容器技术，也是虚拟化技术

```
vm : linux centos 原声镜像隔离，需要开启多个虚拟机！   几个G  几分钟
docker: 隔离，镜像（最核心的环境 4m + jdk+ mysql） 十分小巧，运行镜像就可以了！  几个M   秒级启动！
```



Docker基于go语言开发



#### Docker能干嘛

> 虚拟机技术

![image-20201024183018136](/Users/guohuanjie/Library/Application Support/typora-user-images/image-20201024183018136.png)

虚拟机缺点：

1、资源占用多

2、冗余步骤多

3、启动慢



> 容器化技术

容器化技术并不是虚拟一个操作系统

![image-20201024184532392](/Users/guohuanjie/Library/Application Support/typora-user-images/image-20201024184532392.png)



比较docker和虚拟机不同：

- 传统虚拟机，虚拟出一些硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件
- 容器内的应用直接运行在宿主机上，容器是没有自己的内核的，也没有虚拟我们硬件，所以轻便
- 每个容器互相隔离，每个容器有一个属于自己的文件系统，互不影响。





> DevOps(开发、运维)

**应用更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：打包镜像发布测试，一键运行

**更便捷的升级和扩缩容**

使用了Docker后，部署应用就和搭积木一样！

项目打包为一个镜像，扩展服务器

**更简单的系统运维**

在容器化后，我们开发、测试环境都是高度一致的

**更高效的计算资源利用**：

Docker是内核级别的虚拟化，可以在一个物理机上运行很多容器实例。



### Docker的安装

#### Docker的基本组成

![image-20201024192930580](/Users/guohuanjie/Library/Application Support/typora-user-images/image-20201024192930580.png)

**镜像**image：

docker镜像好比模版，可以通过这个模版创建容器



**容器**container：

Docker利用容器技术，独立运行一个或一组应用，由镜像来创建

启动，停止，删除，基本命令！

容器可以理解为简易的Linux系统



**仓库**repository：

仓库是存放镜像的地方！

仓库分为公有和私有仓库！

Docker Hub

阿里云都有容器服务器（配置加速）





