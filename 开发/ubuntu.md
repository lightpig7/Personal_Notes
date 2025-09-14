## 基础

- mkdir:创建目录

- mv：移动文件、更改文件名

- rm:删除文件

- rm -r ：删除文件夹

- rm -f ：强制删除

**引号**

```
在linux中，单引号所包含的内容只代表纯文本

双引号与单引号的不同之处在于，双引号会解析特殊字符，如果想要忽略特殊字符，那么需要用到 `\` 转义字符,双引号不会解析命令，只是在单引号的基础上增添识别特殊符号的功能

反引号和单引号与双引号则完全不同，后两者都是括号内的字符当作字符串处理，而反引号则处理命令
```

**. 点 source**

```
用. file执行文件，是不需要file有x权限的
```

## apache

```
systemctl status apache2.service 查看状态

sudo service apache2 restart 重启apache服务

sudo service apache2 start 

sudo service apache2 stop
```

## apt-get

```
使用aptitude代替

sudo apt-get purge libelf1    \\卸载

sudo apt-get autoremove libapache2-mod-php7.4 卸载
```

## apt换源

```
换源
sudo vim /etc/apt/sources.list
sudo apt update
sudo apt upgrade


# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
cat > /etc/apt/sources.list << 'EOF'
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
EOF

cat > /etc/apt/sources.list << 'EOF'
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
EOF
# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

路径为：/etc/apt/sources.list.d/ubuntu.sources

```
Types: deb
URIs: http://mirrors.tuna.tsinghua.edu.cn/ubuntu/
Suites: noble noble-updates noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

## java

```
update-alternatives --config java

update-alternatives --config javac

java -jar xxx.jar
```

## mysql

```
sudo mysql_secure_installation
```

## mvn

```
mvn clean package -DskipTests
```



## 权限管理

```
sudo chown www-data:www-data /path/to/your/file_or_directory //更改文件所有者

sudo chown 777 html

对于目录和文件权限，数字的含义如下：

4: 读权限（Read）
2: 写权限（Write）
1: 执行权限（Execute）
因此，chmod 755 html 将给定的目录 html 设置为以下权限：

所有者（owner）：读、写、执行（7 = 4 + 2 + 1）
组用户（group）：读、执行（5 = 4 + 1）
其他用户（others）：读、执行（5 = 4 + 1）
```

# 安装工具

## VM tools

```
sudo apt install open-vm-tools
```

[如果安装后不能正常复制文件](./Ubuntu18-64位安装VMware Tools后无法复制粘贴解决办法_ubuntu 64 复制不了-CSDN博客 (2024_6_3 23_56_02).html) 

## ssh

要在 Ubuntu 系统上安装 OpenSSH 客户端应用程序，请在终端提示符下使用此命令：

```
sudo apt install openssh-client
```

要安装 OpenSSH 服务器应用程序和相关支持文件，请在终端提示符下使用此命令：

```
sudo apt install openssh-server

sudo systemctl enable ssh

# 手动启动 SSH 服务（使用 service 命令或直接运行二进制文件）
service ssh start
```

# 网络配置

## 重启网卡

```
kali:
sudo service networking restart

sudo systemctl restart networking

ubuntu:
service network-manager restart
```

## 网卡掉了

```
1、首先确保网卡设备名称能看到，看不到，以下步骤无意义；
sudo lshw -c network |grep ens

2、命令行执行以下命令；
sudo service NetworkManager stop
sudo rm  /var/lib/NetworkManager/NetworkManager.state
sudo gedit /etc/NetworkManager/NetworkManager.conf 

这一步将打开一个文件，把里面的managed=false改为managed=true 再保存。
sudo service NetworkManager start

3、若成功则右上角已经能看到本地网络标志；
```



## dns解析

当网络很慢时，可配置`/etc/hosts`或者 `/etc/resolv.conf`文件来进行DNS解析

## deb包安装

```
https://mirrors.aliyun.com/docker-ce/linux/ubuntu/dists/xenial/pool/stable/amd64/?spm=a2c6h.25603864.0.0.5b3f1ae84PxxyV
```

```
sudo apt-get install -y docker-ce=5:20.10.13~3-0~ubuntu-jammy docker-ce-cli=5:20.10.13~3-0~ubuntu-jammy
```

