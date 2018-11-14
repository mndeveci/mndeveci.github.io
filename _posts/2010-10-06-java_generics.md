---
layout: post
title: "Java Generics"
date: 2010.10.06 00:22:00
categories: [Programming]
tags: [java, programlama, generics]
comments: true
---
_Generics_, java 1.5 ile Java da yer almaya başlamış, nesneye _Object_ türünden başka türler belirterek kullanım kolaylığının yanı sıra, daha okunaklı, bakımı ve geliştirilmesi daha kolay kod yazımını sağlamıştır. 

<!--more-->

Örnek olarak vermek gerekirsek, `Vector` tipini ele alalım. Eğer siz `Vector`'ünüzü; 

```java
Vector myVector = new Vector();
```

şeklinde yaratırsanız, size get metodunda _Object_ türünden nesneler dönüp, _set_ metodunda ise _Object_ türünde nesne isteyecektir. Siz her seferinde bu `Vector`'e `String` nesnesi koysanız bile, her aldığınızda kendiniz `String` nesnesine _cast_ etmek zorunda kalırsınız. 

![]({{ site.baseurl }}/img/post/generics2.png)
Ancak siz aynı `Vector`'ünüzü; 

```java
Vector<String> myVector = new Vector<String>();
```

şeklinde yaratırsanız, size _get_ metodundan `String` türü dönecek, _set_ metodunda ise `String` türünde nesne isteyecektir. Siz her seferinde _get_ ile aldığınız nesneleri _cast_ etmek zorunda kalmayacak ve kodlama sırasında farklı türden bir nesneyi _set_ etmeye ya da ona _get_ etmeye çalışmanız halinde _compile-time_ hata vererek, olası _run-time_ hatalarından kurtaracaktır. 

![](http://1.bp.blogspot.com/-8K5Q0gTI4jU/UDgJLP8N7ZI/AAAAAAAAAd0/ehqhBho6BZM/s1600/generics.png) 
Generic'lerde dikkat edilmesi gereken bir husus, `Vector` bir tür değildir. `Vector<String>.class` mantıksız olacak ve compile-time da hata verecektir. _Generic'ler_ sadece birer belirteç gibi görebilirsiniz. Sizi sadece bazı hatalardan koruyarak, sizin bu hataları yapmanızı engelleyip, arka planda aslında yine _cast_ işlemlerini yaparlar. `Vector` sınıfının kaynak koduna bakarsanız, orada verileri bir Object[] dizisinde tutup, _get_ metodunda da o diziden aldığı _Object_ türündeki nesneyi _generic_ türüne _cast_ etmektedir. Java compiler da siz kodunuzu _binary_ hale dönüştürdüğünüzde, oradaki _generic_ imzalarını verdiğiniz türe çekerek _compile_ eder.

Nasıl Vector bir tür değilse; `Vector myVector = new Vector();` şeklinde bir _generic Vector_ tanımlamak, _generic_'siz bir `Vector` tanımlamakla eşdeğerdir. Çünkü soru işareti (?) herhangi bir tür olduğunu belirtir. Ancak _wild card_ şu şekilde kullanırsak;

```java
public void myMethod(Vector<? extends String> paramVector) {
    // method body
}
```


burada da, `String` ve ondan türemiş diğer nesneleri kabul eder şeklinde bir _wild card_ kullanmış oluruz. Yine metod, en az `String` veya `String`'den türemiş bir nesne isteyecektir. _Object_ ya da `String` türünden farklı bir nesne gönderimi _compile-time_ da hata verecektir. 

![]({{site.baseurl}}/img/post/generics_wild.png){: .lazyload}
Sınıfları ve metod parametrelerini _generic_ olarak yazmanın yanı sıra, bir metodun dönüş değerini de _generic_ olarak tanımlayabiliriz. Örnek vermek gerekirse; 

```java
public <T> T myMethod(String p_strParam, Class<T> p_typeParam){
    return (T) p_strParam;
}
```

myMethod metodu, `T` türünden bir nesne dönmektedir. Nesnenin asıl türünü ise parametre ile gönderilen sınıftan almaktadır. eğer metodu şu şekilde çağrırısak;  `myObj.myMethod("my parameter", String.class);`

içeride "my parameter" ı `String` nesnesine _cast_ edip _return_ edecektir. Ancak burada dikkat edilmesi gereken nokta, bu gibi benzer durumlarda, burada bir `String` nesnesini T nesnese (ki herşey olabilir) _cast_ ediyoruz. Bu da _compile-time_ da _warning_ verecektir. 

![]({{site.baseurl}}/img/post/generic_metod.png){: lazyload}
Sonuç olarak java da _generic_'ler, daha generic (DRY:don't repeat yourself) kodlar yazmanıza olanak sağlıyarak, daha efektif çalışmanıza imkan verecektir.
