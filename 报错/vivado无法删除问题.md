
问题描述：一点想xuninstall.exe就直接开始安装，无法卸载


在操作（1）打开的文件夹下创建xsetup的快捷方式，右键打开属性，为目标添加 `-Uninstall`参数

例如：`xxx/xsetup.exe -Uninstall`

管理员运行该快捷方式即可