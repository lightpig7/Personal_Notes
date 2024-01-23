[GitHub - 0x727/JNDIExploit: 一款用于JNDI注入利用的工具，大量参考/引用了Rogue JNDI项目的代码，支持直接植入内存shell，并集成了常见的bypass 高版本JDK的方式，适用于与自动化工具配合使用。](https://github.com/0x727/JNDIExploit)

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

​									伪造rmi服务器、伪造http服务器=>

​									访问rmi服务器=>

​									rmi回复一个http服务地址=>

​									靶机访问http服务器的恶意服务=>

​									实现远程任意代码执行



