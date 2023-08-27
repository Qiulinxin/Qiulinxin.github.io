---
title: 导入 CSV 文件至 Excel 及 Excel 文件的拆分合并操作
categories: [note]
comments: true
---

# 导入 `CSV` 文件至 `Excel` 及 `Excel` 文件的拆分合并操作

## 说明

这篇博客会记录我使用 `VBA` 在 `Excel` 中编写的三个 **宏**[^1]，分别实现了以下三个功能：

1.   将 `CSV` 文件批量导入至 `Excel`；
2.   将 `Excel` 文件按照 **工作表**[^2] 拆分为单个的 `Excel` 文件；
3.   将多个 `Excel` 文件合并为一个 `Excel` 文件。

## 导入 `CSV` 文件至 `Excel`

这个宏是在完成课程设计的过程中编写的，昨天晚上[^3]将这个宏做了一些修改和整理。

**代码**：`InputCVStoExcel`

```vb
Option Explicit

'读取文件名称
'返回集合类型
'包含了文件后缀名
Function getFileName(Optional ByVal fileType As String = "csv") As Collection

Dim nameColl As Collection
Dim path, fileName As String

Set nameColl = New Collection

fileType = "\*." & fileType
path = ThisWorkbook.path & fileType
fileName = Dir(path)

'读取文件名称并存入集合
Do While fileName <> ""
    nameColl.Add Item:=fileName
    fileName = Dir
Loop

Set getFileName = nameColl

End Function

'处理读取的文件名称
'去除文件后缀名仅返回文件的名称
'以字符串类型返回
Function getName(ByVal fileName As String, _
                Optional ByVal fileType As String = "csv") As String

Dim nameLen As Long
Dim name As String

'计算文件名称长度，减去后缀名cvs和.
nameLen = Len(fileName) - (Len(fileType) + 1)

name = Mid(String:=fileName, Start:=1, length:=nameLen)

getName = name

End Function

'判断工作表是否存在
'返回布尔类型
Function IsWorksheetExist(ByVal sheetName As String) As Boolean

Dim ws As Worksheet

IsWorksheetExist = False

For Each ws In ThisWorkbook.Worksheets
    If ws.name = sheetName Then IsWorksheetExist = True
Next ws

End Function

'删除工作表
Function deleteWorkSheet(ByVal sheetName As String)

Dim ws As Worksheet

Application.DisplayAlerts = False

Set ws = ThisWorkbook.Worksheets(sheetName)

ws.Delete

Application.DisplayAlerts = True

End Function

'将外部CSV文件导入Excel
Function inputCSV(ByVal fileName As String)

Dim wb As Workbook
Dim Tws, ws As Worksheet

fileName = "\" & fileName

Set Tws = ThisWorkbook.Worksheets(1)

'打开外部CSV文件
Set wb = Workbooks.Open(fileName:=ThisWorkbook.path & fileName)

'wb.Worksheets(1).Copy before:=Tws
wb.Worksheets(1).Copy after:=Tws

wb.Close

End Function

'主过程
'将外部的CSV文件导入至Excel中
Sub main()

Dim name As String
Dim fileName As Variant

'关闭屏幕闪动
Application.ScreenUpdating = False

For Each fileName In getFileName
    
    '对读取的文件名称进行处理
    '去除文件后缀名.csv，仅保留文件名称
    name = getName(fileName:=fileName)
    
    '若Excel中已存在与外部CVS文件同名的工作表
    '则删除Excel中的同名工作表
    '重新导入外部CSV文件
    If IsWorksheetExist(sheetName:=name) Then
        Call deleteWorkSheet(sheetName:=name)
        Call inputCSV(fileName:=fileName)
    Else
        Call inputCSV(fileName:=fileName)
    End If
    
Next fileName

'自动保存Excel
ActiveWorkbook.Save

Application.ScreenUpdating = True

'完成文件导入后弹出提示对话框
Call MsgBox(prompt:="已将CSV文件全部导入Excel!", Buttons:=vbOKOnly)

End Sub

```

**实现思路**：

1.   获取所有 `CSV` 文件包含了文件后缀名 `.csv` 的完整名称，并以 **集合** 数据结构返回文件名称；
2.   处理获取到的 `CSV` 文件名称，去除文件后缀名 `.csv`；
3.   比对 `Excel` 中是否存在与 `CSV` 文件同名的工作表；
4.   若存在，则先删除 `Excel` 中的工作表，再导入 `CSV` 文件至 `Excel`；若不存在，则直接导入 `CSV` 文件至 `Excel`。避免在多次执行该程序后，将同一个 `CSV` 文件重复导入 `Excel`；支持更新 `CSV` 文件后 `Excel` 文件同样可以在执行一次程序后实现更新。

程序导入 `CSV` 文件后，工作表的排序是随机混乱的。

**小结**：

1.   构造了一个函数用于获取指定文件类型的名称，并以 **集合** 数据结构返回所有文件的名称；
2.   构造了一个函数用于从包含文件后缀名的字符串中获取文件名称；
3.   构造的函数是以 **集合** 或 **数组** 等作为返回值时，要使用 `Set FunctionName = myArray` 的形式返回值；仅以单个的 **值** 作为返回值时，使用 `FunctionName = Value` 即可。

## 拆分 `Excel` 文件

剩下的两个宏是在学习 `Excel VBA` 的过程中，自己随手写的两个例子，今天上午[^4]做了一些整理和优化。

**代码**：`SplitWorkbooks`

```vb
Option Explicit

'拆分Excel文件
'每执行一次该程序，
'都会对已拆分的文件进行覆写
Sub splitWorkbook()

Dim wb As Workbook
Dim ws As Worksheet

Application.DisplayAlerts = False
Application.ScreenUpdating = False

For Each ws In Worksheets
    Set wb = Workbooks.Add
    
    '将当前工作表复制到新建的工作簿中
    ws.Copy before:=wb.Worksheets(1)
    
    '保存新建的工作簿
    wb.SaveAs ThisWorkbook.Path & "\" & ws.Name & ".xlsx"
    
    '关闭新建的工作簿
    wb.Close
Next ws

ThisWorkbook.Save

Application.DisplayAlerts = True
Application.ScreenUpdating = True

'完成文件合并后弹出提示对话框
Call MsgBox(prompt:="已完成Excel文件拆分!")

End Sub

```

**实现思路**：

1.   创建一个空白的工作簿；
2.   将 `Excel` 中一个工作表复制到这个新的工作簿中；
3.   以该工作表的名称命名这个新建的工作簿并保存该工作簿。

**注意**：

每运行一次该程序，都会对已拆分的文件进行覆写。

## 合并工作表

**代码**：`mergeWorkbooks`

```vb
Option Explicit

'读取文件名称
'返回集合类型
'包含了文件后缀名
Function getFileName(Optional ByVal fileType As String = "csv") As Collection

Dim nameColl As Collection
Dim path, fileName As String

Set nameColl = New Collection

fileType = "\*." & fileType
path = ThisWorkbook.path & fileType
fileName = Dir(path)

'读取文件名称并存入集合
Do While fileName <> ""
    nameColl.Add Item:=fileName
    fileName = Dir
Loop

Set getFileName = nameColl

End Function

'将外部Excel文件导入Excel
Function inputXLSX(ByVal fileName As String)

Dim wb As Workbook
Dim Tws, Ows As Worksheet

fileName = "\" & fileName

Set Tws = ThisWorkbook.Worksheets(1)
Set wb = Workbooks.Open(ThisWorkbook.path & fileName)

For Each Ows In wb.Worksheets
    Ows.Copy after:=Tws
Next Ows

wb.Close

End Function

'清空原Excel中所有工作表
'避免重复合并工作表
Function cleanWorkbook()

Dim i As Long
Dim Tws As Worksheet

Application.DisplayAlerts = False

'工作簿中必须保留至少一个工作表
For i = 2 To ThisWorkbook.Worksheets.count
    'i - 1避免溢出错误
    ThisWorkbook.Worksheets(i - 1).Delete
Next i

ThisWorkbook.Worksheets(1).name = "Sheet1"

Application.DisplayAlerts = True

End Function

'主过程
'将外部的Excel文件合并至Excel中
Sub main()

Dim name As String
Dim fileName As Variant

Application.ScreenUpdating = False

'清空原Excel中所有工作表
Call cleanWorkbook

For Each fileName In getFileName(fileType:="xlsx")
    Call inputXLSX(fileName:=fileName)
Next fileName

'保存Excel
ActiveWorkbook.Save

Application.ScreenUpdating = True

'完成文件合并后弹出提示对话框
Call MsgBox(prompt:="已将Excel文件全部合并!")

End Sub


```

**实现思路**：

1.   将该程序所在工作簿中的所有工作表全部删除，仅保留一个默认工作表 `Sheet1`。避免在多次执行该程序后，重复合并多个工作表。
2.   获取待合并工作簿名称；
3.   按照名称打开工作簿，将工作簿中所有工作表复制进该程序所在的工作簿，关闭打开的工作簿。

**小结**：

1.   单独调用没有返回值的函数时，使用 `Call myFunction` 可以避免一些关于何时使用括号 `()` 的问题。

---

[^1]: 在 `Excel` 中使用 `VBA` 编写的程序，理解成脚本也可以。
[^2]: 此处需要明确，在 `Excel` 中 **工作簿** 和 **工作表** 是两个不同的概念。**工作簿** 指文件夹中带有名称的 `Excel` 文件；**工作表** 指 **工作簿** 中的 `Excel` 表，新建一个空白的 **工作簿** 就能在其中看到一个默认名称为 `Sheet1` 的 **工作表**。
[^3]: 2023-08-26-晚
[^4]: 2023-08-27-上午