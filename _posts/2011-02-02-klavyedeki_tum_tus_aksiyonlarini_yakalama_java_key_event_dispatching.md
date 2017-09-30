---
layout: post
title: "Klavyedeki Tüm Tuş Aksiyonlarını Yakalama | Java Key Event Dispatching"
date: 2011.02.02 23:33:00
categories: [Programming]
tags: [java, programlama, howto, swing, key events]
comments: true
---
Java / Swing'de herhangi bir nesne üzerinde klavye aksiyonlarını yakalamak istiyorsanız bunun için bir kaç yol mevcut. 

<!--more-->

Birincisi ekleyerek bunu yapabilirsiniz. Bu sayede kullanıcının herhangi bir klavye aksiyonunu 3 farklı zaman diliminde yakalayabilirsiniz. Birincisi tuşa basılırken (key pressed), ikincisi tuş geri çekilirken (key released), son olarak da bu üçünün sonunda işlem tamamlanınca (key typed). Ancak keyTyped metodu içerisinde bazı kısıtlamalar mevcut. Mesela kombinasyon şeklindeki aksiyonları yakalayamazsınız (_ctrl + a_ gibi). Diğer tüm bilgileri sınıfındaki dökümantasyonda bulabilirsniz. 

İkinci çözüm olarak da, ilgili kontrole tuş aksiyonları _bind_ edebilirsiniz. Bunun için kontrolün _input map_ nesnesinebir klavye aksiyonunu bir anahtar ile kaydedip, _action map_ nesnesine de bu anahtar ile aksiyon ekleyip gerçekleştirebilirsiniz. Örnek olarak; 

<pre class="prettyprint">component.getInputMap().put(KeyStroke.getKeyStroke("F2"), "doSomething"); 
component.getActionMap().put("doSomething", anAction); 
//anAction implements javax.swing.Action</pre>

Bu çözüm, panel ya da diyalog gibi ekranlarda kullanılarak kullanıcının herhangi bir girdi giremeyeceği nesneler üzerinde etkili olur. Kullanıcıların klavye ile girdi girebileceği (JTextField ya da JTextArea gibi) nesnelerde birinci çözüm daha efektif olarak kullanılabilir. 

Eğer ki üstteki iki çözümde size yeteri kadar yardım etmediyse, üçüncü çözüm olarak da aktif pencere üzerinden geçen tüm klavye aksiyonlarını yakalayabileceğiniz bir sınıf yazabilirsiniz. arayüzünden türeyen bir sınıf yazarak ve bu sınıfı metodu ile KeyboardFocusManager'a  ekleyerek o andan sonra o pencere üzerinden geçen tüm klavye aksiyonlarını yakalayabilirsiniz. Bu çözümü kullanmak için yukarıdaki iki çözümünde sizin için işe yaramamış olmaması gerekmektedir. Zira bu çözüm oldukça düşük seviyeli bir çözüm olmakla beraber, bazı istenmeyen sonuçlara da yol açabilir. Çünkü klavye aksiyonu yukarıdaki iki örnekten de önce bu sınıftan geçecektir. Bu sınıftan geçtikten sonra yukarıdaki yöntemler ile işlenecektir. O yüzden siz bu seviyede aksiyonu _consume_ [KeyboardFocusManager.addKeyEventDispatcher()](http://download.oracle.com/javase/6/docs/api/java/awt/KeyboardFocusManager.html#addKeyEventDispatcher(java.awt.KeyEventDispatcher))
