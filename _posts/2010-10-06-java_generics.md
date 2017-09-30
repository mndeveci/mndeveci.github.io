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

Örnek olarak vermek gerekirsek, _Vector_ tipini ele alalım. Eğer siz _Vector_'ünüzü; 

<pre class="prettyprint">Vector myVector = new Vector();</pre>

şeklinde yaratırsanız, size get metodunda _Object_ türünden nesneler dönüp, _set_ metodunda ise _Object_ türünde nesne isteyecektir. Siz her seferinde bu _Vector_'e _String_ nesnesi koysanız bile, her aldığınızda kendiniz _String_ nesnesine _cast_ etmek zorunda kalırsınız. 

![](http://2.bp.blogspot.com/-dA9pyJMc-hE/UDgJGmrsjXI/AAAAAAAAAds/t8nHnBjwtLU/s1600/generics2.png) 
Ancak siz aynı _Vector_'ünüzü; 

<pre class="prettyprint">Vector <string>myVector = new Vector<string>();</string></string></pre>

şeklinde yaratırsanız, size _get_ metodundan _String_ türü dönecek, _set_ metodunda ise _String_ türünde nesne isteyecektir. Siz her seferinde _get_ ile aldığınız nesneleri _cast_ etmek zorunda kalmayacak ve kodlama sırasında farklı türden bir nesneyi _set_ etmeye ya da ona _get_ etmeye çalışmanız halinde _compile-time_ hata vererek, olası _run-time_ hatalarından kurtaracaktır. 

![](http://1.bp.blogspot.com/-8K5Q0gTI4jU/UDgJLP8N7ZI/AAAAAAAAAd0/ehqhBho6BZM/s1600/generics.png) 
Generic'lerde dikkat edilmesi gereken bir husus, 

<pre class="prettyprint">Vector</pre>

bir tür değildir. 

<pre class="prettyprint">Vector<string>.class</string></pre>

mantıksız olacak ve compile-time da hata verecektir. _Generic'ler_ sadece birer belirteç gibi görebilirsiniz. Sizi sadece bazı hatalardan koruyarak, sizin bu hataları yapmanızı engelleyip, arka planda aslında yine _cast_ işlemlerini yaparlar. _Vector_ sınıfının kaynak koduna bakarsanız, orada verileri bir Object[] dizisinde tutup, _get_ metodunda da o diziden aldığı _Object_ türündeki nesneyi _generic_ türüne _cast_ etmektedir. Java compiler da siz kodunuzu _binary_ hale dönüştürdüğünüzde, oradaki _generic_ imzalarını verdiğiniz türe çekerek _compile_ eder. 

Nasıl Vector bir tür değilse; 

<pre class="prettyprint">Vector myVector = new Vector();</pre>

şeklinde bir _generic Vector_ tanımlamak, _generic_'siz bir _Vector_ tanımlamakla eşdeğerdir. Çünkü soru işareti (?) herhangi bir tür olduğunu belirtir. Ancak _wild card_ şu şekilde kullanırsak; 

<pre class="prettyprint">public void myMethod(Vector { 

// method body 
}</pre>

burada da, _String_ ve ondan türemiş diğer nesneleri kabul eder şeklinde bir _wild card_ kullanmış oluruz. Yine metod, en az _String_ veya _String_'den türemiş bir nesne isteyecektir. _Object_ ya da _String_ türünden farklı bir nesne gönderimi _compile-time_ da hata verecektir. 

![](http://3.bp.blogspot.com/-pROt8o042a8/UDgJOydAwYI/AAAAAAAAAd8/OsjLkJZmhP4/s1600/generics_wild.png) 
Sınıfları ve metod parametrelerini _generic_ olarak yazmanın yanı sıra, bir metodun dönüş değerini de _generic_ olarak tanımlayabiliriz. Örnek vermek gerekirse; 

<pre class="prettyprint">public T myMethod(String p_strParam, Class <t>p_typeParam){ 

return (T) p_strParam; 
}</t></pre>

myMethod metodu, T türünden bir nesne dönmektedir. Nesnenin asıl türünü ise parametre ile gönderilen sınıftan almaktadır. eğer metodu şu şekilde çağrırısak; 

<pre class="prettyprint">myObj.myMethod("my parameter", String.class);</pre>

içeride "my parameter" ı _String_ nesnesine _cast_ edip _return_ edecektir. Ancak burada dikkat edilmesi gereken nokta, bu gibi benzer durumlarda, burada bir _String_ nesnesini T nesnese (ki herşey olabilir) _cast_ ediyoruz. Bu da _compile-time_ da _warning_ verecektir. 

[![](http://3.bp.blogspot.com/-6AckKQsjKxM/UDgJTlB3JUI/AAAAAAAAAeE/8jNUpDP0iL8/s320/generic_metod.png)](http://3.bp.blogspot.com/-6AckKQsjKxM/UDgJTlB3JUI/AAAAAAAAAeE/8jNUpDP0iL8/s1600/generic_metod.png) 
Sonuç olarak java da _generic_'ler, daha generic (DRY:don't repeat yourself) kodlar yazmanıza olanak sağlıyarak, daha efektif çalışmanıza imkan verecektir.
