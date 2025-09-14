
pip安装好pwntools库后，导入pwn库时发生以下错误
```
Traceback (most recent call last):
  File "F:\code\py\luogu\test.py", line 1, in <module>
    from pwn import *
  File "C:\Users\1\AppData\Local\Programs\Python\Python37\lib\site-packages\pwn\__init__.py", line 4, in <module>
    from pwn.toplevel import *
  File "C:\Users\1\AppData\Local\Programs\Python\Python37\lib\site-packages\pwn\toplevel.py", line 23, in <module>
    from pwnlib import *
  File "C:\Users\1\AppData\Local\Programs\Python\Python37\lib\site-packages\pwnlib\dynelf.py", line 57, in <module>
    from pwnlib import elf
  File "C:\Users\1\AppData\Local\Programs\Python\Python37\lib\site-packages\pwnlib\elf\__init__.py", line 9, in <module>
    from pwnlib.elf.corefile import Core
  File "C:\Users\1\AppData\Local\Programs\Python\Python37\lib\site-packages\pwnlib\elf\corefile.py", line 79, in <module>
    from elftools.common.py3compat import bytes2str
ModuleNotFoundError: No module named 'elftools.common.py3compat'
```

是因为elftools.common.py3compat已经不存在，bytes2str移动到了elftools.common.utils

```
所以将在报错的 \lib\site-packages\pwnlib\elf\corefile.py处，将

from elftools.common.py3compat import bytes2str  
from elftools.common.utils import roundup  
from elftools.common.utils import struct_parse  
from elftools.construct import CString

改为

from elftools.common.utils import bytes2str  
from elftools.common.utils import roundup  
from elftools.common.utils import struct_parse  
from elftools.construct import CString
```

即可解决