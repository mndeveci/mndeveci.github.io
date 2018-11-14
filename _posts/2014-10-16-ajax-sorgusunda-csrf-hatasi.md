---
layout: post
title: "Ajax Sorgusunda CSRF Hatası"
date: 2014.10.16 22:43:00
categories: [Programming]
tags: [java, spring, security, jquery, ajax, post, csrf]
comments: true
---
Spring security ayarlarından csrf'yi etkinleştirdiyseniz ve jQuery ile ajax post request'i yapmaya çalıştığınızda csrf hatası alacaksınız. Hatayı gidermek için `jQuery.ajax()` metodu içine `'headers'` parametresi ile csrf token'ını gönderebilirsiniz.

```javascript
var headersData = {};

var attrVals = {'test1': 'val1'};

$.ajax({
	method: 'POST',
	url: 'http://localhost:8080/testProject/test',
	data: {
		'attr' : attrVals,			
	},
	headers: headersData,
});
```
