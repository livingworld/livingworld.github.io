---
layout: post
title: "pyenv官方文档翻译"
date: 2018-02-04 16:00:00 +0800
categories: 其他
tag: []
---   

本文档翻译自官方文档[pyenv/pyenv: Simple Python version management](https://github.com/pyenv/pyenv)，英文水平有限，若有错漏之出，还望指正。

# 简单的Python版本管理:pyenv

pyenv让您轻松的切换多个版本的Python。它很简单、不显眼，并遵循UNIX的传统，使用单一用途的工具来完成一件事情。

这个项目从[rbenv](https://github.com/rbenv/rbenv)和[ruby-build](https://github.com/rbenv/ruby-build)分叉(fork)，并修改为Python。

![20180203-pyenv](http://p32dsli77.bkt.clouddn.com/20180203-pyenv.png)

### pyenv能...

- 让你在每个用户的基础上**修改全局Python版本**
- 为**每个项目的Pyhton版本**提供支持。
- 允许你使用环境变量覆盖Python版本
- **一次从多个Python版本**搜索命令。这可能对使用[tox](https://pypi.python.org/pypi/tox)跨Python版本进行测试有帮助。

### 与pythonbrew和pythonz相反，pyenv不..

- **取决于Python自身**。pyenv由纯shell脚本编写的。没有Python的引导问题。
- **需要加载到你的shell**。相反，pyenv的shim方法通过在`$PATH`添加一个目录来工作。
- **管理virtualenv**。当然，你可以自己创建[virtualenv](https://pypi.python.org/pypi/virtualenv)，或[pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)来自动化这个进程。

## 目录

- **如何工作**
    + 认识PATH
    + 认识Shims
    + 选择Python版本
    + 定位Python安装
- **安装**
    * 基本的Github查看
        + 升级
        + Homebrew在MAC OS X上
        + 高级配置
        + 卸载Python版本
- **命令参考**
- **开发**
    * [版本历史](https://github.com/pyenv/pyenv#version-history)
    * [许可证](https://github.com/pyenv/pyenv#license)

## 它如何工作？

 在高级别上，pyenv使用注入到`PATH`的shim可执行程序拦截Python命令，确定您的应用程序制定了哪个Python版本，并将您的命令传递给正确的Python安装。

### 认识PATH

 当你运行像`python`或`pip`的命令时，您的操作系统会搜索一个目录列表来查找具有该名称的可执行文件。这个目录位于名为`PATH`的环境变量中，列表中的每个目录都用冒号分隔:

 ```
 /usr/local/bin:/usr/bin:/bin
 ```

`PATH`中的目录是从左到右搜索的，因此在列表开头的目录中匹配的可执行文件优先于另一个目录。在这个例子中，首先搜搜`/usr/local/bin`目录，然后是`/usr/bin`，再然后是`/bin`。

### 认识Shims

pyenv通过在`PATH`前面插入一个*shims*目录来工作：

```
$(pyenv root)/shims:/usr/local/bin:usr/bin:/bin
```

通过一个称为`rehashing`的过程，pyenv在该目录中维护shims，以匹配每个已安装版本的Python中的每个Python命令-`pyenv`、`pip`等等。

Shims是轻量级的可执行文件，只需将您的命令传递到pyenv。所以在安装pyenv的时候，当你运行的时候，比如说，`pip`，你的操作系统将会执行如下操作:

- 在`PATH`中搜索名为`pip`的可执行文件。
- 在你的`PATH`开始处找到名为`pip`的pyenv shim。
- 运行名为`pip`的shim，它依次将命令传递给pyenv。

### 选择Python版本

当你执行一个shim时，pyenv通过从以下源读取它来确定使用哪个Python版本，顺序如下:

  1、`PYENV_VERSION`环境变量(如果指定)。你可以使用`pyenv shell`命令在当前shell会话中设置此环境变量。
  2.在当前目录中的特定于应用程序的`.python-version`(如果存在)。你可以使用`pyenv local`修改当前目录的`.python-version`文件。
  3.通过搜索每个父目录，找到第一个`.python-version`文件(如果有)，直到到达你的文件系统的根目录。
  4.全局的`$(pyenv root)/version`文件。你可以使用`pyenv global`命令修改这个文件。如果全局版本文件不存在，pyenv假设你想要使用"system"的 Python。(换句话说，如果pyenv不在你的`PATH`中，任何版本都可以运行)。

**注意**：你可以同时激活多个版本，包括多个版本的Python2或Python3。这允许并行使用Python2和Python3，并且需要像`tox`这样的工具。例如，要设置你的路径，首先使用你的`system`的Python和Python3(在这个例子中设置为2.7.9和3.4.2)，但是在您的`PATH`中也有Python3.3.4、3.2和2.5，你可以先`pyenv install`丢失的版本，然后设置`yenv global system 3.3.6 3.2 2.5`。在这一点上，应该能够使用`pyenv which`来找到每一个的完整可执行路径，例如`pyenv which python2.5`(应该显示`$(pyenv root)/versions/2.5/bin/python2.5`)，或`pyenv which python3.4`(应显示系统Python3的路径)。你可以在一个`.python-version`文件中指定多个版本，通过换行符或任何空格分割。

### 定位Python安装

一旦pyenv已经确定了你的应用程序已经指定哪个版本的Python，它会将该命令一起传递给相应的Python安装。

每个Python版本都安装在`$(pyenv root)/versions`下的自己的目录中。

例如，你可能已经安装了这些版本:

- `$(pyenv root)/versions/2.7.8/`
- `$(pyenv root)/versions/3.4.2/`
- `$(pyenv root)/versions/pypy-2.4.0/`

就pyenv而言，版本名称只是`$(pyenv root)/versions`中的目录。

### 管理虚拟环境

有一个名为[pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)的pyenv插件，它提供了各种功能来帮助pyenv用户管理由virtualenv或Anaconda创建的虚拟环境。由于这些虚拟环境的`activate`脚本依赖于用户交互式shell的可变`$PATH`变量，它将截取pyenv的shim式命令执行钩子。如果你有打算使用这些虚拟环境的计划，我们建议安装pyenv-virtual。

## 安装

若果你在Mac OS X，考虑[installing with Homebrew](https://github.com/pyenv/pyenv#homebrew-on-mac-os-x)。

### 自动化安装程序

参考我的其他项目:[https://github.com/pyenv/pyenv-installer](https://github.com/pyenv/pyenv-installer)

### 基本的Github查看

这将使你得到最新版本的pyenv，并很容易地fork和贡献任何改变回上游。

1.**检查你想要安装的pyenv的位置***。一个好的选择是`$HOME/.pyenv`(但你可以把它安装在别的地方)。

```
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

2.**定义环境变量`PYENV_ROOT`**以指向pyenv仓库被克隆的路径，并将`$PYENV_ROOT/bin`添加到您的`$PATH`以访问`pyenv`命令行的实用程序。

3.将**`pyenv init`添加到您的shell**以启用shims和autocompletion。请确保`eval "$(pyenv init -)"`放置在shell配置文件的末尾，因为它在初始化期间操作`PATH`。

```
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

**Zsh注意**：修改你的`~/.zshenv`文件而不是`~/.bash_profile`。
**Ubuntu和Fedora注意**:修改你的`~/.bashrc`文件而不是`~/.bash_profile`。

**一般警告**:有一些系统`BASH_ENV`变量配置为指向`.bashrc`。在这样的系统，你几乎可以肯定地把上面提到的行`eval "$(pyenv init -)"`放到`.bash_profile`中，而不是放在`.bashrc`中。否则你可能会观察到奇怪的行为，如`pyenv`进入了无限循环。详情请参阅[#264](https://github.com/pyenv/pyenv/issues/264)。

4.**重启你的shell来使路径的改变生效**。你现在可以开始使用pyenv了。

```
$ exec "$SHELL"
```

5.**将Python版本安装到`$(pyenv root)/versions`中**。例如，要下载并安装Python2.7.8，请运行:

```
$ pyenv install 2.7.8
```

**注意**:如果你需要通过配置选项来构建，请使用`CONFIGURE_OPTS`环境变量。
**注意**:如果你想使用代理(proxy)来下载，请使用`http_proxy`和`https_proxy`环境变量。
**注意**:如果你在安装pyhton版本时遇到问题，请访问wiki页面[Common Build Problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems)。

#### 升级

如果你已经按照上面的说明安装了pyenv，你可以随时用git升级你的安装。

要升级到pyenv的最新的开发版本，请使用`git pull`：

```
$ cd $(pyenv root)
$ git pull
```

要升级到一个特定版本的pyenv，请查看相应的标签:

```
$ cd $(pyenv root)
$ git fetch
$ git tag
v0.1.0
$ git checkout v0.1.0
```

### 卸载pyenv

pyenv的简单性使很容易暂时禁用它，或者从系统中卸载。

  1.要禁用pyenv管理您的Python版本，只需要从您的shell启动配置中移除`pyenv init`行。这将从PATH中移除pyenv shims目录，像`python`之类的未来的调用将执行系统的Python版本，如之前的pyenv。

  `pyenv`仍可通过命令行访问，但是你的Python应用不会受到版本切换的影响。

  2.要完全**卸载**pyenv，执行步骤(1)，然后移除它的根目录。这将**删除安装在`$(pyenv root)/versions/`目录下的所有Python版本**:
  ```
  rm -rf $(pyenv root)
  ```
  如果你已经使用安装包管理器安装了pyenv，作为最后一步执行pyenv安装包的移除。例如，对于Homebrew:
  ```
  brew uninstall pyenv
  ```

### Homebrew在MAC OS X上

你也可以使用MAC OS X的Homebrew安装包管理器来安装pyenv。

```
$ brew update
$ brew install pyenv
```

在未来要升级pyenv，使用`upgrede`而不是`install`。

然后按照上面[Basic GitHub Checkout](https://github.com/pyenv/pyenv#basic-github-checkout)之后的其他安装步骤，从#3开始("将`pyenv init`添加到你的shell中以启用shims和autocompletion")。

### 高级配置

跳过这一节，除非你必须知道你的shell配置文件中每一行都在做什么。`pyenv init`是跨越加载额外命令到你的shell中的唯一命令。来自rvm，你们中的一些人可能会反对这个想法。这里`pyenv init`实际上做的事情:

  - 1.**设置你的shims是路径**。这是pyenv正常工作的唯一要求。你可以通过预先将`$(pyenv root)/shims`到你的`$PATH`来手动执行此操作。
  - 2.**安装autocompletion**。这完全是可选的，但是非常有用。源`$(pyenv root)/completions/pyenv.bash`将设置。Zsh的用户也有一个`$(pyenv root)/completions/pyenv.zsh`。
  - 3.**重新调整shims**。有时你需要重建你的shim文件。在init中这样确保一切都是最新的。你总是可以手动运行`pyenv rehash`。
  - 4.**安装sh调度程序**。这一点也是可选的，但是允许pyenv和插件改变当前shell的变量，使得像`pyenv shell`这样的命令称为可能。sh调度程序不做任何像修改`cd`或破坏你的shell提示符那样疯狂的事儿，但如果由于某种原因，你需要`pyenv`成为一个真正的脚本而不是shell函数，你可以安全地跳过它。

要确切地看到到底发生了什么，运行`pyenv init -`

### 卸载Python版本

随着时间的推移，你将在您的`$(pyenv root)/versions`目录中积累Python版本。

要移除旧的Python版本，请使用`pyenv uninstall`命令自动化移除过程。

或者，只需`rm -rf`你想要移除的版本的目录。你可以使用`pyenv prefix`命令找到特定Python版本的目录，例如`pyenv prefix 2.6.8`。

## 命令参考

看[COMMANDS.md](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md)

## 环境变量

您可以通过以下设置来影响pyenv的运行方式：

|名称  |  默认 |描述|
|------|:------:|:------:|
|PYENV_VERSION   |  | 指定要使用的Python版本，另请参阅[pyenv shell](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-shell)|
|PYENV_ROOT | `~/.pyenv`   | 定义Python版本和shims所在的目录。也可以参见
 `pyenv root`|
|PYENV_DEBUG   |  |输出调试信息，如: `pyenv --debug <subcommand>`|
|PYENV_HOOK_PATH |[见wiki](https://github.com/pyenv/pyenv/wiki/Authoring-plugins#pyenv-hooks) | 用冒号分隔的路径搜索pyenv钩子。|
|PYENV_DIR |  `$PWD`  |  目录开始搜索 `.python-version` 文件。|
|PYTHON_BUILD_ARIA2_OPTS  | |  用于将其他参数传递给[aria2](https://aria2.github.io/).如果 `aria2c` 二进制在PATH上可用, pyenv使用`aria2c`而不是`curl`或`wget` 来下载Python源代码。如果您的网络连接不稳定，您可以使用此变量来指示`aria2`加速下载。在大多数情况下，你只需要使用`-x 10 -k 1M`作为`PYTHON_BUILD_ARIA2_OPTS`环境变量的值。

## 开发

pyenv源代码[托管在GitHub商行](https://github.com/pyenv/pyenv)。它干净、模块化，易于理解，即使你不是一个shell黑客。

测试使用[Bats](https://github.com/sstephenson/bats)执行:

```
$ bats test
$ bats/test/<file>.bats
```

请随时在issue跟踪器上提交pull请求和文件bug。
。
















