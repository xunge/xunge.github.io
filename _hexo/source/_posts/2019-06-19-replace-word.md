---
title: 使用 VBA（宏）批量替换 Word 中的字词
tags:
  - VBA
  - 宏
  - Word
categories:
  - Word
date: 2019-06-19 14:53:27
updated: 2019-06-19 14:53:27
thumbnail: 
---

当使用翻译软件翻译论文时，经常会将某些专业名词翻译错误，例如将 `adversarial example` 翻译为 `对抗性的例子` 而不是 `对抗样本`。虽然 Word 可以全局替换，但是一次只能替换一个词组，如果想同时替换多个词组将非常费事。这时使用 VBA 将是一个不错的选择。

<!--more-->

`视图` -> `宏` -> `查看宏` -> `新建宏` 命名为 replace。

在打开的 VB 编辑器粘贴下面代码，更改具体的替换词语，即可
```
Sub replace()
    Const wdReplaceAll = 2
    Dim oRng As Range
    Set oRng = ActiveDocument.Content
    With oRng.Find
        .Execute FindText:="对抗性的例子", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="挤压", ReplaceWith:="压缩", replace:=wdReplaceAll
    End With
End Sub
```
保存后关闭编辑器即可。

之后只需要运行名为 replace 的宏即可。

也可以将该宏添加到快速访问工具栏中，这样可以更方便的调用宏。