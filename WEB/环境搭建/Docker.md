删除镜像
```
docker rmi image_id
```

## dockerfile

```
当一直卡在时区选择时，配置环境变量

Please select the geographic area in which you live. Subsequent configuration
questions will narrow this down by presenting a list of cities, representing
the time zones in which they are located.

  1. Africa      4. Australia  7. Atlantic  10. Pacific  13. Etc
  2. America     5. Arctic     8. Europe    11. SystemV
  3. Antarctica  6. Asia       9. Indian    12. US


ENV DEBIAN_FRONTEND=noninteractive
```

```
使用apt安装时。注意使用-y参数

RUN apt-get install -y xxx
```

```
镜像操作：

docker pull <image>：下载一个容器镜像。
docker images：列出所有本地镜像。
docker rmi <image>：删除本地镜像。
docker build -t <image_name> <path_to_Dockerfile>：构建一个镜像。
容器操作：

docker run -it <image>：创建并启动一个容器。
docker ps：列出正在运行的容器。
docker ps -a：列出所有容器，包括已停止的。
docker start <container>：启动一个已停止的容器。
docker stop <container>：停止一个正在运行的容器。
docker restart <container>：重启一个容器。
docker rm <container>：删除一个容器。
容器日志和信息：

docker logs <container>：查看容器的日志。
docker inspect <container>：查看容器的详细信息。
docker top <container>：查看容器内运行的进程。
docker stats：实时查看容器的资源使用情况。
容器交互：

docker exec -it <container> <command>：在容器内运行交互式命令。
docker attach <container>：连接到一个正在运行的容器的标准输入、输出和错误流。
容器网络：

docker network ls：列出 Docker 网络。
docker network inspect <network>：查看网络的详细信息。
docker run --network=<network> ...：创建容器并连接到指定网络。
容器数据卷：

docker volume ls：列出 Docker 数据卷。
docker volume create <volume>：创建数据卷。
docker run -v <volume>:<container_path> ...：挂载数据卷到容器内。
Docker Compose：

docker-compose up：启动 Docker Compose 配置。
docker-compose down：停止并删除 Docker Compose 配置的容器。
Docker Swarm：

docker swarm init：初始化 Docker Swarm。
docker service ls：列出 Swarm 服务。
docker node ls：列出 Swarm 节点。
Docker 登录和注销：

docker login：登录到 Docker Hub 或其他容器仓库。
docker logout：注销当前登录状态。
Docker 帮助：

docker --help：查看 Docker 的帮助文档。
docker <command> --help：查看特定命令的帮助文档。
```

