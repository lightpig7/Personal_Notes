## 换源

1. **修改CentOS仓库配置（使用Vault镜像源）**

   bash

   ```bash
   sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
   sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
   ```

2. **清理并重建YUM缓存**

   bash

   ```bash
   yum clean all
   yum makecache
   ```



## 安装必要软件

```bash
yum install -y openssh-server openssh-clients sudo passwd
```

```bash
# 生成 SSH 主机密钥
ssh-keygen -A

# 允许 root 登录（仅限测试环境！）
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config

# 允许密码认证
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```

### 启动 SSH 服务

bash

```bash
# 直接前台启动（保持容器运行）
/usr/sbin/sshd -D
```

```
yum install -y yum-utils
```

在执行 `yum list docker-ce --showduplicates | sort -r` 之前，需要先添加 Docker 的 YUM 仓库。如果没有正确配置仓库，YUM 将无法找到 `docker-ce` 包。
解决方法：

bash

```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

或者使用阿里云的镜像源（推荐）：

bash

```bash
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

#### **手动下载旧版本 RPM 包（适用于强制需求）**

若必须安装 `18.03.1`，需手动下载：

bash

```bash
# 从阿里云镜像站下载
wget https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/Packages/docker-ce-18.03.1.ce-1.el7.centos.x86_64.rpm

# 安装本地 RPM 包
yum localinstall -y docker-ce-18.03.1.ce-1.el7.centos.x86_64.rpm
```

```bash
wget https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/Packages/docker-ce-18.03.1.ce-1.el7.centos.x86_64.rpm
```

#### **清理 YUM 缓存并禁用 GPG 校验（临时）**

bash

```bash
yum clean all
yum install -y --nogpgcheck docker-ce-18.03.1.ce-1.el7.centos.x86_64.rpm
```

```
--nogpgcheck
```

：跳过 GPG 校验（仅限临时使用，生产环境慎用）

```shell
# 下载docker-ce
wget https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/Packages/docker-ce-17.03.0.ce-1.el7.centos.x86_64.rpm 

# 下载docker-ce-selinux
wget https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/stable/Packages/docker-ce-selinux-17.03.0.ce-1.el7.centos.noarch.rpm
```