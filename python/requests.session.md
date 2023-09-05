
-   requests.session的作用
    -   自动处理cookie，即 **下一次请求会带上前一次的cookie**
-   requests.session的应用场景
    -   自动处理连续的多次请求过程中产生的cookie

```
session = requests.session() # 实例化session对象
response = session.get(url, headers, ...)
response = session.post(url, data, ...)
```