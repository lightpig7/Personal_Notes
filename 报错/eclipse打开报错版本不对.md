
问题描述：
```
eclipse Version 1.8.0_321 of the JVM is not suitable for this product. Version: 17 or greater is required.
```

打开eclipse.ini文件，在文件头处插入以下代码前两行即可(jdk17以上版本的路径)
```
-vm
C:\Users\xxx\.jdks\openjdk-19.0.1\bin 
-startup
```