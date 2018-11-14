---
layout: post
title: "Apache Ant - Bölüm 2"
date: 2011.06.27 00:19:00
categories: [Programming]
tags: [java, programlama, howto, ant, apache-ant, build, deploy, version-control]
comments: true
image:
    feature: 554px-Apache-Ant-logo.svg.png
---

Bir önceki yazımda belirttiğim üzere, Ant ile proje geliştirme süreçlerinizi iyileştirebilir ve gereksiz bazı prosedürlerden kurtulabilirsiniz. Kurulum için bir önceki okuyabilirsiniz. 

<!--more-->

Ant görevlerini tanımlayacağımız dosya build.xml dosyası olacak. Format olarak standart XML olacak. 

```xml
<project basedir="." name="myProject">
    <target name="my-target">
        <echo message="Hello World!" />
    </target>
</project> 
```

İlk tanımlayacağımız nesne project olacak. Proje nesnesine isim verip, projede çalışacağınız base directory bilgilerini değiştirebiliyorsunuz.  Komut satırından çalıştıracağınız hedefleri de target olarak tanımlıyorsunuz. Target nesnelerinin isimleri ile komut satırından çalıştırılabilirler. Örneğin yukarıdaki örnekteki işi çalıştırmak için, ant my-target yazmamız yeterli. Sonuç olarak komut satırında Hello World! yazısını göreceksiniz. 

Tanımladığınız hedeflere dependency ekleyebilirsiniz. Bir hedefin gerçekleştirilmesi için diğer bir kaç hedefi gerçekleştirmek gibi. 

```xml
<target depends="task-2, task-3" name="target-1">
    <echo message="target 1 executed " />
</target>

<target name="target-2">
    <echo message="target 2 executed " />
</target>

<target name="target-3">
    <echo message="target 3 executed " />
</target> 
```

Yukarıdaki örnekte 3 adet hedef tanımladık, target-1, target-2, target-3\. target-1 hedefine de target-2 ve target-3 hedeflerini dependency olarak ekledik. Bu sayede target-1 gerçeklenmek istendiğinde, önce target-2 sonra target-3 en son da target-1 gerçeklenecek. 

Ant yapısındaki en basit 2 elemanı gözden geçirmiş olduk. 

* Her projemiz bir build.xml dosyası içinde tanımlanmalı

* Her proje bir project tag'i içerisinde olmalı, ki bir proje olabilsin

* Projeye ait hedefleri de target isimli tag'ler içerisinde tanımlayacağız

Bu öğelerden bahsederken de ilk ant task (görev) 'ini tanımlamış olduk. Ekrana bir mesaj yazdırmak için, echo task'ini kullanıyoruz. Göstermek istediğimiz mesajı da message parametresi olarak gönderiyoruz. Burada gördüğünüz gibi parametreyi direk tırnak içerisinde karakter seti olarak verdik. Ancak her programlama dilinde olduğu gibi, bu parametreyi değişkenler yardımı ile de sunabilirdik. Değişken tanımlamak için property task'ini kullanıyoruz. 

```xml
<property name="my.message" value="Hello World!" /> 
<echo message="${my.message}" /> 
```

Yukarıda verdiğim örnekte de, önce my.message adı altında bir değişken tanımlıyoruz ve değişkene Hello World! değerini atıyoruz. Daha sonra ise echo task'inde bu değişkeni kullanıyoruz. Bir task içerisinde değişken kullanmak için ise, ${degisken.adi} şeklinde yazabiliyoruz. 

Şimdi belli başlı dosya işlemlerinden bahsedelim. 

* Dosya kopyalamak için; 

```xml
<copy file="c:/Users/mndeveci/Desktop/tmp/Main.java" todir="c:/Users/mndeveci/Desktop/projcet_sources/Main.java" /> 
```

* Dosya taşımak için; 

```xml
<move file="c:/Users/mndeveci/Desktop/tmp/Main.java" todir=" c:/Users/mndeveci/Desktop/projcet_sources/Main.java" /> 
```

* Dosya silmek için; 

```xml
<delete file="c:/Users/mndeveci/Desktop/tmp/Main.java" /> 
```

Dosya işlemlerinde klasör yapısıyla ilgili işler yaparken, ya da bir grup dosya ile işlem yaparken, fileset task'i tanımlayıp, o dosyalar/klasörler üzerinde işlem yapabilirsiniz. 

* Klasör kopyalamak için; 

```xml
<copy todir="c:/Users/mndeveci/Desktop/projcet_sources">
    <fielset dir="c:/Users/mndeveci/Desktop/tmp" />
</copy> 
```

* Klasör taşımak için; 

```xml
<move todir="c:/Users/mndeveci/Desktop/projcet_sources">
    <fielset dir="c:/Users/mndeveci/Desktop/tmp" />
</move> 
```

* Klasör silmek için; 

```xml
<delete dir="c:/Users/mndeveci/Desktop/tmp" /> 
```

* Klasör silmek için, boş klasörleri de içermesi için; 

```xml
<delete dir="c:/Users/mndeveci/Desktop/tmp" includeemptydirs="true" /> 
```

* Verdiğimiz dosya gruplarına bazı filitrelemeler de uygulayabiliriz. Örneğin proje klasörü altındaki sadece .java ve .png dosyalarını kopyalamak için; 

```xml
<copy todir="c:/Users/mndeveci/Desktop/projcet_sources">
    <fielset dir="c:/Users/mndeveci/Desktop/tmp" includes="**/*.java, **/*.png" />
</copy> 
```

* Klasör yaratmak için; 

```xml
<mkdir dir="c:/Users/mndeveci/Desktop/tmp_resources" /> 
```

Tabi her zaman işlemleriniz lokal makinedeki dosyalar üzerinden işlemeyceksiniz. Versiyon kontrol sistemi (VCS) ile alakalı da işlemleri gerçekleyebilirsiniz. Örnek olarak CVS () 'i ele alalım. Tabi CVS komutlarını çalıştırmak için CVS client'ının executable dosyasını sistem PATH değişkenine tanıtmanız gerekli. 

Örnek olarak CVS'deki projeyi lokale çekmek için; 

```xml
<cvs cvsroot=":pserver:USERNAME@SERVER:/PATH/TO/PROJECT">
    <commandline>
        <argument value="checkout" />
        <argument value="YOUR_PROJECT_NAME" />
    </commandline>
</cvs> 
```

Eğer sistem değişkenlerinde CVS_ROOT tanımlı ise, ilk parametre olan CVS_ROOT'u vermeniz gerekli değil. Parametrenin içerisinde kullanıcı adı, uzak sunucu ve sunucu üzerindeki projeye olan dosya yolu bulunmaktadır. Yine CVS sunucusuna ulaşmak için şifre dosyasına ihtiyaç var ise onu da passfile değişkeni ile tanımlayabilirsiniz. 

Yine CVS projenizi tag'lemek isterseniz; 

```xml
<cvs>
    <commandline>
        <argument value="tag" />
        <argument value="YOUR_TAG" />
    </commandline>
</cvs> 
```

şeklinde yapabilirsiniz. 

CVS ile alakalı diğer komut satırı işlemlerini bulabilirsiniz. 

Şu ana kadar yaptıklarımıza gelirsek, 

* Projemizi CVS'den çektik

* Dosyalarımızı yeni versyion numarası ile tag'ledik

* Çektiğimiz resource dosyalarından sadece proje için gerekli olanlara, yeni bir dosya adı ile taşıdık.

Şimdi bu source dosyalarımızı derleyip, gerekli jar dosyalarını oluşturup, bu jar dosyalarını sign etmek kaldı. 

Dosyaları derlemek için önce dışarıdan kullandığımız diğer library'leri eklemek gerekli. Bunun için path task'ini kullanacağız; 

```xml
<path id="my.library.classpath">
    <pathelement location="../../Documents/Libraries/OutLibrary01.jar" />
    <pathelement location="../../Documents/Libraries/OutLibrary02.jar" />
</path> 
```

id özelliğini daha sonra referans gösterirken kullanacağız. Kendi içerisindeki her bir pathelement task'i de, bir dosya yolunu göstermektedir. 

Dışarıdan eklediğimiz library'leri de referans olarak eklediğimize göre artık dosyaları derleyebiliriz. Bunun için javac task'ini kullanacağız; 

```xml
<javac classpathref="my.library.classpath" destdir="classes" srcdir="sources" /> 
```

Bu task, 'sources' klasöründeki source dosyalarını alıp, classpathref değişkeni ile belirtilen library'ler ile beraber derleyip, classes klasörü altına derlenmiş dosyaları koyacaktır. 

Eğer ki projenizde örneğin RMI Stub dosyalarını oluşturmak gerekiyorsa (yani RMI sınıflarını rmic ile derlemek gerekiyorsa) ant'de bulunan rmic task'ini kullanabilirsiniz; 

```xml
<rmic base="CLASS_FILE_PATH" destdir="DESTINATION_DIR" includes="YOUR_CLASS_FILES" /> 
```

Derledeğimiz dosyaları jar dosyalarında toparlama için jar task'ini kullanacağız; 

```xml
<jar destfile="DESTINATION.JAR" manifest="MANIFESTFILE_PATH">
    <fileset dir="CLASSES_FOLDER" includes="com/**" />
    <fileset dir="ANOTEHR_CLASSES_FOLDER" includes="net/java/YourClassName.class" />
</jar> 
```

Derlediğimiz dosyalardan istediklerimiz yine fileset'ler tanımlayıp verebiliriz. Bunun yanı sıra manifest dosyanızıda önceden tanımlayarak jar task'ine parametre olarak gönderebilirsiniz. 

Jar dosyalarımızıda oluşturduğumuza göre bu dosyaları sign edebiliriz. Bunun için signjar task'ini kullanacağız; 

```xml
<signjar keystore="your.keystore.file" tsaurl="http://url.to.sign"> storepass="your.pass" alias="your.alias" jar="jar.file.to.be.signed" /> 
```

Bu işlem içinde zaten normalde de gereken, sign işlemi için kullandığınız web sitesinin adresi, keystore dosyanızın yolu, şifreniz, alias'ınız ve sign etmek istediğiniz jar dosyanızın yolu parametreleridir. 

Bu sayede CVS üzerinde bulunan projenizin yeni versiyon çıkarma işlemini tamamlamış bulunuyoruz. Tabi son olarak artık elde ettiğiniz jar dosyalarının taşınması gereken klasöre taşıma işlemi kaldı. 

Tabi normal bir proje de burada anlattığım gibi tek satırlık dosya işlemleri ya da tek satırlık derleme işlemleri ile yazmak mümkün değil. Bu yüzden dosyanız bir süre sonra bakım yapılamaz hale gelebilir. Bunun da üstesinden gelmek için birden fazla build.xml dosyaları kullanabilirsiniz. Bunu sağlamak için de improt task'ini kullanabilirsiniz; 

```xml
<import file="OTHER_BUILD_FILES" /> 
```

Bu sayede işinizi alt işlere dağıtıp, daha küçük alanlarda bakım ve değişiklikler yapabilirsiniz. 

Tabi ki de Apache Ant'in yapabilecekleri sadace bunlarla sınırlı değil. Burada sadece ufak bir giriş yapıp, belli başlı - versiyon çıkarma işlemi için gerekli olan - bazı task'lerden bahsettik. Tüm yararlanabileceğiniz task'ler için şu göz atabilirsiniz. 

[linke](http://ant.apache.org/manual/tasksoverview.html)
