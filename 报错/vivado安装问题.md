问题描述：点击xsetup.exe没反应

**一、路径不能有中文或空格**

**二、目录路径不能太长**

**三、xsetup.exe实际调用的是bin目录下的xsetup.bat**

所以可以先试试目录中cmd运行两个文件，看看报什么错，如果是`Administrative permissions are not available. Please restart the command line shell as Administrator.`

即可改（最好先保存副本）后缀为xsetup.txt删除：
```
%SYSTEMROOT%\System32\net session >nul 2>&1  
if NOT %errorLevel% == 0 (  
echo ERROR: Administrative permissions are not available. Please restart the command line shell as Administrator.  
set EXITCODE=1  
goto :end  
)  
```

再改回来管理员运行即可