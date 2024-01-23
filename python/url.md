


-   requests.session的作用
    -   自动处理cookie，即 **下一次请求会带上前一次的cookie**
-   requests.session的应用场景
    -   自动处理连续的多次请求过程中产生的cookie

```python
session = requests.session() # 实例化session对象
response = session.get(url, headers, ...)
response = session.post(url, data, ...)
```



```python
req=requests.post(url=url,data=data,allow_redirects=False)
#allow_redirects默认为true，意思是准许跳转
```

## Handler类

```
HITPDefaultErrorHandler ：用于处理响应错误，错误都会抛出 HTTP Error 类型的异常
HTTPRedirectHandler ：用于处理重定向
HTTPCookieProcessor 用于处理 ki es
ProxyHandler ：用于设置代理 默认 理为空
HπPPasswordMgr ：用于管理密码，它维护了用户名和密码的表
HTTPBasicAuthHandler 用于管理认证，如果一个链接打开时需要认证，那么可以用它来解决认证问题
```



## 常用文件头

```
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/119.0',
    'Connection': 'close',
    'Content-Type':'application/x-www-form-urlencoded; charset=UTF-8',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8'
}
```

