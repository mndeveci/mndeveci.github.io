---
layout: post
title: "Java'da Generic Yapılar"
date: 2010.01.13 21:21:00
categories: [Programming]
tags: [java, programlama, generic, inheritance]
comments: true
---
Şimdi eskiden elinizdeki `Vector`'deki yapıların türlerini bilemezdiniz. Her şey size `Object` olarak gelir, siz `Cast` ederdiniz. Ancak _**Generic**_ sayesinde bu dertten kısmen kurtulduk. 

<!--more-->

Kısmen diyorum çünkü, eğer _java 1.6 sdk_ nın içindeki `Vector` source dosyasına bakarsanız, bütün get metodlarının önünde _Cast_ olduğunu görürsünüz. Aslında mekanizma aynı mekanizma, hala daha _Cast_ yapılıyor `Object`'ler. Ancak sadece katman değiştirilerek, size daha sade kod yazma imkanı sunulmuş. 

Burada ilginç bir durum da söz konusu. Şimdi olayı özetlemeye kalkarsak, önce sınıf hiyerarşisinden gireceğim; 

İki adet sınıfımız olduğunu farz edelim. Bunlardan biri diğerinden türemiş olsunlar. Şöyle ki; 

```java
class BaseClass { } 

class ChildClass extends BaseClass { }
```

Burada özel olan `ChildClass`, genel olan `BaseClass`'dır. Sadece `BaseClass` kullanılarak aslında `ChildClass`'da tutlabilir. Şöyle örnek bir metod yazmak gerekirse; 

```java
BaseClass insA = new ChildClass(); 
BaseClass insB = new BaseClass();
```

Burada da görüldüğü üzere, genel olarak tanımladığınız bir değişkene, özel sınıfın örneğini koyabilirsiniz. Yani özel sınıf genel sınıfın tüm özelliklerini taşıyacağından ötürü, genel sınıfta özel sınıf tutabilirz. Ne _compile-time_ da, ne de _run-time_ da herhangi bir hata ile karşılaşmayız. Yani tamamen doğru bir kullanımdır, sınıf türetmenin amaçlarından birisidir diyebilir hatta. Ancak şunu yapamayacağımız kesin; 

```java
ChildClass insC = new BaseClass();
```

Burada ise özelin içinde geneli tutma isteğidir. Ancak ilk okuyuşta mantıklı da gelse, aslında genel özelin tüm özelliklerini sağlayamayacağı için, bu kesinlikle yanlıştır, _compile-time_ da hata verir. 
Buraya kadar anlaştık ise işi _generic_'lere getirelim. 

Şimdi generic bir `Vector` ve metodları aşağıdaki gibi olsun (bugunlerde `Vector` ile ne uğraştıysam artık :) ); 

```java
class MyVectorTestClass { 

    Vector<String> myStrVector = new Vector<String>(); 

    public String getMyVectorElementAt(int nIndex) { 
        return myStrVector.get(nIndex); 
    } 

    public void addToMyVector(String strElement) { 
        myStrVector.add(strElement); 
    } 

    public void setMyVector(Vector givenVector) { 
        myStrVector = givenVector; 
    } 
}
```

İlk satır güzel, generic olarak yapımızı oluşturmuşuz. Bu sayede 'get' metodunu gönül rahatlığıyla `Vector`'ün get metodunun verdiği değeri döndürebiliriz. 'set' metodunda ise yine gönül rahatlığı ile `Vector`'e String'i ekleyebiliriz. Ancak eğer ki elimizdeki bu `Vector`'ü başka bir `Vector` ile değiştirmeye kalkarsak, o zaman gelen `Vector` konusunda (given `Vector`) jdk bizi herhangi bir zorlamaya sokmayacaktır. Bu da diğer `Vector`'ün generic'i ile aynı olması konusunda. Derleyip sessiz sedasız çalıştıracaktır kodunuzu, hata şu koda dahi bir şey demeyecektir;

```java
MyVectorTestClass myTestClass = new MyVectorTestClass();
Vector myIntVector = new Vector(); 
myIntVector.add(15); 
myTestClass.setMyVector(myIntVector); 
System.out.println(myTestClass.getMyVectorElementAt(0));
```

Ancak eğer ki bu kod yürümeye kalkarsa o zaman işte `ClassCastException` fırlatacaktır, en sondaki satırdan. Aslında hata bu _setMyVector_ metodunun yazımından belli. Eğer siz generic type belirttiğiniz bir `Vector`'e, generic belirtmeden yazdığınız `Vector`'u set etme imkanı verirseniz, bu olacak demektir. Ancak burada ilginç olan, hem bu _setMyVector_ metoduna bir Warning bile vermemesi, üstüne sizin farklı generic türündeki `Vector`'u set ettiğinizde sesini çıkarmaması ta ki siz bunu get ile erişmeye kalktığınızda size hata vermesi. Aslında hatalı olan bu yapı nereden baktığınızla da alakalı olarak mantıklı gelebilir. Eğer ki siz '_get metoduyla erişene kadar çalışsın, çalışabildiği yere kadar_' derseniz. Ama o zaman asıl hatanın nerede olduğunu göremeyebilirsiniz.

Şimdi toparlamaya kalkarsak, birbirinden türemiş sınıfların instance'ları genel olan özel olanı taşıyabilmektedir. Ancak genel olanın tipinden. Siz özel tipten bir instance'a genel olan bir instance'a atayamazsınız _compile-time_'da hata alırsınız. Ancak aynı durumun benzerini _Generic Vector'_de gerçekleştirmeye çalışırsanız, genel olana özeli atayabildiğiniz gibi, özel olana geneli atayabilirsiniz. Diğerinde hata verirken burada hata vermeyecektir. Ta ki siz bunlardan birine erişmeye kalkışana kadar. Her iki `Vector`'de sonuçta aynı sınıf olduğu içi, generic sınıf yapısında herhangi bir değişikliğe gitmediğini sadece _Cast_ yaptığını başta bahsetmiştim, her türlü atamaya _JVM_ izin verecektir. 
Düşünülmesi gereken bir problem midir veya problem midir bilinmez. 

Saygılarımla :)
