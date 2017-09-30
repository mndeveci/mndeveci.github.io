---
layout: post
title: "Java'da Generic Yapılar"
date: 2010.01.13 21:21:00
categories: [Programming]
tags: [java, programlama, generic, inheritance]
comments: true
---
Şimdi eskiden elinizdeki _Vector_'deki yapıların türlerini bilemezdiniz. Her şey size _Object_ olarak gelir, siz _Cast_ ederdiniz. Ancak _**Generic** _sayesinde bu dertten kısmen kurtulduk. 

<!--more-->

Kısmen diyorum çünkü, eğer _java 1.6 sdk_ nın içindeki _Vector_ source dosyasına bakarsanız, bütün get metodlarının önünde _Cast_ olduğunu görürsünüz. Aslında mekanizma aynı mekanizma, hala daha _Cast_ yapılıyor _Object_'ler. Ancak sadece katman değiştirilerek, size daha sade kod yazma imkanı sunulmuş. 

Burada ilginç bir durum da söz konusu. Şimdi olayı özetlemeye kalkarsak, önce sınıf hiyerarşisinden gireceğim; 

İki adet sınıfımız olduğunu farz edelim. Bunlardan biri diğerinden türemiş olsunlar. Şöyle ki; 

<pre class="prettyprint">class BaseClass { 
} 

class ChildClass extends BaseClass { 
}</pre>

Burada özel olan _ChildClass_, genel olan _BaseClass_'dır. Sadece _BaseClass_ kullanılarak aslında _ChildClass_'da tutlabilir. Şöyle örnek bir metod yazmak gerekirse; 

<pre class="prettyprint">BaseClass insA = new ChildClass(); 
BaseClass insB = new BaseClass();</pre>

Burada da görüldüğü üzere, genel olarak tanımladığınız bir değişkene, özel sınıfın örneğini koyabilirsiniz. Yani özel sınıf genel sınıfın tüm özelliklerini taşıyacağından ötürü, genel sınıfta özel sınıf tutabilirz. Ne _compile-time_ da, ne de _run-time_ da herhangi bir hata ile karşılaşmayız. Yani tamamen doğru bir kullanımdır, sınıf türetmenin amaçlarından birisidir diyebilir hatta. Ancak şunu yapamayacağımız kesin; 

<pre class="prettyprint">ChildClass insC = new BaseClass();</pre>

Burada ise özelin içinde geneli tutma isteğidir. Ancak ilk okuyuşta mantıklı da gelse, aslında genel özelin tüm özelliklerini sağlayamayacağı için, bu kesinlikle yanlıştır, _compile-time_ da hata verir. 
Buraya kadar anlaştık ise işi _generic_'lere getirelim. 

Şimdi generic bir _Vector_ ve metodları aşağıdaki gibi olsun (bugunlerde _Vector_ ile ne uğraştıysam artık :) ); 

<pre class="prettyprint">class MyVectorTestClass { 

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

}</pre>

İlk satır güzel, generic olarak yapımızı oluşturmuşuz. Bu sayede 'get' metodunu gönül rahatlığıyla _Vector_'ün get metodunun verdiği değeri döndürebiliriz. 'set' metodunda ise yine gönül rahatlığı ile _Vector_'e String'i ekleyebiliriz. Ancak eğer ki elimizdeki bu _Vector_'ü başka bir _Vector_ ile değiştirmeye kalkarsak, o zaman gelen _Vector_ konusunda (given_Vector_) jdk bizi herhangi bir zorlamaya sokmayacaktır. Bu da diğer _Vector_'ün generic'i ile aynı olması konusunda. Derleyip sessiz sedasız çalıştıracaktır kodunuzu, hata şu koda dahi bir şey demeyecektir; 

<pre class="prettyprint">MyVectorTestClass myTestClass = new MyVectorTestClass(); 
Vector myIntVector = new Vector(); 
myIntVector.add(15); 
myTestClass.setMyVector(myIntVector); 
System.out.println(myTestClass.getMyVectorElementAt(0));</pre>

Ancak eğer ki bu kod yürümeye kalkarsa o zaman işte _ClassCastException_ fırlatacaktır, en sondaki satırdan. Aslında hata bu _setMyVector_ metodunun yazımından belli. Eğer siz generic type belirttiğiniz bir _Vector_'e, generic belirtmeden yazdığınız _Vector_'u set etme imkanı verirseniz, bu olacak demektir. Ancak burada ilginç olan, hem bu _setMyVector_ metoduna bir Warning bile vermemesi, üstüne sizin farklı generic türündeki _Vector_'u set ettiğinizde sesini çıkarmaması ta ki siz bunu get ile erişmeye kalktığınızda size hata vermesi. Aslında hatalı olan bu yapı nereden baktığınızla da alakalı olarak mantıklı gelebilir. Eğer ki siz '_get metoduyla erişene kadar çalışsın, çalışabildiği yere kadar_' derseniz. Ama o zaman asıl hatanın nerede olduğunu göremeyebilirsiniz. 

Şimdi toparlamaya kalkarsak, birbirinden türemiş sınıfların instance'ları genel olan özel olanı taşıyabilmektedir. Ancak genel olanın tipinden. Siz özel tipten bir instance'a genel olan bir instance'a atayamazsınız _compile-time_'da hata alırsınız. Ancak aynı durumun benzerini _Generic Vector'_de gerçekleştirmeye çalışırsanız, genel olana özeli atayabildiğiniz gibi, özel olana geneli atayabilirsiniz. Diğerinde hata verirken burada hata vermeyecektir. Ta ki siz bunlardan birine erişmeye kalkışana kadar. Her iki _Vector_'de sonuçta aynı sınıf olduğu içi, generic sınıf yapısında herhangi bir değişikliğe gitmediğini sadece _Cast_ yaptığını başta bahsetmiştim, her türlü atamaya _JVM_ izin verecektir. 
Düşünülmesi gereken bir problem midir veya problem midir bilinmez. 

Saygılarımla :)
