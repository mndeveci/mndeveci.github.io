---
layout: post
title: "JTable Kolon Indeksi"
date: 2011.01.01 16:56:00
categories: [Programming]
tags: [java, programlama, howto, swing, jtable]
comments: true
---
JTable'da kullanıcıya kolonların sıralarını değiştirme hakkını verebiliyorsunuz. Ancak eğer ki kullanıcı kolonları değiştirdikten sonra, kolon indeks'ine göre, kendi tablo modelinizde bilgiye erişmek isterseniz hata ile karşılacaksınız. Çünkü size seçili kolon indeksi olarak görsel tablodaki indeksi verecektir size. Ancak size tablo modelindeki asıl indekse ulaşmak isterseniz, bunu JTable sınıfı içerisindeki TableColumnModel nesnesi içindeki TableColumn nesnesi içindeki getModelIndex() metodu ile öğrenebilirsiniz. 
[http://download.oracle.com/javase/6/docs/api/javax/swing/table/TableColumn.html#getModelIndex()](http://download.oracle.com/javase/6/docs/api/javax/swing/table/TableColumn.html#getModelIndex())
