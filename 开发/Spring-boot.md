[idea创建springboot项目并启动 / 超详细步骤_okikka的博客-CSDN博客](https://blog.csdn.net/qq_42711381/article/details/106756977)
```
建立好后，需要点击Modules然后点击+号，选择Import Module，找到我们本项目的pom.xml文件

且setting中的maven处选择本地maven，勾选setting file 和 reposipory

然后File->Invalidate Cache，重启一下
```
[Spring Initializr](https://start.spring.io/)
手动初始化项目

华为镜像 https://mirrors.huaweicloud.com/java/jdk/

## 新建项目

勾选工具Dependencies：Spring Boot DevTools、Spring web、JDBC API、MYSQL Driver、 Mybatis、MyBatis Framework

application.yml
```
server:

port: 8085

spring:

 #数据库连接配置

 datasource:

   driver-class-name: com.mysql.cj.jdbc.Driver

   url: jdbc:mysql://127.0.0.1:3306/user?characterEncoding=utf-8&useSSL=false&serverTimezone=UTC

   username: root

   password: root

mybatis:

mapper-locations: classpath:mapper/*.xml
```

Controller.hello.java
```
package com.example.demo.Controller;  
  
import org.springframework.web.bind.annotation.*;  
  
@RestController  
@RequestMapping("/login")  
public class hello {  
    @GetMapping("/{id}")  
    public  String helloWorld(@PathVariable Integer id){  
        System.out.println("id==>"+id);  
        return "hello";  
    }  
}
```

## 注解语句

```
@RestController或@ResponseBody //返回字符串本身
```



## maven安装依赖

在pom.xml中安装，插入语句

```
例：
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version> <!-- 使用适当的版本 -->
    <scope>provided</scope>
</dependency>

```

## 无法解析html

添加thymeleaf依赖

```
<dependency>
      <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```



## 启动jar包

```
java -Xdebug -Xrunjdwp:transport=dt_socket,address=5005,server=y,suspend=y -jar challenge-0.0.1-SNAPSHOT.jar

java -Dspring.profiles.active=dev -Dserver.port=8080 -jar test.jar
```

## 报错解决

需要配置项：

![image-20240117173229809](F:\笔记\开发\Spring-boot.assets\image-20240117173229809.png)



![image-20240117173307454](F:\笔记\开发\Spring-boot.assets\image-20240117173307454.png)



![image-20240117173333803](F:\笔记\开发\Spring-boot.assets\image-20240117173333803.png)



![image-20240117173344140](F:\笔记\开发\Spring-boot.assets\image-20240117173344140.png)







