临时换源

```
# 使用七牛云的国内镜像
go env -w GOPROXY=https://goproxy.cn,direct

# 或阿里云镜像
go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/,direct
```

