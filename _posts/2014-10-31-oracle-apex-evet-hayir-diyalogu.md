---
layout: post
title: "Oracle Apex Evet/Hayır Diyaloğu"
date: 2014.10.31 01:52:00
categories: [Programming]
tags: [javascript, oracle, apex, plsql]
comments: true
---
Oracle Apex'de, bir linkin altına Evet/Hayır sorusu sormak için:

```javascript
javascript:apex.confirm('Sormak istediğiniz soru?', {set:{"ITEM_1":"VAL_1"}});
```
