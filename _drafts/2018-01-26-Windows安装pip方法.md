# Windows安装pip方法

本文主要介绍pip在windows中的安装。

## 下载

在网址[https://pypi.python.org/pypi/pip#downloads](https://pypi.python.org/pypi/pip#downloads)下载安装包，截止目前最新版本为`pip-9.0.1.tar.gz`。

## 安装

解压安装包，打开命令行，切换至解压后的安装包下

```
cd D:\Downloads\pip-9.0.1
```


运行如下命令进行安装:

```
python setup.py install
```

这时若无法正确安装，并提示错误`ImportError: No module named 'setuptools'`，请前往[https://pypi.python.org/pypi/setuptools](https://pypi.python.org/pypi/setuptools)下载`setuptools-38.4.0.zip (md5)`文件，解压后，切换至该安装包带有setup.py文件的目录下，运行如下命令进行安装:


```
python setup.py install
```

成功安装setuptools后，切换至`pip-9.0.1.tar.gz`文件夹下，重新运行如下命令，进行`pip`的安装。

```
python setup.py install
```

## 添加环境变量

在命令行输入`pip`后，若依旧提示未找到`pip`,则有必要配置系统环境变量。我们在环境变量`PATH`内添加路径`C:\Python35\Scripts;`，注意python版本号根据自己的情况设置。

## pip常用命令

命令行输入`pip`查询。

