---
layout: post
title: "Javascript Sayı Formatlama"
date: 2014.09.18 00:30:00
categories: [Programming]
tags: [javascript, number, format]
comments: true
---
Javascript'de, bölünen sayılar (yüzdeler) için basit olarak yuvarlamayı aşağıdaki gibi yapabilirsiniz.

```javascript
var profits = 2489.8237;
profits.toFixed(3); //returns 2489.824 (round up)
profits.toFixed(2); //returns 2489.82
profits.toFixed(7); //returns 2489.8237000 (padding)
```
