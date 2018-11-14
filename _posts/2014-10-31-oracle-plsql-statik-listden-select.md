---
layout: post
title: "Oracle PLSQL Statik List'den Select"
date: 2014.10.31 04:56:00
categories: [Programming]
tags: [oracle, plsql, select]
comments: true
---
Excel veya benzer bir ortamdan aldığınız statik bir listinize SELECT çalıştırabilmek için;

```sql
select column_value from table(sys.odcivarchar2list('18776', '18775'))
```
