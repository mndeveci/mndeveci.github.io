---
layout: post
title: "Eclipse - Change Sets ve Mylyn"
date: 2012.02.28 00:49:00
categories: [Programming]
tags: [java, programlama, howto, change-set, cvs, eclipse]
comments: true
---
Çoğu zaman başınıza gelmiştir. Uzun süreli problem üzerinde çalışıyorsunuz, hali hazırda onlarca dosyayı değiştirmişsiniz. Siz gayet güzel işinizle uğraşırken, araya hemen yetişmesi gereken, müşteriye saç baş yolduran bir iş çıkagelmiştir. Müşteri velinimettir prensibinden yola çıkılarak, o işin daha önce ve hemen yapılması gerekmektedir. Ama şöyle bir problem ortaya çıkmıştır artık, değişen dosyalardan hangileri, hangi iş için değişmiştir. Bunu çözmek için bir kaç yöntem var: 

<!--more-->

* Birincisi, eğer iş kısa sürecekse herhangi bir iş arkadaşınızın bilgisayarını ödünç olarak kullanıp, sorunu çözüp, CVS'e commit'lemek. Ama her zaman iş 5-10 dk'lık olmayabilir ya da iş arkadaşınızın size ayıracak zamanı yoktur.

* İkinci çözüm, CVS'ten projeyi tekrar çekerek yeni bir proje altında çalışıp, değişiklikleri yapıp, commit'lemektir. Bu da projenin çekilmesi, kütüphanelerin bağlanması, derlenmesi, konfigürasyon dosyalarının yapılandırılması gibi uzun ve gereksiz bir iş yüküne sokar sizi.

* Üçüncü çözüm ise, programcının baş dostu Eclipse ile gelmekte. Eclipse'de CVS commit perspektifinde, kaynak dosyalar üzerinden değil de ChangeSet'ler üzerinden commit yapmakta.

Öncelikle change set, size proje domaini altında alt bir domain oluşturarak, yaptığınız değişiklikleri, açtığınız dosyaları o domaine bağlayıp, siz değişen dosyaları commit etmek istediğinizde, size değişik change set'ler altında onlara ait dosyaları göstererek, az önce bahsettiğimiz karışıklıktan kurtarıp, sizi daha düzenli ve hatasız çalışma imkanı sunuyor. Tüm bunlara bir de issue tracking sisteminizi entegre ettiğinizde, çalışmanıza plan program uygulayıp, daha sonra geri dönmek istediğinizde, ya da yapılan değişikliklerin ne ile ilgili olduğunu bulmak istediğinizde, size daha planlı ve kayıt tutulan bir yapı sağlıyor. 

[![](http://3.bp.blogspot.com/-8v67tJJ-k5E/T8Z73okFkQI/AAAAAAAAAag/h7RhEgnCjHE/s400/connector.jpg)](http://3.bp.blogspot.com/-8v67tJJ-k5E/T8Z73okFkQI/AAAAAAAAAag/h7RhEgnCjHE/s1600/connector.jpg) 
Öncelikle Mylyn'i kullandığınız issue tracking sistemine bağlıyorsunuz. Bunu yapmak için Mylyn'in Task Repository penceresinden yeni Repository ekleyerek yapabilirsiniz. Burada eklemek istediğiniz başka connector'lar var ise, Install More Connectors butonuna basarak kullandığınız sisteme (ör: Mantis BT) uygun bir connector yükleyebilirsiniz. Size ait connector'ü kurup issue repository'ye bağlanabilirsiniz. 

Gerekli ayarları yaptıktan sonra, issue repository'deki kayıtları görebilir, yeni issue'lar ekleyebilir, var olanlardan bazılarını üzerinize alabilir ya da kapatabilirsiniz. Kısacası Mylyn bir hata kaydının tüm süreçlerinde size yardım etmektedir. 

Şimdi gelelim çalışacağınız iş üzerinde Change Set'leri kullanmaya başlamaya. Mylyn'den yeni bir kayıt girilebilir ya da var olan kayıtlardan biri seçilebilir. Kayda sağ tıkladığınızda çıkan menude Activate derseniz, şu an aktif olarak o kayıt üzerinde çalışmış olduğunuzu belirtmiş olursunuz. Bu neler sağlar; öncelikle issue tracking sisteminizde, çalışılan zamanı tutma özelliği mevcut ise, sizin o iş üzerinizde harcadığınız zamanı ölçemeye başlar. Kaydı kendinize assing ederseniz, o hata kaydını size atamış olur. Aynı zamanda farkında olmadan, bir Change Set oluşturup, yaptığınız değişiklikleri o Change Set üzerinden yönetebilme imkanı sunacaktır sizlere. İlgili kaydı Activate ettiğinizden bu yana yaptığınız tüm değişiklikler o kayıt üzerinden gidecektir. 

[![](http://3.bp.blogspot.com/-PF5Qbw7Czvs/T8Z722nmKvI/AAAAAAAAAaY/INGge6_avtM/s400/change-set.jpg)](http://3.bp.blogspot.com/-PF5Qbw7Czvs/T8Z722nmKvI/AAAAAAAAAaY/INGge6_avtM/s1600/change-set.jpg) 

Şimdi Team Synchronizing perspektifine geçip, yukarıdaki menüden All Models yerine Change Set seçerseniz, yaptığınız değişiklikleri, Activate etmiş olduğunuz kayıt ismiyle gösterecektir (Burada başlık olarak nasıl gözükmesini istiyorsanız, ayarlardan değiştirebilirsiniz). Eğer bu perspektifte commit yaparsanız, commit comment olarak da bu Mylyn'de oluşturduğunuz kaydın başlığı, yani Change Set adı ile commit edecektir. Yine karşı tarafta bu commitleri alacak olanlara da, yine bu başlık bilgisi ve commit eden kullanıcı bilgileri ile ulaşacaktır. 

[![](http://2.bp.blogspot.com/-Are4wbvbJB0/T8Z77wRe-UI/AAAAAAAAAbQ/Qf0URNKT67k/s400/overridefadednohighlight.png)](http://2.bp.blogspot.com/-Are4wbvbJB0/T8Z77wRe-UI/AAAAAAAAAbQ/Qf0URNKT67k/s1600/overridefadednohighlight.png) 

Son olarak da ilk başta bahsetmiş olduğumuz soruna geri dönersek, aktif olarak bir issue üzerinde çalışırken, farklı bir işe başlamamız gerekirse, Mylyn'den yeni bir issue seçerek onu aktive ettiğimizde, bundan sonra yapacağımız değişiklikler bu yeni issue üzerinden gidecektir. Bu sayede Team Synchronizing  perspektifindeki ağaçta iki farklı dal olup, yapılan değişiklikleri ağacın dalları altında görebilirisiniz. Son çalıştığınız issue üzerine sağ tıklayıp commit ederseniz de sadece ona ait dosyaları CVS'e göndermiş olacaksınız. İşte bu kadar basit. 

Eclipse'deki Mylyn plugini ve Change Set konsepti ile, daha düzenli ve daha verimli bir takım çalışma ortamı yaratabilirsiniz.
