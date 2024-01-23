参考博客：[SpringBoot个人博客从无到有项目搭建——实战综合介绍_springboot项目实战_〆清峰ㄟ的博客-CSDN博客](https://blog.csdn.net/weixin_45019350/article/details/108869025)

## mysql

```
先mysql

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
//可能会报不满足安全需求（ERROR 1819 (HY000): Your password does not satisfy the current policy requirements）

SHOW VARIABLES LIKE 'validate_password%';
//查看密码强度约束

SET GLOBAL validate_password.length = 6;
SET GLOBAL validate_password.mixed_case_count = 0;
SET GLOBAL validate_password.number_count = 0;
SET GLOBAL validate_password.special_char_count = 0;

现在可以设置密码为123456（记得改回来哦）

最后flush privileges;
```

## 端口冲突

```
netstat -anp | grep 8080   //找到被占用端口的运行情况

lsof -i:8800 //查看PID

kill -9 26191 //sudo kill -9 <PID>

或者关闭apache2服务 //service apache2 stop

或者改变apache2服务端口 // vim /etc/apache2/ports.conf中listen 80 改为xxx
```

## 多版本jdk切换

```
update-alternatives --config java

update-alternatives --config javac

sudo update-alternatives --config java //查看openJDK被安装到什么地方，常用于配置环境变量
```

## nohup

启动nohup进程

```
chmod 777 start.sh  //赋予管理员权限
nohup  ./start.sh &  //不间断运行文件内的指令
nohup java -jar jar包名称 &
```

##   javascript时间

在JavaScript中，月份是从0开始计数的，所以1月对应的是0，2月对应的是1

```javascript
function setTime() {
          /*此处为网站的创建时间*/
          var create_time = Math.round(new Date(Date.UTC(2023, 9, 28 , 15, 15, 15)).getTime() / 1000);
          var timestamp = Math.round((new Date().getTime() + 8 * 60 * 60 * 1000) / 1000);
          currentTime = secondToDate((timestamp - create_time));
          currentTimeHtml = currentTime[0] + '年' + currentTime[1] + '天'
              + currentTime[2] + '时' + currentTime[3] + '分' + currentTime[4]
              + '秒';
          document.getElementById("htmer_time").innerHTML = currentTimeHtml;
      }
     
所以在这个函数中，计算当前时间与2023年1月1日0时0分0秒之间的时间差,应该这样设置时间
var create_time = Math.round(new Date(Date.UTC(2023, 0, 1, 0, 0, 0)).getTime() / 1000);

```





