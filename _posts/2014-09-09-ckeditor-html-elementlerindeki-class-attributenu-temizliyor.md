---
layout: post
title: "CKEditor html elementlerindeki class attribute'nu temizliyor"
date: 2014.09.09 14:00:00
categories: [Programming]
tags: [javascript, number, format]
comments: true
---
Configuration dosyasında aşağıdaki parametreler eklenerek sorun çözülebilir:

```language-javascript
config.allowedContent = true;
config.extraAllowedContent = "*(*)";
```
