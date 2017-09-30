---
layout: post
title: "Java Call Stack'i Arttırmak"
date: 2010.01.13 20:41:00
categories: [Programming]
tags: [java, programlama, call stack, recursion]
comments: true
---
Java'nın default call stack büyüklüğü 400kb. Burada bahsi geçen heap size değil. Call Stack'den kasıt, o anda çalışan ya da bekleyen, yani çağrısı yapılmış metodların bulunduğu stack'dir. Bazen bu büyüklük sizi kesmeyebilir :) Çok büyük veri yapıları ya da recursion yapılar bunu tetikleyen unsurlardan sadece bir kaçı. Bunu kısa yoldan çözmenin yolu mevcut, çalıştırdığınız kodu; 

<!--more-->

<pre lang="java">java -Xss1024k MyProgram</pre>

şeklinde çağırarak bu büyüklüğü 1024 KB'a arttırmanız mümkün. 'k' yerine 'm' yazarak değeri MB cinsinden de verebilirsiniz. Ancak şunu belirtmek gerekir ki, bu tür yapıların büyüklükleri exponential olarak artacağı için, sizin bunu arttırmanız bir yere kadar işlerin yolunda gitmesini sağlayacaktır. 
Örneğin, 1000 çağrılık bir recursion metodunuz olduğunu düşünün. Bu kodu çalıştırmaya kalktığınızda ise stackoverflow exception'ı fırlattı. Stack büyüklüğünü 1024k yaptınız, 1000 çalıştı ancak bu sefer 1500 çağrı da saçmaladı. Sinirlenip büyüklüğü 10m yaptınız, 1500 çağrı çalıştı. Ancak bu sefer de 2500 çağrı da tekrar exception. Büyüklüğü 10 katına çıkarmanıza rağmen, çağrı sayısı (en dıştaki fonksiyon çağrısı) 2 katına çıkamadan tekrar exception fırlattı. 

Çünkü eğer toplam fonksiyon çağrınızın şu şekilde olduğunu düşünürsek; 

<pre>->**fonk1** 
------->**fonk2** 
------------->**fonk3**</pre>

İkinci recursion da bu yapı; 

<pre>->fonk1 
------->fonk2 
------------->fonk3 
------------------->**fonk1** 
------------------------>**fonk2** 
---------------------------->**fonk3**</pre>

şeklinde olacaktır. Yaptığınız her çağrıda bu sayı exponential olarak artacaktır. Eğer bu fonksiyonu çoğu yerde çağırıyorsanız, burada gidip call stack size'ı büyütmek yerine, yapınızda değişikliğe gitmeniz daha etkili olacaktır.
