```
CSRF token绕过技巧：#SRC经验技巧#

1. 删除 token 或将其留空
2. 将 POST 更改为 GET
3. 将 token 替换为随机值
4. 使用随机 token 匹配约束
5. 重复使用以前的token
6. 绕过正则表达式检查
7. 删除referer标头
```