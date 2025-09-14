更换国内源

```
pip install xxx -i https://pypi.tuna.tsinghua.edu.cn/simple

本地安装也
pip install h5py-2.10.0-cp36-cp36m-win32.whl -i https://pypi.tuna.tsinghua.edu.cn/simple


清华：https://pypi.tuna.tsinghua.edu.cn/simple
中国科学技术大学 https://pypi.mirrors.ustc.edu.cn/simple/
华中理工大学：http://pypi.hustunique.com/
阿里云：http://mirrors.aliyun.com/pypi/simple/
豆瓣：http://pypi.douban.com/simple/
百度：https://mirror.baidu.com/pypi/simple
```

pip卸载

```
pip uninstall xxx
```

pip升级

```
python -m pip install --upgrade pip
```

pip位置

```
pip 
```

pip查看已安装包

```
pip list
```



##### 安装报错可以手动安装

```
先安装wheel
pip install wheel

通过命令python -m pip debug --verbose查看自己python适合的cp版本

然后在
https://www.lfd.uci.edu/~gohlke/pythonlibs/#h5py找适合自己版本的
```

然后进入 `h5py-2.10.0-cp36-cp36m-win_amd64.whl` 的目录中

```
pip install h5py-2.10.0-cp36-cp36m-win32.whl即可
```

[PyPI · The Python Package Index](https://pypi.org/)

[Archived: Python Extension Packages for Windows - Christoph Gohlke (uci.edu)](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

[Simple Index (tsinghua.edu.cn)](https://pypi.tuna.tsinghua.edu.cn/simple/)

https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient

## anaconda

有时候环境总会出现各种各样的问题，此时我们可以采用anaconda来搭建虚拟环境

```
conda create --name python36 python=3.6 

conda env list

activate python36
conda activate python310

conda env remove --name py
```

#### 导出导入已有环境

打开Anaconda Prompt，输入

```
activate your_environment
```

来到自己要备份的虚拟环境下，然后输入

```
conda env export > environment.yaml 
```

当我们想再次创建该环境，或根据别人提供的.yaml文件复现环境时，可以：

```python
conda env create -f environment.yaml
```

[保姆级教程，4步完成JupyterLab插件安装（附多款高生产力插件推荐） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/556433254)
