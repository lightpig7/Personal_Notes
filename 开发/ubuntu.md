## 基础

- mkdir:创建目录
- mv：移动文件、更改文件名
- rm:删除文件
- rm -r ：删除文件夹
- rm -f ：强制删除
```
命令模式下
gg		# 跳至文件首行
dG		# 删除光标所在行到末尾行内容，d删除，G跳转到文件末尾行
```

```
1、使用“gg”将光标移动到文档开头

2、使用“v”切换到可视模式

3、再用“G”将光标移动到文档尾部

这三步操作相当于Alt+A全选代码

4、最后使用“=”，即可完成整个文档的自动排版。


全部复制：按esc键后，先按gg，然后ggyG

:set paste 解除vim粘贴时全注释问题

vim查找使用 /xxx 查找前一个匹配项，只需按下"N"键 查找下一个匹配项，只需按下"n"键 
```

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

sudo apt-get purge libelf1:卸载

sudo apt-get autoremove libapache2-mod-php7.4 卸载
```

## 换源

```
换源
sudo vim /etc/apt/sources.list
sudo apt update
sudo apt upgrade


# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```



```
session存放路径

/var/lib/php/sessions
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

## 重启网卡

```
kali:
sudo service networking restart

sudo systemctl restart networking

ubuntu:
service network-manager restart
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

## tar

#### 压缩 tar 文件

```
tar -czvf archive.tar.gz directory_to_compress
```

- `-x`: 解压缩
- `-z`: 使用 gzip 解压缩
- `-v`: 显示详细信息（可选）
- `-f`: 指定要解压的归档文件

#### 解压 tar 文件

```
tar -xzvf archive.tar.gz
```

- `-x`: 解压缩
- `-z`: 使用 gzip 解压缩
- `-v`: 显示详细信息（可选）
- `-f`: 指定要解压的归档文件
