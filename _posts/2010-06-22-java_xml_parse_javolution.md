---
layout: post
title: "Java XML Parse - Javolution"
date: 2010.06.22 23:22:00
categories: [Programming]
tags: [java, xml]
comments: true
---
Günümüzde veriyi bir yerden bir yere taşımak için en etkili araçlardan birisi (hatta en etkilisi) XML diyebiliriz. Okunabilir olması, düzenli bir şekilde iç içe ağaç yapısının olması ve HTML'in bunca senedir standart olarak hiç bir şekilde büyük çapta değişimine gerek duyulmadan kullanılması bunların başlıca sebepleri olarak sıralayabiliriz. Ancak verinin XML hale gelmesi ve diğer uçta XML'in tekrar anlamlı verilere dönüştürülmesi hem zaman hem de yer açısından büyük bir önem taşımaktadır. 

<!--more-->

Java programala dilinde XML için kullanabileceğimiz iki adet yöntem (ya da konsept de diyebiliriz) bulunmaktadır. DOM ve SAX. Bunlara ek olarak son zamanlarda StAX da eklenmiştir. 

DOM sizin XML dosyanızı en başta okuyup kendi içinde ağaç yapısı halinde size vermektedir. Bu sayede siz işlemin herhangi bir anında bir yere dallanıp oradan veri okuyup daha sonra başka kademede başka bir veriye ulaşabilirsiniz. Yani ilk Element nesnesini kaybetmediğiniz sürece veriyi elinizde tutmaktasınızdır. Ancak bu yapıyı elinizde tutmak size bazı negatif getirileri olacaktır. Birincisi büyük çaptaki dosyalarla uğraşmaktaysanız, yüksek miktarda hafıza isteyecektir. İkincisi, tüm XML'i en başta çözümlediği için, size gerekli olup olmayan bütün veriyi daha en başta çözecektir. Size tüm veriyi sunmadaki becerisi çok iyi bir özellik olurken yüksek miktarlardaki veri isteyen işlerde ise problem yaratacağı kesindir. 

SAX ise DOM gibi çalışmaz. SAX parser ve de-parse işlemlerini sizin yazmanız gerekmektedir. Bu sayede işlemler tamamen size kalmıştır. Nerede ne yapacağınız hangi nesneyi yaratacağınız, hangi verileri tutacağınız vs gibi. Dahası tamamen _event-driven_ olduğu için efektif bir algoritma yazmanız halinde DOM'a kıyasla daha hızlı olacaktır. Negatif yanı ise sizin XML'i kendiniz çözümlemeniz gerekmektedir. Tüm hataları ve tüm istisnai durumları kendiniz halletmelisinizdir. 

StAX ise geçenlerde, daha hızlı XML parse'ı araştırıken, karşıma çıkan bir yenilik oldu. Aslında sonuna kadar okuyamamla beraber, SAX'a çok benzer bir yapıya sahip olduğunu öğrendim. Asıl önemli olan ise StAX değil onu gördüğüm yer olan _javolution_ kütüphanesi idi. 

sitesinde de anlatıldığı üzere bahsettikleri şey java.util kütüphanesini baştan yaratmak olduğu. Daha hızlı ve daha efektif çalışan ve util paketiyle eşdeğer bir paket hazırladıklarını anlatıyorlar. Bunların içinden en önemlisi, benim işime en yarayacak olanı, xml paketiydi. XML _parse_ ve _de-parse_ işlemlerini çok basit ve çok etkili bir hale getirmekte _javolution_. 

Bunu nasıl yaptığına gelirsek, öncelikle XML'e dönüştürülebilecek nesnelerimizi XMLSerializable interface'inden türetiyoruz. Java'da bulunan Serializable'a benzer bir mantığı var. Daha sonra ise Sınıfımızın içine 

<pre class="prettyprint">XMLFormat XML = new XMLFormat(MyClass.class){};</pre>

nesnesini tanımlıyoruz. İşte asıl işlem bu XMLFormat instance'ında yatıyor. Bu inner class'ın içine yazacağımzı, yazmamız gereken, read ve write fonksiyonları bu nesnenin otomatik olarak XML'e yazılıp daha sonra ise XML'den okunmasını sağlamaktadır. Geri planda bu işi yapan kısım javolution paketinin içindedir. 

Örnek olarak vermek gerekirse XML'i dosyaya yazmak ve okumak için; 

[http://javolution.org/](http://javolution.org/)
