
```
当一直卡在时区选择时，配置环境变量

Please select the geographic area in which you live. Subsequent configuration
questions will narrow this down by presenting a list of cities, representing
the time zones in which they are located.

  1. Africa      4. Australia  7. Atlantic  10. Pacific  13. Etc
  2. America     5. Arctic     8. Europe    11. SystemV
  3. Antarctica  6. Asia       9. Indian    12. US


ENV DEBIAN_FRONTEND=noninteractive
```

```
使用apt安装时。注意使用-y参数

RUN apt-get install -y xxx
```