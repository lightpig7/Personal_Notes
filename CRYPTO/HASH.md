hash长度拓展攻击。
```
这题有个工具叫HashPump。kali中安装方法如下（root终端依次输入）

git clone https://github.com/bwall/HashPump
apt-get install g++ libssl-dev 
cd HashPump
make
make install
应用场景：md5("密文"+"已知字符串")=现有哈希值

文件夹下开终端（cd进去也可以），输入hashpump。

Input Signature                    #现有哈希值（题目给的MD5）
Input Data                         #已知字符串
Input Key Length                   #为密文（salt）长度
Input Data to Add                  #为补位后自己加的字符串（自定义）

满足：md5("密文"+"处理过的已知字符串")=处理过的哈希值

payload：（url加密就是把\x换成%）
```
可以看到返回给我们两行内容，第一行是处理过的哈希值，第二行是处理过的已知字符串。





## `ciphey`

### 在 Windows上安装命令：

使用pip安装即可

```
pip install ciphey  -i https://pypi.mirrors.ustc.edu.cn/simple/
```

windows编码错误修改    (根据错误修改代码，主要是在读取文件的时候发生错误，将"r" 改为"rb",即可。)

> **UnicodeDecodeError: 'gbk' codec can't decode byte 0xbf**

### 导入Ciphey

您可以导入Ciphey的main并在自己的程序和代码中使用它。

> **`from Ciphey.__main__ import main`**

### 参数列表

> **（查看完整的参数列表，请运行`ciphey --help`.）** 

```cpp
ciphey --help
用法: ciphey [选项] [TEXT_STDIN]
 
  Ciphey - 自动解密工具
 
  文档地址: https://github.com/Ciphey/Ciphey/wiki
 
  Discord (这里支持，我们大部分时间都在线):
  https://discord.ciphey.online/
 
  GitHub: https://github.com/ciphey/ciphey
 
 cipher是一种使用智能人工智能的自动解密工具
和自然语言处理。输入加密文本，获取解密文本
 
 
例如:
	基本用法: ciphey -t "aGVsbG8gbXkgbmFtZSBpcyBiZWU="
 
选项:
  -t, --text TEXT            您想要解密的密文。
  -q, --quiet                减少冗长的显示,直接给结果
  -g, --greppable            只输出答案(对于grep很有用)
  -v, --verbose
  -C, --checker TEXT         使用给定的检查器
  -c, --config TEXT          使用给定的配置文件。默认为
                             appdirs.user_config_dir('ciphey',
                             'ciphey')/'config.yml'
 
  -w, --wordlist TEXT        使用给定的密码字典
  -p, --param TEXT           将参数传递给语言检查器
  -l, --list-params BOOLEAN  列出所选模块的参数
  --searcher TEXT            选择要使用的搜索算法
  -b, --bytes                强制密码使用二进制模式作为输入
  --default-dist TEXT        设置默认的字符/字节分布
  -m, --module PATH          从给定路径添加模块
  -A, --appdirs              输出密码到想要的文件位置
 
  -f, --file FILENAME
  --help                     显示此帮助消息并退出。
```