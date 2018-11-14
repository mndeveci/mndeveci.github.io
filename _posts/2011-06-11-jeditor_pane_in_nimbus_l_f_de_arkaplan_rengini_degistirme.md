---
layout: post
title: "JEditorPane'in, Nimbus L&F'de, Arkaplan Rengini Değiştirme"
date: 2011.06.11 16:58:00
categories: [Programming]
tags: [java, programlama, howto, swing, jeditorpane, nimbus]
comments: true
image:
    feature: editor1.jpg
---

Java'da HTML göstermek için kullanılan JEditorPane'e normalde arkaplan değerini verebiliyorsunuz. Ancak _Look&Feel_ olarak Nimbus seçerseniz, arkaplan değeri hep beyaz olarak kalıyor. Bu hatadan kurtulmak için, arka plan rengi transparan verilebilir. 

```java
JEditorPane editor = new JEditorPane();
editor.setBackground(new java.awt.Color(0,0,0,0));
```

[Konu ile alakalı bug](http://bugs.sun.com/bugdatabase/view_bug.do;jsessionid=ab1938d61a7fd83ca2b54eb8df7?bug_id=6789980)
