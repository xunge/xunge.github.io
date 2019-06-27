---
title: HTML 表单的验证
tags:
  - HTML
  - JS
categories: 知识点
thumbnail: https://img.xungejiang.com/static/images/16-7-19/001.jpg
updated: '2016-07-19 09:01:42'
date: 2016-07-19 09:01:42
---


表单的验证在实际生活中很常见，比如注册页面要求用户名长度6-12位，如果不在这个范围内会报错的。

表单的验证一般使用 JavaScript 实现，博主在这里简单介绍一下。

<!--more-->




## 表单的创建

表单经常是由输入框组成。这里介绍几个常用的输入框。

```
<input type="text" />     文本框   <br />
<input type="password" /> 密码框   <br />
<input type="submit" />   提交按扭  <br />
<input type="checkbox" /> 复选框   <br />
<input type="radio" />    单选框   <br />
<input type="reset" />    重置按扭  <br />
<input type="image" />    图片按扭  <br />
<input type="hidden" />   隐藏域   <br />
<input type="button" />   按扭     <br />
<input type="file" />     浏览文件  <br />
<select>
	<option></option>
</select> <br />
<textarea></textarea> <br />
```

执行后的效果大概如此

![](https://img.xungejiang.com/static/images/16-7-19/001.jpg)

这里以注册用户名为例，要求 **6-30位字母、数字或“_”,字母开头**

```
用户名：<input type="text" onblur="checkName()" id="username" />&nbsp;<span id="yes"></span><font color="#FF9900">6-30位字母、数字或“_”,字母开头</font>
<br /><span id="info1"></span>
```

将改代码片段放入 <body> 标签下，最后执行结果如图所示：

![](https://img.xungejiang.com/static/images/16-7-19/002.jpg)

### onblur 介绍

这里需要用到一个 **event** 对象 `onblur` ，定义为 **onblur 事件会在对象失去焦点时发生**，及当我光标离开输入框时触发事件发生，这非常适用于检查输入框内容的格式是否符合要求。当然还有很多 `events` ，若想了解更多可到 [w3school](http://www.w3school.com.cn/jsref/dom_obj_event.asp) 查询。

### span 介绍

`<span>` 标签被用来组合文档中的行内元素。这里主要用来显示错误信息。

## JavaScript 实现

```
function checkName()
{
	var u = document.getElementById("username");
	var info1 = document.getElementById("info1");
	var yes = document.getElementById("yes");
	if (u.value == "")
	{
		info1.innerHTML = "<font color = 'red'>✘请输入用户名！</font>";
		return false;
	}
	if(u.value.length < 6)
	{
		info1.innerHTML = "<font color = 'red'>✘用户名长度不能少于6个字符！</font>";
		return false;
	}
	var r = /^[a-zA-Z][a-zA-Z0-9_]{5,29}$/;
	if (!r.test(u.value))
	{
		info1.innerHTML = "<font color = 'red'>✘用户名只能由字母，数字，下划线组成，须以字母开头</font>";
		return false;
	}
	yes.innerHTML = "<font color='green'>✔</font>";
	info1.innerHTML = "";
}
```

由代码可以很容易理解 JavaScript 语法需要先用 `document.getElementById("")` 获取输入框，然后用 `.value` 得到输入框的内容。

错误分三种情况：

1.如果输入为空，则错误信息为“请输入用户名！”
2.如果输入少于6个字符，则错误信息为“用户名长度不能少于6个字符！”
3.如果输入不满足由 `由字母，数字，下划线组成，须以字母开头` 这个条件，则错误信息为 `用户名只能由字母，数字，下划线组成，须以字母开头`

三种错误方式分别如图所示

![](https://img.xungejiang.com/static/images/16-7-19/003.jpg)

![](https://img.xungejiang.com/static/images/16-7-19/004.jpg)

![](https://img.xungejiang.com/static/images/16-7-19/005.jpg)

正确方式如图所示

![](https://img.xungejiang.com/static/images/16-7-19/006.jpg)

其中最后的条件限制使用了正则表达式。

### placeholder 介绍

placeholder 是 html5 `<input>` 里的属性，提供可描述输入字段预期值的提示信息（hint）。

为了

具体代码如下，以下代码基于 [12306 网上购票用户注册](https://kyfw.12306.cn/otn/regist/init)

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>无标题文档</title>
</head>

<body>
<form>
<table align="center">
	<tr>
    	<td align="right">用户名:</td>
        <td><input type="text" style="width:210px" placeholder="用户名设置成功后不可修改" onblur="checkName()" id="username" />&nbsp;<span id="yes"></span><font color="#FF9900">6-30位字母、数字或“_”,字母开头</font>
        <br /><span id="info1"></span>
        </td> <!--失去焦点-->

    </tr>
    <tr>
    	<td align="right">登陆密码:</td>
        <td><input type="password" style="width:210px" placeholder="6-20位字母、数字或符号" onblur="checkPassword()" id="password"/>&nbsp;<span id="yes1"></span>
     	<br /><span id="info2"></span>
        </td>
    </tr>
    <tr>
    	<td align="right">确认密码:</td>
        <td><input type="password" style="width:210px" placeholder="再次输入您的登录密码" onblur="checkPasswordtwice()" id="passwordconfirm"/>&nbsp;<span id="yes2"></span>
     	<br /><span id="info3"></span>
        </td>
    </tr>
     <tr>
    	<td align="right">姓名:</td>
        <td><input type="text" style="width:210px" placeholder="再次输入您的登录密码" /></td>
    </tr>
	<tr>
        <td align="right">证件类型:</td>
        <td><select style="width:210px">
                <option>二代身份证</option> <!-- 选项 -->
                <option>港澳通行证</option> <!-- 选项 -->
                <option>台湾通行证</option> <!-- 选项 -->
                <option>护照</option> <!-- 选项 -->
            </select></td>
	</tr>
	<tr>
		<td>证件号码:</td>
        <td><input type="text" style="width:210px" placeholder="请输入您的证件号码" onblur="checkID()" id="IDNO" />&nbsp;<span id="yes3"></span>
        <br /><span id="info4"></span>
        </td>
	</tr>
	<tr>
    	<td>手机号码:</td>
        <td><input type="text" style="width:210px" placeholder="请输入您的手机号码" /></td>
	</tr>
    <tr>
        <td align="right">旅客类型:</td>
        <td><select>
                <option>成人</option> <!-- 选项 -->
                <option>儿童</option> <!-- 选项 -->
                <option>学生</option> <!-- 选项 -->
                <option>残疾军人、伤残人民警察</option> <!-- 选项 -->
            </select></td>
	</tr>
</table>
</form>
</body>
<script>
function checkName()
{
	var u = document.getElementById("username");
	var info1 = document.getElementById("info1");
	var yes = document.getElementById("yes");
	if (u.value == "")
	{
		info1.innerHTML = "<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>请输入用户名！</font>";
		return false;
	}

	if(u.value.length < 6)
	{
		info1.innerHTML = "<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>用户名长度不能少于6个字符！</font>";
		return false;
	}

	var firstChar = u.value.charAt(0);


	/*if (!((firstChar >= 'a' && firstChar <= 'z') || (firstChar >= 'A' && firstChar <= 'Z')))
	{
		info.innerHTML = "<font color = 'red'>✘用户名必须以字母开头！</font>";
		return false;
	}*/
	// 定义一个正则表达式，校验用户名的规则
	var r = /^[a-zA-Z][a-zA-Z0-9_]{5,29}$/;
	if (!r.test(u.value))
	{
		info1.innerHTML="<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>用户名只能由字母，数字，下划线组成，须以字母开头</font>";
		return false;
	}

	yes.innerHTML="<font color='green'>✔</font>";
	info1.innerHTML="";
}

/***************************checkPassword*************************/
var pass = "";
function checkPassword()
{
	var p = document.getElementById("password");
	var info2 = document.getElementById("info2");
	pass = p.value;
	if (p.value == "")
	{
		info2.innerHTML = "<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>请输入密码！</font>";
		return false;
	}

	if(p.value.length < 6)
	{
		info2.innerHTML = "<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>密码长度不能少于6个字符！</font>";
		return false;
	}

	var firstChar = p.value.charAt(0);

	// 定义一个正则表达式，校验用户名的规则
	var r = /^[a-zA-Z][a-zA-Z0-9_]{5,29}$/;
	if (!r.test(p.value))
	{
		info2.innerHTML="<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>只能由字母，数字，下划线组成，须以字母开头";
		return false;
	}

	yes1.innerHTML="<font color='green'>✔</font>";
	info2.innerHTML="";
}

/*********************checkPasswordtwice*******************************/
function checkPasswordtwice()
{
	var p1 = document.getElementById("passwordconfirm");
	var info3 = document.getElementById("info3");
	if (p1.value!=pass)
	{
		info3.innerHTML = "<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>确认密码与密码不同！</font>";
		return false;
	}

	yes2.innerHTML="<font color='green'>✔</font>";
	info3.innerHTML=""
}

/*****************************checkID*********************************/
function checkID()
{
	var p2 = document.getElementById("IDNO");
	var info4 = document.getElementById("info4");

	//var r = /^[0-9][a-zA-Z0-9]{18}$/;
	var r1 = /[0-9]{18}$/;
	if (!r1.test(p2.value))
	{
		info4.innerHTML="<img src='https://kyfw.12306.cn/otn/resources/images/ots/icon_wrong.png'><font color = 'red'>请正确输入18位的身份证号</font>";
		return false;
	}
	yes3.innerHTML="<font color='green'>✔</font>";
	info4.innerHTML=""
}

</script>
</html>

```
