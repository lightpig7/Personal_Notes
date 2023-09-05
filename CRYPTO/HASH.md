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