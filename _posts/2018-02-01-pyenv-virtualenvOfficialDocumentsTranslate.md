---
layout: post
title: "pyenv-virtualenv官方文档翻译"
date: 2018-02-04 16:00:00 +0800
categories: 其他
tag: []
---   

本文档翻译自官方文档[pyenv/pyenv-virtualenv: a pyenv plugin to manage virtualenv (a.k.a. python-virtualenv)](https://github.com/pyenv/pyenv-virtualenv)，英文水平有限，若有错漏之出，还望指正。

# pyenv-virtualenv

pyenv-virtualenv是一个[pyenv](https://github.com/pyenv/pyenv)插件，提供了在类UNIX系统上管理Python的virtualenvs和conda环境的功能。

(注意:如果你是[virtualenvwrapper](https://pypi.python.org/pypi/virtualenvwrapper))的现有用户，并且你喜欢，则[pyenv-virtualenvwrapper ](https://github.com/pyenv/pyenv-virtualenvwrapper)可以帮助你(另外)管理你的virtualenvs。)

## 安装

### 作为一个pyenv插件安装

这里将把pyenv-virtualenv的最新的开发版本安装到`$(pyenv root)/plugins/pyenv-virtualenv`目录。

**重要说明**:如果你将pyenv安装到非标准的目录中，请确保将此仓库克隆到任何你安装`plugins`目录的地方。

从该目录中可以:

- 查看一个特定的release标签。
- 通过运行`git pull`获得最新的开发版本来下载最新的改变。

1.**检查pyenv-virtual到插件目录**

```
$ git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

2.(可选)添加`pyenv virtual-init`添加到您的shell以启用virtualenvs的自动激活。这是完全可选的，但非常有用。请参阅下面的`Activate virtualenv`。

```
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile
```

**Fish shell注意**:把这个添加到你的`~/.config/fish/config.fish`

```
status --is-interactive; and source (pyenv virtualenv-init -|psub)
```

**Zsh注意**:修改你的`~/.zshenv`文件而不是`~/.bash_profile`。
**Pyenv注意**:如果您还没有这样做，你可能还需要添加`eval "$(pyenv init -)"`到您的配置文件。

3.重启的你shell来启用pyenv-virtualenv

```
$ exec "$SHELL"
```

### 用Homebrew安装(对于OS X用户)

Mac OS X用户可以使用[Homebrew](https://brew.sh/)安装包管理器来安装pyenv-virtualenv。这将使您可以访问`pyenv-virtualenv`的命令。如果你安装了pyenv，你也可以使用`pyenv virtualenv`命令。

*如果你使用Homebrew安装了pyenv，这是推荐的安装方法*。

```
$ brew inistall pyenv-virtualenv
```

或者，如果您想要安装最新的开发版本:

```
$ brew install --HEAD pyenv-virtualenv
```

安装之后，你仍染需要添加

```
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

到您的配置文件(如注意事项中所述)。你能做一次。

## 用法
### 与pyenv一起使用virtualenv

要为pyenv使用的Python版本创建virtualenv，请运行`pyenv virtualenv`，指定您所需的Python版本和virtualenv目录的名称。例如:

```
$ pyenv virtualenv 2.7.10 my-virtual-env-2.7.10
```

将在名为`my-virtual-env-2.7.10`的文件夹下中创建一个基于Python 2.7.10的virtualenv。

### 从当前版本创建virtualenv

如果只有一个参数给`pyenv virtualenv`，virtualenv将用根据当前pyenv Python版本
的给定名称创建。

```
$ pyenv version
3.4.3 (set by /home/yyuu/.pyenv/version)
$ pyenv virtualenv venv34
```

### 现有的virtualenvs列表

`pyenv virtualenvs`向您显示现有的virtualenvs和`conda`环境的列表。

```
$ pyenv shell venv34
$ pyenv virtualenvs
  miniconda3-3.9.1 (created from /home/yyuu/.pyenv/versions/miniconda3-3.9.1)
  miniconda3-3.9.1/envs/myenv (created from /home/yyuu/.pyenv/versions/miniconda3-3.9.1)
  2.7.10/envs/my-virtual-env-2.7.10 (created from /home/yyuu/.pyenv/versions/2.7.10)
  3.4.3/envs/venv34 (created from /home/yyuu/.pyenv/versions/3.4.3)
  my-virtual-env-2.7.10 (created from /home/yyuu/.pyenv/versions/2.7.10)
* venv34 (created from /home/yyuu/.pyenv/versions/3.4.3)
```

每个virtualenv有两个条目，而较短的一个只是一个符号链接。

### 激活virtualenv

某些外部工具(例如[jedi](https://github.com/davidhalter/jedi))可能需要你`activate` virtualenv和`conda`环境。

如果在您的shell中配置了`eval "$(pyenv virtualenv-init -)"`，`pyenv-virtualenv`将自动激活/停用virtualenvs进入/离开目录，其中包含一个`.python-version`文件，该文件包含有效的虚拟环境的名称，如`pyenv virtualenvs`的输出中所示(例如，venv34或3.4.3/envs/venv34以上这些例子)。pyenv使用`.python-version`文件用于表示本地的Python版本，可以用`pyenv local`命令来创建和删除。

你也可以手动激活或停用pyenv virtualenv:
```
pyenv activate <name>
pyenv deactivate
```

### 删除存在的virtualenv
移除在`$(pyenv root)/versions`和`$(pyenv root)/versions/{version}/envs`中的目录将会删除virtualenv，或者运行:

```
pyenv uninstall my-virtual-env
```

### virtualenv和venv

有一个`venv`模块可用于CPython 3.3或更新的版本。它提供了一个可执行模块`venv`，它是`virtualenv`的后继版本，默认分发。

如果`pyenv-virtualenv`可用，并且`virtualenv`命令不可用，则使用`python -m venv`

### Anaconda 和 Miniconda

你可以通过`conda create`管理`conda`环境，像标准的Anaconda/Miniconda安装一样管理conda环境。要使用这些环境，可以使用`pyenv activate`和`pyenv deactivate`。

```
$ pyenv version
miniconda3-3.9.1 (set by /home/yyuu/.pyenv/version)
$ conda env list
# conda environments:
#
myenv                    /home/yyuu/.pyenv/versions/miniconda3-3.9.1/envs/myenv
root                  *  /home/yyuu/.pyenv/versions/miniconda3-3.9.1
$ pyenv activate miniconda3-3.9.1/envs/myenv
discarding /home/yyuu/.pyenv/versions/miniconda3-3.9.1/bin from PATH
prepending /home/yyuu/.pyenv/versions/miniconda3-3.9.1/envs/myenv/bin to PATH
$ python --version
Python 3.4.3 :: Continuum Analytics, Inc.
$ pyenv deactivate
discarding /home/yyuu/.pyenv/versions/miniconda3-3.9.1/envs/myenv/bin from PATH
```

如果`conda`可用，`pyenv virtualenv`将使用`conda create`来创建环境。

```
$ pyenv version
miniconda3-3.9.1 (set by /home/yyuu/.pyenv/version)
$ pyenv virtualenv myenv2
$ conda env list
# conda environments:
#
myenv                    /home/yyuu/.pyenv/versions/miniconda3-3.9.1/envs/myenv
myenv                    /home/yyuu/.pyenv/versions/miniconda3-3.9.1/envs/myenv2
root                  *  /home/yyuu/.pyenv/versions/miniconda3-3.9.1
```

你可以使用像`miniconda3-3.9.1/envs/myenv`这样的版本来指定`conda`环境作为pyenv的一个版本。

```
$ pyenv version
miniconda3-3.9.1 (set by /home/yyuu/.pyenv/version)
$ pyenv shell miniconda3-3.9.1/envs/myenv
$ which python
/home/yyuu/.pyenv/versions/miniconda3-3.9.1/envs/myenv/bin/python
```

### 特殊的环境变量

你可以设置一些环境变量来控制pyen-virtualenv。

- `PYENV_VIRTUALENV_CACHE_PATH`，如果设置，指定一个目录用于缓存下载的安装包文件。
- `VIRTUALENV_VERSION`，如果设置，则强制pyenv-virtualenv安装所需的virtualenv版本。如果`virtualenv`尚未安装，`pyenv-virtualenv`将尝试安装给定版本的virtualenv。
- `GET_PIP`，如果设置并且`venv`优先于`virtualenv`，则使用指定位置的`get_pip.py`。
- `GET_PIP_URL`，如果设置并且venv优先于virtualenv，则从指定的URL下载`get_pip.py`。
- `PIP_VERSION`，如果设置并且`venv`优先于`virtualenv`，则安装指定的pip版本。

### 版本历史

请参阅[CHANGELOG](https://github.com/pyenv/pyenv-virtualenv/blob/master/CHANGELOG.md)。

### License


>Copyright (c) 2015 Yamashita, Yuu
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

>The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

>THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.