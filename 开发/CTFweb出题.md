PS:因为一开始出题一头雾水，网上的教程也找不到多少，所以写下文章一起交流学习

首先得学会docker（这里只简单阐述一下），分清镜像和容器的区别：

- 镜像是包含了各种环境或者服务的一个模板
- 容器是镜像的一个实例；

我们需要抒写dockerfile，然后docker将会把它build成一个镜像，需要时我们再示例成容器

## 出题流程

构思题目知识点、解题流程

​    =>本地环境搭建

​        =>构建Docker（dockerfile）

​            =>不断debug即可

因为需要虚拟化的环境，我们首先得熟悉docker，采用dockerfile来构建一个docker

这里给出了一个示例，对代码有说明

```
# 使用基础镜像 Ubuntu 22.04
FROM ubuntu:22.04

# 设置环境变量，以便在安装期间避免交互式提示，不然有可能有些东西需要人交互就会结束程序DEBIAN_FRONTEND=noninteractive用于禁用任何交互式提示
ENV DEBIAN_FRONTEND=noninteractive

# 使用阿里云镜像源以加速软件包安装，更新包列表，apt可以用-qq不输出信息，错误除外
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get update && \

# 安装 Apache2 Web服务器，加-y避免交互
    apt-get install -y apache2 && \

# 安装 Apache2 的 PHP 8.1 模块
    apt-get install -y libapache2-mod-php8.1 && \

# 安装 PHP 的 cURL 扩展
    apt-get install -y php-curl && \

# 清理不需要的软件包和文件，以减小镜像大小
    apt-get clean && \
    rm /var/www/html/index.html && \
    rm -rf /var/lib/apt/lists/*

# 复制本地的 HTML 文件到 Apache 的默认网页目录
COPY html/ /var/www/html/

# 复制自定义的 PHP 配置文件到 Apache 的 PHP 配置目录
COPY php.ini /etc/php/8.1/apache2/php.ini

# 复制本地的 start.sh 脚本文件到镜像中
COPY start.sh start.sh

# 暴露容器的端口 9999，以便外部可以访问该端口
EXPOSE 9999

# 定义容器启动时执行的命令，运行 product.sh 脚本
CMD ["./start.sh"]
```

#### start.sh脚本

```
#!/bin/sh
# Add your startup script
# Change $GZCTF_FLAG to your flag

echo $GZCTF_FLAG > /flag

# DO NOT DELETE
/etc/init.d/xinetd start;
sleep infinity;
```

## docker安装

##### 添加仓库

1. 更新`apt`包管理器索引
   
   ```
   sudo apt update
   ```

2. 安装一些需要的工具
   
   ```
   sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
   ```

3. 添加`Docker`官方`GPG key`
   
   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. 添加`Docker stable`稳定版仓库（Intel、Amd）
   
   ```
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

##### 安装 Docker

1. 更新`apt`索引
   
   ```
   sudo apt update
   ```

2. 安装最新版
   
   ```
   sudo apt install docker-ce docker-ce-cli containerd.io
   ```

##### 添加用户组

安装好`docker`后，需要使用管理员权限进行操作，如：`sudo docker`。为了更方便的使用`docker cli`，我们可以通过用户组的方式进行解决。

```
groups 查看用户组

sudo usermod -aG docker ${USER}

newgrp docker //使用户组更改立即生效
```

## 常用命令

```
docker run -d -p 80:9999 test    //运行镜像构建容器（-d后台运行 -p端口映射 左边为物理机）

docker build -t <镜像名称>:<标签> .  //创建自定义容器镜像（.表示为当前目录）

docker ps    //查看容器

docker stop 3538ab0a2104 //停止容器

docker rm <容器名称或容器ID> //删除容器
docker container prune    //删除所有停止的容器

docker rmi 镜像1(镜像名称或ID) 镜像2 镜像3        //docker删除镜像 （可以-f强制删除）
docker rmi $(docker images | grep "none" | awk '{print $3}')    //删除所有<none>镜像

docker exec -it c4d92beb7f62 /bin/bash 交互式

docker run -dit -p 8080:80 test2 //一启动docker就停止问题解决
```

不要使用`docker-compose`，而要使用`docker compose`，因为我在将 ubuntu 升级到 24.10 时遇到了同样的问题。此外，您还需要授予 sudo 权限，因为这些权限是它工作所必需的，然后重新启动并尝试。
