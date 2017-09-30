---
layout: post
title: "ConcurrentModificationException"
date: 2010.01.09 04:23:00
categories: [Programming]
tags: [java, programlama, concurrency]
comments: true
---
[![](http://2.bp.blogspot.com/-zoEE-bIvvW0/UAvbcLKI3-I/AAAAAAAAAcw/ph5Sl6V76fY/s320/fig9-3.jpg)](http://2.bp.blogspot.com/-zoEE-bIvvW0/UAvbcLKI3-I/AAAAAAAAAcw/ph5Sl6V76fY/s1600/fig9-3.jpg) 
Java'da _Vector_ ve _ArrayList,_ ikiside birbirine benzer işlemler gerçekleştirmektedir. Birbirlerinden tek farkları ise, _Vector_'un thread safe olarak adlandırılmasıdır. Yani birden fazla thread içeren bir uygulamanız var ise, ve siz _Vector_'un elemanlarına bunların herbirinden erişip üzerlerinde değişiklik yapıyorsanız, herhangi bir problem çıkarmayacaktır. Ancak _ArrayList_ kullanırsanız, bu thread safe mantığını kendiniz halletmeniz gerekir. 

<!--more-->

Ama bugün başıma gelen bir problem, _Vector_'un thread safe olmasına karşın, bazı kullanımlarda bu durumun değişebildiğini gösterdi. Eğer _Vector_ tanımlamasında generic belirttiyseniz, aslında belirtmesenizde olur, belirttiğiniz _generic_ türünde _foreach_ ile teker teker gezip işlem yapabilirsiniz. Ancak, bu kullanım Vector'u thread safe olmaktan çıkarıyor. 

Örnek vermek gerekirse, bir _Swing_ objesinin paint metodu, onun modelindeki bir _Vector_'den renk ile alakalı parametre okuduğunu düşünelim. Bu obje ekranda çiziliyken ve görünür vaziyette iken, siz gidip bu _Vector_ üzerinde _foreach_ ile gezip; renk parametreleriyle oynamaya kalkarsanız, yüksek ihtimal ile _ConcurrentModificationException_ alacaksınız. Nedeni ise, sizin değişikliği yaptığınız thread ile, _Swing_ objelerinin paint metodları (ve benzer metodların) çağırıldığı thread in farklı olması. 

<pre class="prettyprint"> 
for(MyObject myObj : vctMyObjects) { 

if(myObj.getIndex() == 0) { 

vctMyObjects.remove(myObj); 

} else { 

myObj.setColor(Color.BLACK); 

} 
}</pre>

Bunu çözmenin bir yolu bulunmakta. Eğer Vector'ün elemanlarını Iterator ile gezerseniz ve değişikliği Iterator'ın verdiği elementler ile yaparsanız, bu Exception'dan kaçmış olacaksınız. 

<pre class="prettyprint"> 
Iterator <myobject>myIterator = vctMyObjects.iterator(); 
while(myIterator.hasNext()) { 

MyObject myObj = myIterator.next(); 

if(myObj.getIndex() == 0) { 

// Bu satir su andaki elemani Vector'den cikarmaya yarar 

myIterator.remove(); 

} else { 

myObj.setColor(Color.BLACK); 

} 
}</myobject></pre>
