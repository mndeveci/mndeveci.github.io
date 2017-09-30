---
layout: post
title: "MySql'den Yüklü Miktarda Veri Çekmek"
date: 2012.06.10 19:53:00
categories: [Programming]
tags: [java, fetch_size, jdbc, mysql]
comments: true
---
![](http://4.bp.blogspot.com/-qKbV0NUHcTA/T9T7QxVwqDI/AAAAAAAAAcg/zeeUXF3Qnj4/s1600/mysql-java-jdbc.jpg) 

Java ile MySql'den çektiğiniz yüklü miktarlarda verilerle çalışırken (30~40 GB) bazı problemler yaşayabiliyorsunuz. Yazdığınız uygulama verileri satır satır çeksede, sonuçta jdbc sürücüsü verileri tüm olarak çekmeye çalışacak, yazdığınız SQL sorgusu, heap hafızasının aşılması sebebiyle exception fırlatıp çıkacaktır. 

<!--more-->

Statement nesnesinde bulunan setFetchSize metodu ise; SQL sunucusundan istek duyulması halinde bir defada seçilecek satır sayısını belirlemektedir. Yani yazdığınız sorgu üzerinde çalışıyorsunuz. Her seferinde yeni satırı isteyip işlemlerinize başlıyorsunuz. Eğer java tarafındaki buffer da satır nesneleri tükenmiş ise, SQL server ile tekrar başlantı kurulup, belirlediğiniz fetcSize kadar java kısmına getirecektir. Ayrıca Statement nesnesini oluştururken resultSet tipini ResultSet.TYPE_FORWARD_ONLY ve resultSetConcurrency'yi ResultSet.CONCUR_READ_ONLY olarak set etmelisiniz. 

Buraya kadar herşey normal. Ancak Statement nesnesindeki setFetchSize metodu tam olarak çalışmamakta. Çünkü bir üst paragrafta anlattığım şekilde dahi yapsanız, sSQL sorgunuz tüm verisi ile birlikte java kısmına çekilecek yine aynı hata ile karşılaşacaksınız. Bununla alakalı araştırdığınızda şöyle bir  bilgiyle karşılaşacaksınız internette. Statement nesnesini yaratırken kullandığınız iki parametreye ek olarak, fetSize'ı Integer.MIN_VALUE olarak belirlediğinizde, jdbc sürücüsü bu durumu özel olarak algılayıp, MySql'den verileri tek satır olarak okuyacaktır. Ancak bu yöntem de başarısız olacaktır. 

Daha sonra jdbc sürücüsünün bir parametresi daha olduğunu öğreniyorsunuz. MySql bağlantı String'inin en sonuna useCursorFetch=true olarak tanımladığınızda, yukarıdaki tüm denemelerinizin başarılı olduğunu göreceksiniz. 

MySql sürücüsünün dökümantasyonunda yazılanlara göre setFetchSize metodu java kısmında(!) bufferlamaktadır. Program istedikçe oradan çekmektedir. useCursorFetch=true olarak tanımladığınızda ise, buffer'lama işlemini database kısmında gerçekleştirip, program istediğinde java katmanına geçirmektedir. Java tarafında her ne kadar saçma gözükse de, programın hızlı çalışmasını sağlayıp daha çok verim alındığından bahsetmekte. Buffer'lama işlemini database tarafında yaparsanız hızdan feragat edeceğiniz ama hafızada ise istediğiniz seviyelerde performans kazanabileceğinizden bahsedilmekte. 

useCursorFetch=true olarak tanımladığınızda da, java konsolunda "Incorrect key file for table '/tmp/#sql_xxxx_x.MYI'" in MySQL" şeklinde bir hata alabilirsiniz. Bu hatanın sebebi de çoğunlukla database sunucusunun temp olarak kullandığı sürücüde yer kalmadığının göstergesidir. Her ne kadar hata index ile alakalı olsa da. Bunun sebebi de, sizin attığını sorguya göre gelecek olan veri, temp klasöründe buffer'lanıp daha sonra kısım kısım java kademesine aktarılacaktır. 

Bu tarz bilginin daha göz önünde bulundurulması lazım ancak bunu bulmak için bazen 2-3 gününüzü harcayabiliyorsunuz :) 

Not: Yukarıda anlattığım tüm özellikleri bulduğum adresler: 

[http://lists.mysql.com/mysql/207831](http://lists.mysql.com/mysql/207831)
