---
layout: post
title: "Java'nın Saklı Kalmış Özellikleri: Double Brace Initialization"
date: 2012.11.27 13:14:00
categories: [Programming]
tags: [java, programlama, howto, double-brace-initialization]
comments: true
image: 
    feature: java_logo.png
--- 
Geçenlerde 'da gezinirken konuya rastladım. Java'da kullanılan ve az kişinin bildiği özellikler şeklinde. Orada adı geçen bazı şeyler o konuya ilişkin uğraşanlar için ilginç gelecektir ama _Double Brace Initialization_ şeklinde bir özellikle karşılaştım. Aslında Collections ile alakalı olduğu için nasıl oldu da bilinmediğini pek çözemedim. 

<!--more-->

Mesela hızlı bir şekilde bir Hashtable yaratıp içerisine bir kaç nesne koyup onu bir yere göndereceksiniz. İşte ilk satır yeni nesneyi yaratıp altındanki satırlarda da onunla alakalı işleri yapıp bu işi halledersiniz; 

```java
Hashtable<String, String> myHashtable = new Hashtable<String, String>(); 
myHashtable.put("a", "A"); 
myHashtable.put("b", "B"); 
someMethod(myHashtable);
```

Aslında olması gereken de budur :) 

Ancak bazı durumlarda hızlı bir şekilde bir hashtable yaratıp, tek satırda bir method çağrısı yapma isteyebilirsiniz. Bunun içinde; 

```java
someMethod(new Hashtable<String, String>(){{ put("a", "A"); put("b", "B"); }}); 
```

Tabi tek satırda halledilebilen bir işlem olsa da, okunulabilirliği düşürdüğü için, kullanımının pek de hoş durmadığını söyleyebilirim. 

[şu](http://stackoverflow.com/questions/15496/hidden-features-of-java)
