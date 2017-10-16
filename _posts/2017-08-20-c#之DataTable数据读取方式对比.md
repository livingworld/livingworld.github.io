---
layout: post
title:  "c#之DataTable数据读取方式对比"
date:   2017-08-20 12:00:00 +0800
categories: c#
tag: [c#]
---   

# c#之DataTable数据读取方式对比
## 方法一
```
foreach (DataRow row in dt.Rows)
{
    v[j] = Convert.ToDouble(row["RV"]);
    z[j] = Convert.ToDouble(row["RZ"]);
    j++;
}
```
## 方法二
```
v = dt.AsEnumerable().Select(r => Convert.ToDouble(r["RV"])).ToArray();
z = dt.AsEnumerable().Select(r => Convert.ToDouble(r["RZ"])).ToArray();
```
## 方法三
```
for (int i = 0; i < count_vz; i++)
{
    v_vz[i] = Convert.ToDouble(dt.Rows[i]["RV"]);
    z_vz[i] = Convert.ToDouble(dt.Rows[i]["RZ"]);
}
```