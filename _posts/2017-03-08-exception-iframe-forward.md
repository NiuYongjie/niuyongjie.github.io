---
layout: post
title: 含有iframe的页面在跳转时遇到的异常
tags: [异常,iframe]
---

## 异常现象

含有iframe的页面,在session过期后,点击了页面中非iframe页面中的功能按钮,导致iframe区域中的内容跳转到了登录页面,而iframe区域之外的页面没有发生变化.

## 异常原因

 session过期后,发生请求后被后台拦截器进行拦截,拦截后跳转到登录页面,而前台页面被iframe分层,应该定位到顶级DOM元素,再进行跳转
 
 ## 实现方式
 
 获取顶级DOM元素再进行跳转的方式有很多,但在具体项目中由于不能过多的改造原有的页面,最后想了一个办法:
 
 - 新增一个空白的页面
 - 后台跳转到该空白页面
 - 通过该空白页面去请求登录页面

代码如下:

```html
<html>
	<head></head>
	<body></body>
	<script>
		window.onload=function(){
			window.open(loginUrl,'_top');
		}
	</script>
</html>
```
