---
layout: post
title: "DataTables红绿色和nested tab"
date: 2017-06-14 12:00:00 +0800
categories: 其他
tag: [javascript]
---

## DataTables红绿色
.cs文件中，对应表替换数字为对应涨、落、平字符。
.cs
```
for (int i = 0;i < dt.Rows.Count; i++)
{
    if(dt.Rows[i]["库水水势"].ToString() == 4.ToString())
    {
        dt.Rows[i]["库水水势"] = "落";
    }
    else if(dt.Rows[i]["库水水势"].ToString() == 5.ToString())
    {
        dt.Rows[i]["库水水势"] = "涨";
    }
    else if (dt.Rows[i]["库水水势"].ToString() == 6.ToString())
    {
        dt.Rows[i]["库水水势"] = "平";
    }
}
```
.js和.css文件中设置对应字符的颜色，注意js文件中`columnDefs`的设置，假若不放置则会报错。切记，不要使用`<p></p>`，否则无法调整高度。

.css
```
<style type="text/css">
    .negative {
    color:rgba(255,0,0,1.00);
    }
    .positive {
    color:rgba(0,255,0,1.00);
    }
</style>
```
.js
```
$('#example').DataTable({
    ajax: "data.json",
    "columnDefs": [{
        "defaultContent": "-",
        "targets": "_all"
    }],
    columns: [
        { data: "站名" },
        { data: "站址" },
        { data: "时间" },
        { data: "入库流量" },
        { data: "出库流量" },
        { data: "库水位" },
        {
            data: "库水水势", render: function (name, type, row) {
                if (name == '落') {
                    return '<b class="positive">' + name + '</b>';
                }
                else if (name == '涨') {
                    return '<b class="negative">' + name + '</b>';
                }
            }
        },
        { data: "蓄水量" }
    ]
});
```

### 参考
- [DataTables warning: Requested unknown parameter ](https://stackoverflow.com/questions/16539578/datatables-warning-requested-unknown-parameter-0-from-the-data-source-for-row)
- [jQuery DataTables fnrender with objects](https://stackoverflow.com/questions/6518989/jquery-datatables-fnrender-with-objects)

## tab and subtab
解决方案参考：
- [参考](http://jsfiddle.net/bpJUv/1/)
- [最佳答案](http://jsfiddle.net/TNyb6/)