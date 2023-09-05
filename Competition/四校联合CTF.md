
```
flask

先抓包然后拿session解密（base64），然后根据目录扫描得出，源码，构造payload(需要用到SSTI)，用flask-session加密，上传到admin页面，得到结果

source in /source"
        return rsp
        
@app.route('/source')
def source():
    f = open(__file__, 'r')
    rsp = f.read()
    f.close()
    return rsp[rsp.index('source'):]
    
@app.route('/admin')
def admin_handler():
    try:
        role = session.get('role')
        if not isinstance(role, dict):
            raise Exception
    except Exception:
        return '~~~~~~hacker!'
    if role.get('is_admin') == 1:
        flag = role.get('flag') or 'admin'
        flag = filter(flag)
        message = "%s, I hope you have a good time!your flag is " % flag
        return render_template_string(message)
    else:
        return "I don't know you"
if __name__ == '__main__':
    app.run('0.0.0.0', port=80)
```

`今日心得：多刷题，多总结，不要嫌麻烦`