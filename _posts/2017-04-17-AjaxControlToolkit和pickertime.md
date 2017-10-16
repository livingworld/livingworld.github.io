---
layout: post
title: "AjaxControlToolkit和pickertime"
date: 2017-04-17  12:00:00 +0800
categories: c#
tag: [asp.net]
---

## calender控件的使用

html代码：
```
<%@ Page Language="C#" CodeFile="calendar.aspx.cs" Inherits="calendar" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>无标题页</title>
</head>
<body>
    <form id="form1" runat="server">
    <div>
        <asp:Panel ID="Panel1" runat="server" Height="27px" Width="540px">
            <asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
            &nbsp;&nbsp;&nbsp;
            <asp:Button ID="Button1" runat="server" OnClick="btnOnClick" Text="选择" />
            <div id="LayerCC" style="left: 155px; width: 189px; position: absolute; top: 55px;
                height: 191px; 
                <asp:Calendar ID="Calendar1" runat="server" BackColor="White" BorderColor="#3366CC"
                    BorderWidth="1px" CellPadding="1" Font-Names="Verdana" Font-Size="8pt" ForeColor="#003399"
                    Height="200px" OnSelectionChanged="Calendar1_SelectionChanged" Width="220px">
                    <SelectedDayStyle BackColor="#009999" Font-Bold="True" ForeColor="#CCFF99" />
                    <SelectorStyle BackColor="#99CCCC" ForeColor="#336666" />
                    <WeekendDayStyle BackColor="#CCCCFF" />
                    <TodayDayStyle BackColor="#99CCCC" ForeColor="White" />
                    <OtherMonthDayStyle ForeColor="#999999" />
                    <NextPrevStyle Font-Size="8pt" ForeColor="#CCCCFF" />
                    <DayHeaderStyle BackColor="#99CCCC" ForeColor="#336666" Height="1px" />
                    <TitleStyle BackColor="#003399" BorderColor="#3366CC" BorderWidth="1px" Font-Bold="True"
                        Font-Size="10pt" ForeColor="#CCCCFF" Height="25px" />
                </asp:Calendar>
            </div>
        </asp:Panel>
    </div>
    </form>
</body>
</html>
```
参考代码C#部分
```
using System;
using System.Collections;
using System.Configuration;
using System.Data;
using System.Linq;
using System.Web;
using System.Web.Security;
using System.Web.UI;
using System.Web.UI.HtmlControls;
using System.Web.UI.WebControls;
using System.Web.UI.WebControls.WebParts;
using System.Xml.Linq;

public partial class calendar : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {

    }
    protected void btnOnClick(object sender, EventArgs e)
    {
        Calendar.Visible = !Calendar.Visible;

    }
    protected void Calendar1_SelectionChanged(object sender, EventArgs e)
    {
        TextBox1.Text = Calendar1.SelectedDate.ToString("yyyy-MM-dd");
        Calendar.Visible = false;


    }
}
```

## AjaxControlToolkit.dll插件的使用

这个代码控件只能够将时间精确到day，无法精确到hour。
```
  <div>
        <asp:ScriptManager ID="ScriptManager1" runat="server"></asp:ScriptManager>
        开始时间：<asp:TextBox ID="DateTextBox1" runat="server"  />
        <asp:Image ID="Image1" runat="server" ImageUrl="../Image/calendar.png" />
        <cc1:CalendarExtender ID="CalendarExtender1" runat="server" TargetControlID="DateTextBox1" PopupButtonID="Image1" Format="MM'/'dd'/'yyyy HH':'mm':'ss">
        </cc1:CalendarExtender>
        <br/>
        终止时间：<asp:TextBox ID="DateTextBox2" runat="server"  />
        <asp:Image ID="Image2" runat="server" ImageUrl="../Image/calendar.png" />
       <cc1:CalendarExtender ID="CalendarExtender2" runat="server" TargetControlID="DateTextBox2" PopupButtonID="Image2">
        </cc1:CalendarExtender>
    </div>
```

### 解决方法：
- Format="yyyy-MM-dd hh:mm:ss"，只是对这种方法能否进行有效拓展存疑。
- 切该方法时间默认为12:00,切直接点击textbox即可选择。
- 在c#脚本中可对功能进行拓展，例如如何将时间与数据库查询数据进行关联等，这才是最终目的。

```
    <div>
        <asp:ScriptManager ID="ScriptManager1" runat="server"></asp:ScriptManager>
        开始时间：<asp:TextBox ID="DateTextBox1" runat="server"/>
        <asp:Image ID="Image1" runat="server" ImageUrl="../Image/calendar.png" />
        <cc1:CalendarExtender ID="CalendarExtender1" runat="server" TargetControlID="DateTextBox1" PopupButtonID="CalendarExtender1" Format="yyyy-MM-dd hh:mm:ss">
        </cc1:CalendarExtender >
        <br/>
        终止时间：<asp:TextBox ID="DateTextBox2" runat="server"  />
        <asp:Image ID="Image2" runat="server" ImageUrl="../Image/calendar.png" />
       <cc1:CalendarExtender ID="CalendarExtender2" runat="server" TargetControlID="DateTextBox2" PopupButtonID="CalendarExtender2" Format="yyyy-MM-dd hh:mm:ss">
        </cc1:CalendarExtender>
    </div>
```

### 参考：

- [Any+Time DatePicker/TimePicker AJAX Calendar Widget](https://www.ama3.com/anytime/)

此外可采用js脚本增补当前时间：

```
    OnClientDateSelectionChanged="dateselect"
    function dateselect(ev)
    {
        var calendarBehavior1 = $find("Calendar1");
        var d = calendarBehavior1._selectedDate;
        var now = new Date();
        calendarBehavior1.get_element().value = d.format("MM/dd/yyyy") + " "+now.format("HH:mm:ss")
    }
```