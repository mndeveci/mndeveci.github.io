---
layout: post
title: "Java Swing Debugging"
date: 2011.08.04 22:59:00
categories: [Programming]
tags: [java, programlama, howto, swing, debugging]
comments: true
image:
    feature: boy-on-swing1.jpg
---

Swing ile haşır neşir olduysanız - _hele hele fazla bir şekilde_ - bazı zamanlar, bu nasıl oldu şimdi diye düşünmeye başlıyor insan. Örneğin tablo modelini değiştirirsiniz, bir anda focus anlamsız yere gider, ya da focus alan nesne istenen nesne değildir. Bunları tabi normal alışkanlıklar gereği debug edip takip etmek pek mümkün sayılmaz. Milisaniyeler içinde gerçekleşen bir eylemi 10~20 dk debug edip kendinizi JComponent ya da Component içerisinde, kaybolmuş vaziyette bulursunuz bir anda. 

<!--more-->

Bu durumda B planına geçiyoruz bu da log ya da trace izleme yöntemi. Madem kodu takip edemiyoruz, o halde sırasıyla hangi aksiyonların işlendiğini görebiliriz. Bunu da yapabilmek için Swing'in tek bir thread üzerinden yönetilmesi bizim işimize yarayan yegane özelliği olacak, EventQueue. Bildiğiniz gibi Swing thread safe bir alt yapıdır, herhangi bir şekilde multi-thread çalışan bir kısmı yoktur, tam tersine yeni bir iş geldiğinde iş kuyruğuna eklenerek yapılacaklar arasına konur. 

Şimdi ilk olarak EventQueue'den türeyen yeni bir sınıf yazıyoruz ve dispatchEvent(AWTEvent) metodunu override ediyoruz; 

```java
class DebugThread extends EventQueue {
    protected void dispatchEvent(AWTEvent event) {
        System.out.println(event);
        super.dispatchEvent(event);
    }
}
```

Burada önemli olan super metodunu çağırmayı kesinlikle unutmamak, zira tüm swing işleri duracağından, arayüz karasız bir hale gelecektir. Şimdi sıra bu sınıfı orjinal EventQueue yerine geçirmek. Bunu da aşağıdaki gibi gerçekliyoruz; 

```java
Toolkit.getDefaultToolkit().getSystemEventQueue().push(new DebugThread());
```

Bu satırın işlenmesinin hemen ardından, çalışan tüm swing aksiyonlarını konsol penceresinde görebilirsiniz. Burada son olarak dikkat edilmesi gereken, InvocationEvent türündeki nesnelerin, herhangi bir aksiyon olmadığı durumda gelen nesneler olması. Yani gerçekten trace almak istiyorsanız, o halde bu event dışındaki eventleri izlemeniz gerekmektedir. 

```java
class DebugThread extends EventQueue {
    protected void dispatchEvent(AWTEvent event) {
        if(!(event instanceof InvocationEvent)) {
            System.out.println(event);
        }
        super.dispatchEvent(event);
    }
}
```

Daha fazla bilgi almak ve farklı örneklerden yararlanmak için iki yararlı link; 
[Swing Debugging 2](http://weblogs.java.net/blog/alexfromsun/archive/2006/02/debugging_swing.html)
