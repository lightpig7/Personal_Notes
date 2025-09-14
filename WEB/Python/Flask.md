## flask-session

```
解密 python flask_session_cookie_manager3.py decode -s "secret_key" -c "session"

加密 python flask_session_cookie_manager3.py encode -s "secret_key" -t "data"
```

爆破session密钥：[Flask-Unsign/.github/ISSUE_TEMPLATE at master · Paradoxis/Flask-Unsign · GitHub](https://github.com/Paradoxis/Flask-Unsign)

![image-20240109115120253](Flask.assets\image-20240109115120253.png)

## pin攻击

```
xxx
/proc/sys/kernel/random/boot_id

11:pids:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
10:cpuacct,cpu:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
9:memory:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
8:perf_event:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
7:devices:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
6:net_prio,net_cls:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
5:cpuset:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
4:freezer:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
3:hugetlb:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
2:blkio:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25
1:name=systemd:/docker/6778b81d4814a2cba3f7900e5274c06a60ddb8fb9e21e30bd120a0f604906b25

/proc/self/cgroup

mac 02:42:ac:11:00:06   2485377892358
```

然后沙盒逃逸，flag在环境变量里面



 参考博客：

[SSTI进阶 | 沉铝汤的破站 (chenlvtang.top)](https://chenlvtang.top/2021/03/31/SSTI进阶/)

[关于ctf中flask算pin总结-CSDN博客](https://blog.csdn.net/qq_35782055/article/details/129126825)

[深入浅出Flask PIN - 合天网安实验室 - SegmentFault 思否](https://segmentfault.com/a/1190000042254626)



## yaml反序列化

## pickle反序列化

