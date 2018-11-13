---
layout: post
title: "ConcurrentModificationException"
date: 2010.01.09 04:23:00
categories: [Programming]
tags: [java, programlama, concurrency]
comments: true
image:
    feature: concurrent_mod_ex.jpg
---
 
Java'da `Vector` ve `ArrayList` ikiside birbirine benzer işlemler gerçekleştirmektedir. Birbirlerinden tek farkları ise, `Vector`'un thread safe olarak adlandırılmasıdır. Yani birden fazla thread içeren bir uygulamanız var ise, ve siz `Vector`'un elemanlarına bunların herbirinden erişip üzerlerinde değişiklik yapıyorsanız, herhangi bir problem çıkarmayacaktır. Ancak `ArrayList` kullanırsanız, bu thread safe mantığını kendiniz halletmeniz gerekir. 

<!--more-->

Ama bugün başıma gelen bir problem, `Vector`'un thread safe olmasına karşın, bazı kullanımlarda bu durumun değişebildiğini gösterdi. Eğer `Vector` tanımlamasında generic belirttiyseniz, aslında belirtmesenizde olur, belirttiğiniz _generic_ türünde _foreach_ ile teker teker gezip işlem yapabilirsiniz. Ancak, bu kullanım Vector'u thread safe olmaktan çıkarıyor. 

Örnek vermek gerekirse, bir `Swing` objesinin paint metodu, onun modelindeki bir `Vector`'den renk ile alakalı parametre okuduğunu düşünelim. Bu obje ekranda çiziliyken ve görünür vaziyette iken, siz gidip bu `Vector` üzerinde _foreach_ ile gezip; renk parametreleriyle oynamaya kalkarsanız, yüksek ihtimal ile `ConcurrentModificationException` alacaksınız. Nedeni ise, sizin değişikliği yaptığınız thread ile, `Swing` objelerinin paint metodları (ve benzer metodların) çağırıldığı thread in farklı olması. 

```java
for(MyObject myObj : vctMyObjects) { 
    if(myObj.getIndex() == 0) { 
        vctMyObjects.remove(myObj); 
    } else { 
        myObj.setColor(Color.BLACK); 
    } 
}
```

Bunu çözmenin bir yolu bulunmakta. Eğer Vector'ün elemanlarını Iterator ile gezerseniz ve değişikliği Iterator'ın verdiği elementler ile yaparsanız, bu Exception'dan kaçmış olacaksınız. 

```java 
Iterator<MyObject> myIterator = vctMyObjects.iterator(); 
while(myIterator.hasNext()) { 
    MyObject myObj = myIterator.next(); 
    if(myObj.getIndex() == 0) { 
        // Bu satir su andaki elemani Vector'den cikarmaya yarar 
        myIterator.remove(); 
    } else { 
        myObj.setColor(Color.BLACK); 
    } 
}
```
