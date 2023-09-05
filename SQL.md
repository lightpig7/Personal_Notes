## 当重新装SSMS时

```
看来您需要删除以下注册表项才能开始新的设置。

对于 x64 操作系统:

**“HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Management Studio”**

对于 x86 操作系统:

**“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server Management Studio”**

然后尝试重启后安装

删除之前的注册表： WIN+R  CMD打开命令框，输入命令  
reg DELETE “HKLM\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Management Studio” /reg:32   选择'yes'
```