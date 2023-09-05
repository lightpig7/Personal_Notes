
SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能，且没有对目标地址做过滤与限制。比如从指定URL地址获取网页文本内容，加载指定地址的图片，文档等等

SSRF漏洞通过篡改获取资源的请求发送给服务器（服务器并没有检测这个请求是否合法的），然后服务器以他的身份来访问服务器的其他资源。SSRF利用存在缺陷的Web应用作为代理攻击远程和本地的服务器

# PHP伪协议

php支持的协议和封装协议

### \file://文件绝对路径和文件名
file:// 用于访问本地文件系统，在CTF中通常用来读取本地文件的且不受配置文件中allow_url_fopen与allow_url_include的影响

常用敏感路径：file:///var/www/html/index.php、file:/etc/passwd

### dict://

### php://
使用条件(php.ini配置)：

                                  allow_url_fopen	  allow_url_include
php://input	                         on/off	                          on       （常用）
php://stdin	                         on/off	                          on
php://memory	                     on/off	                          on
php://temp                           on/off	                          on
php://filter	                         on/off	                        on/off      (常用)



#### php://filter
		名称                            	                 描述
resource<--->要过滤的数据流	 这是个必要参数。它指定了你需要筛选过滤的数据流(简单来说就是你的数据来源)
read<--->读链的筛选列表	        这个参数可选。可以设定一个或多个过滤器名称。以管道符（/）分隔
write<--->读链的筛选列表	        这个参数可选。可以设定一个或多个过滤器名称。以管道符（/）分隔

格式如下：
读：php://filter/resource=文件名
 
php://filter/read=convert.base64-encode/resource=文件名(**常常**当源码不能直接被读取时，采用base64加密)
 
写：php://filter/resource=文件名&txt=文件内容
 
php://filter/write=convert.base64-encode/resource=文件名&txt=文件内容



#### php://input
                   ----可以访问请求的原始数据的只读流, 将post请求中的数据作为PHP代码执行。

php://input是php语言中一个只读的数据流；通过"php://input"，可以读取从Http客户端以POST方式提交、请求头“Content-Type”值非"multipart/form-data​"的所有数据；"php://input"一般用来读取POST上来，除已被处理以外的剩余数据。

说白了就是当遇到正则匹配时GET传参不能出现的敏感字符，可以通过php://input在POST上传最后绕过了检测

```
GET : index.php?cmd=php://input
 
POST : flag.php(目的数据)
```

data:// 协议和php://input作用相同，如果碰到input被过滤的情况可以用其替代

zip://, bzip2://, **zlib://** 均属于压缩流，可以访问压缩文件中的子文件，更重要的是不需要指定后缀名


## 文件扫描

扫描?url=127.0.0.1/x

## 端口扫描

通过服务器访问内网地址，直接用bp爆破url传参中的端口即可：127.0.0.1:x

## POST请求（gopher协议）

如果在x.php可以利用curl传url，那么我们可以用gopher协议在x.php中构造post请求包往flag.php传key值，以此获取flag

gopher协议没有默认端口，需要特殊指定，而且需要指定POST方法，回车换行需要使用%0d%0a,而且POST参数之间的&分隔符也需要URL编码
```
POST /flag.php HTTP/1.1
Host: 127.0.0.1:80
Content-Type: application/x-www-form-urlencoded
Content-Length: 36

key=d93819c4c1a18dc606dc5c6486f77227
```
gopher的数据需要用url编码多次之后再发送，且第一次编码后%0A需全部替换成%0D%0A，现在进行编码
content-lenght为key值长度

##### gopher使用结构gopher://127.0.0.1:80/_{TCP/IP数据流}

以下为注入样例
```
?url=http://127.0.0.1:80/index.php?url=gopher://127.0.0.1:80/_POST%252520/flag.php%252520HTTP/1.1%25250D%25250AHost%25253A%252520127.0.0.1%25253A80%25250D%25250AContent-Type%25253A%252520application/x-www-form-urlencoded%25250D%25250AContent-Length%25253A%25252036%25250D%25250A%25250D%25250Akey%25253D0bd5e192bb3c5e0f3df6b8ddf4252d9c
```

因为curl在index.php中

