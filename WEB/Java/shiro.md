## 绕过认证

影响版本：`shiro < 1.1.0` 和`JSecurity 0.9.x`
```
用/./admin 代替 /admin 进行绕过
```

# 反序列化漏洞

特征：返回包中查看是否有rememberMe=deleteMe字段
工具：[https://github.com/feihong-cs/ShiroExploit](https://github.com/feihong-cs/ShiroExploit)

