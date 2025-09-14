[GitHub - 0x727/JNDIExploit: 一款用于JNDI注入利用的工具，大量参考/引用了Rogue JNDI项目的代码，支持直接植入内存shell，并集成了常见的bypass 高版本JDK的方式，适用于与自动化工具配合使用。](https://github.com/0x727/JNDIExploit)

JNDI 注入，即当开发者在定义 `JNDI` 接口初始化时，`lookup()` 方法的参数被外部攻击者可控，攻击者就可以将恶意的 `url` 传入参数，以此劫持被攻击的Java客户端的JNDI请求指向恶意的服务器地址，恶意的资源服务器地址响应了一个恶意Java对象载荷，对象在被解析实例化，实例化的过程造成了注入攻击。

JNDI可利用服务有：RMI、LDAP、DNS

存在条件：

```
存在new InitialContext().lookup(url);
且url可控


InitialContext();构建一个初始上下文
lookup(String name);检索命名对象
```

POC:

```
dns://dns.log.cn
```

攻击流程：

向靶机传入参数（rmi、ldap、dns）=>

​                                    伪造rmi服务器、伪造http服务器=>

​                                    访问rmi服务器=>

​                                    rmi回复一个http服务地址=>

​                                    靶机访问http服务器的恶意服务=>

​                                    实现远程任意代码执行
