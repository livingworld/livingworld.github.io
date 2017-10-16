---
layout: post
title: "C#和Javascript交互"
date: 2017-04-24 12:00:00 +0800 
categories: c#
tag: [asp.net,javascript]
---   

# 20170424
## C#和Javascript交互

在asp.net开发中，经常会用到后台和前台的交互，就此总结了一点c#和javascript相互操作的方法。

### 在后台c#代码中调用jacascript的方法

- javascript代码：

```
<script type="text/javascript" language="javascript">
    function test() {
        alert("oec2003");
        return false;
    }
</script>
```

- C#代码：

```
protected void Button1_Click(object sender, EventArgs e)
{
    ClientScript.RegisterStartupScript(this.GetType(), "clear", "<script>test()</script>");
}
```

### javascript中调用c#方法

如果c#中的方法有返回值，可以用下面方法

- C#代码

```
public string GetAuthStatus()
{
   ViewState["Auth"] = "Red";
   return ViewState["Auth"].ToString();
}
```

- javascript代码

```
<script type="text/javascript" language="javascript">
    function getAuth() {
        var authStatus = "<%=GetAuthStatus()%>";
        return authStatus;
    }
</script>
```

如果在javascript调用的c#方法没有返回值，可以在一面中放一个button，然后在button的单击事件中去写想做的事情，在客户端的脚本中写下如下代码就可以了:

```
    document.all("button1").click();
```

## iframe的使用

如何使用iframe将分支界面嵌入到主界面中，且仅是将其缩小比例尺放入？

```
<iframe id="mainFrame" name="mainFrame" runat="server" frameborder="0" scrolling="no" style="width:100%;height:50%;" src="/chap1.aspx"></iframe>  
```

iframe大小改变可以，但里面引用的外域的页面里面的flash是没办法对其进行操作。

```
<iframe src="http://www.mlwl.cc" style="zoom:50%" width="1024" height="768"></iframe>

```
