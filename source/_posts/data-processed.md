---
title: Excel数据处理之VBA初尝试
tags: [Excel, 数据处理, VBA]
categories: [Excel]
date: 2017-10-13 10:04:02
---
## 前言：
要说学东西最快的方式是什么，那问题驱动法必是其中最有效的方式之一了；最近有同学需要帮忙处理一个Excel数据表格，闲着没事就帮忙试一下。数据特征为按时间记录的多列数据（具体什么数据就不描述了），但是由于某些原因中间有一些数据缺失，主要体现为某天或者某几天的数据缺失了；数据处理要求为把缺失的数据按照一定规则补上。

<!--more-->
## 分析问题
数据表体现为时间跨度长，数据量特别大，首先排除手动解决的方案；依据数据特征，解决的关键是如何找到并补上缺失的数据项，当然就是根据时间了，但是有一个难点在于时间序列不同于数字系列，时间序列规律不是单一的，比如每月的天数有28,29,30,31；然后某月末过来是下月的1号，还有跨年的也要考虑，好像是很有规律，但是并不容易解决。本来以为用Python应该很容易解决了，但是一时间想不到用什么好的办法去处理时间这个点，不借助已有的库处理起来比较麻烦，还得转换时间格式；要是能有一个函数能得出两个时间点之差那就很好办了。
## 解决方案
因为数据表时间格式是Dec 11,2017；想到excel可以将时间格式任意转换，继而联想到应该有时间处理相关的函数，google搜了下，确实有想要的时间处理函数，经过了解，于是决定用VBA(Visual Basic Application)解决这个问题，这种语言主要是微软为Microsoft Office提供的应用程序，很方便的调用各种接口来处理Microsoft Office文件。这种优点就不多说了，就这里可以编程处理这个excel，还提供了各种便捷的接口，那岂不是可以为所欲为了。嗖，花了点时间看了下VBA的文档，由于以前学过一点VB，学起来还是蛮快的；好的还是回到前面的问题，一开始我在找微软的excel内置函数里确实找到一个可以得到两个时间点之差的函数，然后用vba调用相关接口函数即可，但是进一步了解到这个时间函数存在兼容性问题，接口参数对于本问题也不太友好，遂找了下vba本身的时间函数，DateDiff完美适用，然后得到时间差之后用DateAdd(真的是想什么来什么，一看文档就有这个函数，加上前面的DateDiff完美契合本问题)补上缺失的时间，再根据规则补上确实的数据，完美；等一下，好像还有一点小问题，时间顺序需要在注意分情况，样通用性就更好一点，最后来看完整代码，核心代码不超过10行：

``` basic
Sub fixdata()

Dim sheet As Worksheet
Set sheet = Worksheets(1)
Const startRow As Integer = 4

Dim sortByTimeAscending As Boolean
sortByTimeAscending = True

Dim endRow, endCol As Long
Dim prevDate, nextDate As Date
Dim temp, i, j

endRow = sheet.Cells(Rows.Count, 1).End(xlUp).Row
endCol = sheet.Cells(startRow, Columns.Count).End(xlToLeft).Column
Dim flag As Long
flag = DateDiff("d", sheet.Cells(startRow, 1).Value, sheet.Cells(startRow + 1, 1).Value)
If flag < 0 Then
    sortByTimeAscending = False
End If

For i = endRow To (startRow + 1) Step -1
    prevDate = sheet.Cells(i - 1, 1).Value
    nextDate = sheet.Cells(i, 1).Value
    temp = Abs(DateDiff("d", prevDate, nextDate)) - 1
    If temp > 0 Then
        Dim days
        For j = 1 To temp
            dataPrev = sheet.Cells(i - 1, 1).Value
            sheet.Cells(i, 1).EntireRow.Insert
            If sortByTimeAscending Then
                days = -j
            Else
                days = j
            End If
            sheet.Cells(i, 1) = DateAdd("d", days, nextDate)
            Dim col
            For col = 2 To endCol
                sheet.Cells(i, col) = (sheet.Cells(i - 1, col).Value + sheet.Cells(i + 1, col).Value) / 2
            Next col
        Next j
    End If
Next i

End Sub

```

## 最后
在excel运行以上代码，等待几秒后数据就全部处理好了，完美！有时候帮别人做点东西可能自己也会有不少的收获呢，我以后遇到类似问题就可以用vba解决了，然后如果很多表格或者重复的劳动也可以用代码交给机器去做了，又快又好岂不乐哉。关于vba如何在excel里面使用，随便搜索下就知道了，还有一些API文档在参考文献中会给出，你也试试吧。

## 参考文献
* [MSDN](https://msdn.microsoft.com/en-us/vba/vba-excel)
* [VBA教程](http://www.yiibai.com/vba/)

