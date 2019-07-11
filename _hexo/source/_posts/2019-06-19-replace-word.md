---
title: 使用 VBA（宏）批量替换 Word 中的字词
tags:
  - VBA
  - 宏
  - Word
categories:
  - 经验值
date: 2019-06-19 14:53:27
updated: 2019-06-19 14:53:27
thumbnail: 
---

当使用翻译软件翻译论文时，经常会将某些专业名词翻译错误，例如将 `adversarial example` 翻译为 `对抗性的例子` 而不是 `对抗样本`。虽然 Word 可以全局替换，但是一次只能替换一个词组，如果想同时替换多个词组将非常费事。这时使用 VBA 将是一个不错的选择。

<!--more-->

## 添加宏

`视图` -> `宏` -> `查看宏` -> `新建宏` 命名为 replace。

在打开的 VB 编辑器粘贴下面代码，更改具体的替换词语，即可

```
Sub replace()
'
' replace 宏
'
    Const wdReplaceAll = 2
    Dim oRng As Range
    Set oRng = ActiveDocument.Content
    With oRng.Find
        .Execute FindText:="对抗性的例子", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="对抗性示例", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="对抗性实例", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="对抗性例子", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="对抗实例", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="对手", ReplaceWith:="攻击者", replace:=wdReplaceAll
        .Execute FindText:="挤压", ReplaceWith:="压缩", replace:=wdReplaceAll
        .Execute FindText:="功能", ReplaceWith:="特征", replace:=wdReplaceAll
        .Execute FindText:="显着", ReplaceWith:="显著", replace:=wdReplaceAll
        .Execute FindText:="稳健", ReplaceWith:="鲁棒", replace:=wdReplaceAll
        .Execute FindText:="健壮", ReplaceWith:="鲁棒", replace:=wdReplaceAll
        .Execute FindText:="样品", ReplaceWith:="样本", replace:=wdReplaceAll
        .Execute FindText:="渐变", ReplaceWith:="梯度", replace:=wdReplaceAll
        .Execute FindText:="培训", ReplaceWith:="训练", replace:=wdReplaceAll
        .Execute FindText:="规范", ReplaceWith:="范数", replace:=wdReplaceAll
        .Execute FindText:="探测", ReplaceWith:="检测", replace:=wdReplaceAll
        .Execute FindText:="辍学", ReplaceWith:="dropout", replace:=wdReplaceAll
        .Execute FindText:="图层", ReplaceWith:="层", replace:=wdReplaceAll
        .Execute FindText:="合法示例", ReplaceWith:="合法样本", replace:=wdReplaceAll
        .Execute FindText:="对抗性样本", ReplaceWith:="对抗样本", replace:=wdReplaceAll
        .Execute FindText:="对抗性训练", ReplaceWith:="对抗训练", replace:=wdReplaceAll
        .Execute FindText:="对抗性鲁棒性", ReplaceWith:="对抗鲁棒性", replace:=wdReplaceAll
        .Execute FindText:="对抗性强大", ReplaceWith:="对抗鲁棒性", replace:=wdReplaceAll
        .Execute FindText:="敌对", ReplaceWith:="对抗", replace:=wdReplaceAll
    End With
End Sub
```
保存后关闭编辑器即可。

之后只需要运行名为 replace 的宏即可。

也可以将该宏添加到快速访问工具栏中，这样可以更方便的调用宏。