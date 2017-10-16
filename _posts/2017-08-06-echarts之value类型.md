---
layout: post
title: "echarts之value类型"
date: 2017-08-06 12:00:00 +0800
categories: 其他
tag: [javascript]
---

## echarts
### 图表类型
- echarts图表类型分为三类：类目型、数值型、时间型
- 若要考虑横坐标的自定义宽度，则选择数值型或者时间型。此时数据导入的结构变为了[[x,y],[x2,y2],...]
-
### echarts格网清除
代码如下：
```
xAxis: [
        {
            splitLine:{show: false},
        }
    ]
```

### echarts中坐标处插值小数点处理
```
    params.value.toFixed(3) ## 精确到小数点后三位
```

## 取整数
- 截取整数
```
var a = 160.58;
var b = parseInt(a);//截取整数部分
alert(b);
```
- 四舍五入
```
var c= Math.round(a);//四舍五入
alert(c);
```

## echarts中数值型拾取点格式化操作
```
axisPointer:{
            show: true,
            type : 'cross',
            lineStyle: {
                type : 'dashed',
                width : 1
            }
        },
        formatter : function (params) {
            return params.seriesName + ' : [ '
                   + params.value[0] + ', ' 
                   + params.value[1] + ' ]';
        }
```

## 参考
- [echarts2：双数值轴折线](http://echarts.baidu.com/echarts2/doc/example/line7.html)