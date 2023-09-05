## 工具：

RSA-tool2
rsatool
https://github.com/3summer/CTF-RSA-tool


P 、Q 为两个质数

N = P\*Q 公共模数 

φ =  (P-1)\*(Q-1)

E 为 公钥指数 gcd( φ , e ) = 1

D 为 私钥指数 gcd( φ , d) = 1

密文 C= m^e mod N

明文 M=c^d mod N

**密钥对生成**  
**① 求N**  
首先随便找两个素数，比如3、11(这边自己实验就取小数展示)

> p = 3  
> q = 11

得到 N

> N = 3*11 = 33

**② 求φ**

> φ = 2*10= 20

**③ 求e**

> gcd(φ,e)=1

**④ 求d**

> e\*d mod φ=1 
> e\*d ≡ 1(mod φ)
> 
> d与e互为逆元

容易得 d = 7  
至此，`d = 7 ，N = 33`，这就是私钥

```
import math  
  
# 在一次RSA密钥对生成中，假设p=473398607161，q=4511491，e=17  
# 求解出d  
  
p=473398607161  
q=4511491  
e=17  
r=(p-1)*(q-1)  
  
def Euler_d(e,r,k):  
    k += 1  
    if math.gcd(e,r) == 1:  
        while True:  
            if (k*r + 1)%e == 0:  
                #直接用d=(k*r+1)/e会丢失精度  
                (d,_) = divmod((k*r +1),e)  
                break  
    else:  
        d ='不可以计算出d'  
        print(d)  
    return d  
print(Euler_d(e,r,0))

求d脚本
```

### 费马小定理

如果 p 是一个质数，而整数 a 不是 p 的倍数，则

> a^(p−1) ≡ 1 (mod p)

可得

> a * a^(p−2) ≡ 1 (mod p)
> 
> 即a 的逆元即为 a^(p-2) (mod p)


# 大数分解

在线网站:http://www.factordb.com/ （换成十进制在使用）

# 正式解决

知道公钥和密文：

①**先用openssl求出N: openssl rsa -pubin -text -modulus -in pubkey.pem**

-in 是输入文件
-pubin是表示从文件中公钥（因为默认是读取私钥）
附加：-pubout是表示输出公钥（默认是输出私钥）

所以上面也可以换成这个：
openssl rsa -in pubkey.pem -pubin -pubout -modulus -text

-text 是输出密钥的各种组件信息以及密钥文件本身的文本（但是不会直接输出公钥，所以需要下一句）
-modulus 是输出密钥模数（就是输出N）模N嘛

或在线网站[RSA公私钥分解 Exponent、Modulus，Rsa公私钥指数、系数(模数)分解--查错网 (chacuo.net)](http://tool.chacuo.net/cryptrsakeyparse)
②分解p、q

在线网站 http://www.factordb.com/


③接着用该命令求私钥
`python rsatool.py -o prikey.pem -e 65537 -p 275127860351348928173285174381581152299 -q 319576316814478949870590164193048041239`

④再用该命令用私钥解出密文`openssl rsautl -decrypt -in flag.enc -inkey prikey.pem`