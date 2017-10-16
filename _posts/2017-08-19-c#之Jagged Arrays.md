---
layout: post
title: "c#之Jagged Arrays"
date: 2017-08-19 12:00:00 +0800
categories: c#
tag: [asp.net]
---   

# Jagged Arrays 
在将一维数组赋值给多维数组时，往往需要进行循环遍历，是否有更加的替代方案，如简单的赋值即可实现呢？在探索c#数组类型后，发现[Jagged Arrays (C# Programming Guide) | Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/arrays/jagged-arrays)数组类型能够达到想要的效果，且数组长度大小可不一致，代码如下：
```
int[][] jaggedArray = new int[3][];
jaggedArray[0] = new int[5];
jaggedArray[1] = new int[4];
jaggedArray[2] = new int[2];

jaggedArray[0] = {1,1,1,1,1}//即可实现数组的赋值

 int[][] jaggedArray2 = new int[][] 
{
    new int[] {1,3,5,7,9},
    new int[] {0,2,4,6},
    new int[] {11,22}
};
```

注意：
- 若数组未定义大小，则不能够通过索引`[j]`的方式赋值，例如:
```
int[][] jaggedArray = new int[3][];
jaggedArray[1][1] = 0;//该方式为错误方式，除非是整个数组赋值。
```
- 由于jaggedArray2是由其他数组拼接而成，在改变其子数组时，会导致jaggedArray2数组内容发生改变(待求证)。

- 数组定义大小有特定方式。
    + 利用LINQ中Enumuerable.Range方式
    + https://stackoverflow.com/questions/1099731/initialize-a-jagged-array-the-linq-way
