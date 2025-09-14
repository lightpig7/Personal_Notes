## 快捷键

**空格键：反汇编窗口切换文本跟图形 

F5：将一个函数逆向出来(生成c伪代码)

shift+F12：自动分析出参考字符串

shift+F7：打开段名称

ACDU（调整）: A:字符串C:代码D:数据（多次摁）U:

ALT+T：搜索字符串(文本搜索)

ALT+Q：添加到结构体中

G：跳转

N：给变量改名字

；（分号）：添加注释
（shift+ ; ）:添加注释（免除跳转）

## 基础

可在过程中用右键进行各种十六进制等转换

IDA采用递归下降法进行反编译，它的优点在于很少会在反编译时把数据当作代码来处理，不过这次IDA很明显把flag当成代码，进行了反编译，因此在string界面无法找到flag

解决方法是在IDA打开文件时选择binary file（二进制文件）,在此模式下IDA不会进行反编译，此时再打开strings界面就可发现flag

## 动态调试

配置好调试的服务端后，接下来开始配置客户端IDA，正常启动IDA打开【Debugger】-【Run】-【Remote Linux debugger】来按F9设置调试选项：

[IDA | 远程动态调试的基本设置 - 暖暖草果 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zhwer/p/12484708.html)

其中，填入信息如下：

选项    应填入的信息
Application    带调试文件在Linux上的路径（包括带调试文件）.
Directory    调试文件所在的文件夹路径.
Parameters    运行参数，例如运行可执行文件时传递到main函数的命令参数.
Hostname    Linux的IP地址.
Port    Linux的端口，这里默认即可，和Linux上运行的linux_serverx64开启的端口一致.
Password    Linux登陆密码.

### 调试跟踪常用快捷键

    快捷键|功能|
    |---|---|
    |F7|  单步步进，遇到call/指令跟进|
    |F8|  单步步过，遇到call指令不跟进|
    |F4|  运行到光标所在的行|
    |Ctrl + F7|  知道该函数返回时才停止|
    |F9|  运行程序|
    |Ctrl+F2|  终止一个正在运行的进程|
    |F2|  设置断点|
