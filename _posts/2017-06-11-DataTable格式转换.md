---
layout: post
title: "DataTable格式转换"
date: 2017-06-11 12:00:00 +0800
categories: 其他
tag: [天地图]
---

## datatable格式转换
由于想要对datatable中数据进行替换或者特定位置插入插入字符，但提示是Int32，而非string，网上检索得来的答案是无法修改column类型在DataTable中，可以采用clone方式将DataTable全部转换为string类型。
```
DataTable dtClone = dt.Clone(); //just copy structure, no data
for (int i = 0; i < dtClone.Columns.Count; i++)
{
    if (dtClone.Columns[i].DataType != typeof(string))
        dtClone.Columns[i].DataType = typeof(string);
}
//注意dt
foreach (DataRow dr in dt.Rows)
{
    dtClone.ImportRow(dr);
}
```
参考：
    - [Clone DataTable](https://stackoverflow.com/questions/22970418/copy-c-sharp-datatable-and-convert-all-values-to-string)
    - [DataTable.Clone Method ()](https://msdn.microsoft.com/en-us/library/system.data.datatable.clone.aspx)