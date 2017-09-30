---
layout: post
title: "Apache Ant"
date: 2011.04.02 19:29:00
categories: [Programming]
tags: [java, programlama, howto, ant, apache-ant, build, deploy, version-control]
comments: true
---
Bir yazılım projesine başladınız. Proje geliştirme süreci bir aşamayı artık geçti ve sizin belli aralıklarla versiyon çıkarmanız, ve bu versiyonu dağıtmanız gerekiyor. Normal şartlar altında bu süreç sizin için karın ağrısına dönüşüyor, ya da şirkette birinin gereksiz yere önemli konuma gelmesine sebep olabilir. 

<!--more-->

Yüksek ihtimalle bir ekip olduğunuz için, _VCS_ (_Version Control System_) kullanıyorsunuzdur. Yani projenin en son halini sistemden çekmeniz gerekmektedir. Projenizi sattığınız için, bazı saldırılardan kurtulmak için derlenmiş dosyaları şifreleyip (_encrypt_) daha sonra _obfuscate_ etmeniz gerekmektedir. Tabi _jar_ dosyalarını oluşturup, o dosyaları gerekli yerlere yüklemeniz kopyalarını dağıtmanız gerekmektedir. Bunlardan başka _Release Notes_ ve dökümantasyon yayınlamanız gerekmektedir. Tabi daha önceden derleme veya daha sonra _JNLP_ dosyaları oluştururken bazı kütüphaneleri de eklenemiz gerekmektedir. 

Yani yeni versiyon çıkacağı zaman; 

* Projenizi açıp dosyaları _VCS_'den update etmek

* _VCS_'deki dosyaları versiyon numarası ile tag'lemek

* Varsa yeni kütüphaneleri eklemek

* Projeyi en baştan derlemek

* Daha sonra versiyon numarasını kullandığınız _bug monitor_ sistemine girmek

* Yine aynı sistemden _Release Notes_ ve dökümantasyon dosyalarını oluşturmak

* Belki komut satırı ile belki yarattığınız .bat dosyaları ile _jar_ dosyalarını oluşturmak

* Oluşan jar dosyalarını _obfuscate_ etmek

* Obfuscate edilen dosyaları encrypt etmek

* Bu dosyaları imzalamak

* En son haline getirilen bu dosyaları bazı yerlere kopyalamak

Yani görüldüğü üzere yaklaşık 10 adım civarında yapmanız gereken bir iş vardır. Ve bu da bazı kriterler ışığında yaklaşık 10 ile 15 dk arasında sürenizi almaktadır. Belki de harikalar yaratan programınızı üretmek için resmen siz elinden tutup taşıyorsunuz onu :) 

Bu süreci optimum sürede ve minimum eforla halletmek için _Apache Ant_ bize yardım edebilir. 

Öncelikle _Ant_, dosyasından da anlaşılacağı üzere, _build_ işlemini yapmak üzere düşünülmüş bir araçtır. Ancak zaman içerisinde diğer ek özellikleriyle beraber sadece _build / compile_ işlerini değil. Aynı zamanda _release / deploy_ gibi bir sonraki safhada yer alan görevleri de rahatlıkla yerine getirebilmektedir. Burada da yukarıda saydığım çoğu işlemi otomatik olarak halledip bize sadece bir butona basıp versiyon numarasını girmekten başka bir iş yaptırmayacaktır. 

Öncelikle _Ant'_i indirip bazı ön hazırlıklarını halletmemiz gerekmektedir. http://ant.apache.org/ sitesinden _binary_ dağıtımı indirebilirsiniz. Daha sonra indirdiğiniz bu dosyayı bir yerlere açmanız gerekmektedir. Dosyaları açtıktan sonra sistem Path değişkenine aşağıdaki gibi Ant klasörü altında bin klasörünü eklemeniz gerekmektedir. 

<pre lang="java"> 
C:\Program Files\Apache Software Foundation\apache-ant-1.8.2\bin 
</pre>

Bunlara ek olarak ANT_HOME değişkeni belirlemeniz gerekmektedir. 

<pre lang="java"> 
C:\Program Files\Apache Software Foundation\apache-ant-1.8.2 
</pre>

Son olarak da eğer belirtilmemişse JAVA_HOME değişkenini belirlemeniz gerekmektedir. 

<pre lang="java"> 
C:\Program Files\Java\jdk1.6.0_14 
</pre>

Hepsini bitirdikten sonra konsola 

<pre lang="java"> 
ant -h 
</pre>

yazdığınızda aşağıdaki gibi bir bilgi ile karşılaşıyorsanız yükleme işlemi tamam demektir. 

![](http://www.mndeveci.com/wp-content/uploads/2011/04/ant_help.png "ant -h") 

Yazının geri kalan bölümlerinde sırasıyla bazı örnekleri, CVS'den projeyi _checkout_ / _update_ etmeyi, dışarıdan kullanılan kütüphaneler ile beraber derlemeyi, jar dosyası oluşturmayı, dosyayı _obfuscate_, _encrypt_ ve _sign_ etmekten bahsedeceğim.
