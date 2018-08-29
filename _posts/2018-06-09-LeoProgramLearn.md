---
layout: post
title: "leo 编程学习"
date: 2018-06-09 17:22:00 +0800 
categories: leo
tag: [leo]
---   

## 前言

这个话题非常有趣了，因为打开最新版的官方文档，对这方面的介绍太少了。其实我们可以从[旧的官方文档版本](http://davy39.github.io/leo-editor/tutorial-programming.html)中找到相应的参考，并且也可以在[Introduction to Leo for Programmers - YouTube](https://www.youtube.com/watch?v=WkUbJAuFfI4&t=294s)中找到视频演示。

## 小试身手

根据旧文档提示，新建 `@file myfile.py` 节点，节点正文为：
```
@language python
@tabwidth -4
<< docstring >>
<< imports >>
<< class demo >>
@others
if __name__ == '__main__':
    test = demo()
    test.draw()
```

之后，新建子节点 `<< imports >>` 、 `<<docstring >>` 和 `<< class demo >>`

子节点 `<< imports >>` 正文：
```
import matplotlib
import matplotlib.pyplot as plt
import numpy as np
```

子节点 `<<docstring >>` 正文：
```
'''
This is the docstring for this python module.
'''
```


子节点 `<< class demo >>` 正文：

```
class demo:
    '''A docstring.'''
    @others
```

创建 `<< class demo >>` 的子节点 `draw`，`draw` 节点正文:
```
@language python
def draw(self):
    # Data for plotting
    t = np.arange(0.0, 2.0, 0.01)
    s = 1 + np.sin(2 * np.pi * t)
    # Note that using plt.subplots below is equivalent to using
    # fig = plt.figure() and then ax = fig.add_subplot(111)
    fig, ax = plt.subplots()
    ax.plot(t, s)
    ax.set(xlabel='time (s)', ylabel='voltage (mV)',
      title='About as simple as it gets, folks')
    ax.grid()
    fig.savefig("test.png")
    plt.show()
```

最后，通过 CTRL + B 运行 `@file myfile.py` 节点，即通过定义的 deo 类调用 draw 函数来绘制图形。

提纲结构：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-Programming-With-Leo-1.png" width="40%" heigint="40%" align="middle" alt=""/>

运行结果：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-Programming-With-Leo-2.png" width="80%" heigint="80%" align="middle" alt=""/>

## 参考

- [Programming with Leo — Leo 5.0 documentation](http://davy39.github.io/leo-editor/tutorial-programming.html)
- [Introduction to Leo for Programmers - YouTube](https://www.youtube.com/watch?v=WkUbJAuFfI4&t=294s)

## CHANGELOG

- 20180609
