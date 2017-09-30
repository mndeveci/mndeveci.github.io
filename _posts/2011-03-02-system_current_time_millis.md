---
layout: post
title: "System.currentTimeMillis()"
date: 2011.03.02 00:24:00
categories: [Programming]
tags: [java, programlama, howto, time]
comments: true
---
Özellikle 64bit sistemlerde (Windows 2003 Server 64bit'de gördüğüm) method ard arda çağırıldığı takdirde beklenildiği gibi mantıklı long değerler üretemiyor. Örneğin yaptığı işlemlerin zamanlarını tutmak istiyorsanız, hangisinin önce hangisinin sonra olduğunu anlamak için, bazen sonra yaptığı işlemlere daha küçük long değerler atayabiliyor. Bunun sebebi olarak da tam olarak sistem saatinin o anki değerine yakın bir değer döndürmesi. Tam olarak o anki zamanı ölçmede yanılgılara sebep olabilir ancak geçen süreyi hesaplamak için kullanıldığında daha etkili / mantıklı değerler verecektir. 

<!--more-->

Bunun yerine daha kesin veriler almak istiyorsanız, System.nanoTime() nano saniye hassaslığında bildirim yapabilmektedir. 64bit sistemlerde rastlanabilen üstteki örneği nano saniye ile aldığınızda, aldığınız değerler daha mantıklı çıkıyorlar. Bunun nedeni olarak da, nano saniye methodunun içerisinde bir takım iyileştirmelerin yapıldığından bahsediliyor java dökümantasyonunda. 

Daha detaylı bilgi için; [http://download.oracle.com/javase/6/docs/api/java/lang/System.html#currentTimeMillis()](http://download.oracle.com/javase/6/docs/api/java/lang/System.html#currentTimeMillis() "System.currentTimeMillis()")
