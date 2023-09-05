
```
import os.path  
  
for i in range(47):  
    data=int(os.path.getmtime("./stego/"+str(i)+'.txt')-2000000000)#获得文件改变时间戳  
    print(chr(data),end='')
```