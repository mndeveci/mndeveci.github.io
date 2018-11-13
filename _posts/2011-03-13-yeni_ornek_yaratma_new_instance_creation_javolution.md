---
layout: post
title: "Yeni Örnek Yaratma / New Instance Creation (Javolution)"
date: 2011.03.13 15:39:00
categories: [Programming]
tags: [java, programlama, howto, xml, javolution, new-instance]
comments: true
---
**Javolution** kütüphanesinin _[XML serialize / deserialize](http://www.mndeveci.com/2010/06/22/java-xml-parse-javolution/ "Java XML Parse – Javolution")_ işlemlerinden daha önce bahsetmiştim. Ancak orada da dikkat edeceğiniz üzere, bir sınıfın özelliklerini okurken, sınıfın boş _constrcutor_ ile oluşturulması ve her elemanın (_primitve type_ olmayan) _initialize_ edilmiş olması gerektiğinden bahsetmiştik. Ancak bu durum bazı koşullar altında gerçeklenemeyebilir. Örneğin, _enum_ bir sınıfa uygulayamazsınız. Çünkü sınıfın baştan öncelikle _initialize_ olması gerek. _Enum_ bir sınıfta ise, zaten tüm işlem o _initialize_ anında olmaktadır. Örneğin aşağıdaki _enum_ sınıfını ele alırsak; 

<!--more-->

```java
public enum MyEnum {
    RIGHT, LEFT, TOP, BOTTOM
}
```

_Javolution_ bu sınıfa uygun olarak gerçeklenemez. Çünkü _initialize_ etmeniz gerek ve eğer siz _RIGHT_ olarak _initialize_ ettiyseniz, onu daha sonradan _LEFT_ olarak atayamazsınız. Çünkü _read_ metoduna zaten _initialize_ edilmiş nesne gelecektir. Yani kurgu tamamen sınıfın boş _constructor_ ile oluşturulup daha sonra içerideki elemanların sırayla oluşturulmasından geçer. 

Ancak bunu kırmanın bir yolu mevcut. Şöyle ki; hali hazırda _XMLFormat_ sınıfını yazdıktan sonra, _read_ ve _write_ olmak üzere iki adet _abstract_ metodu yazmanız gerek. Ama _XMLFormat_ sınıfının içerisinde bir metot daha mevcut. O da _newInstance_ metodu; 

```java
public T newInstance(Class <T>cls, InputElement xml) throws XMLStreamException;
```

Bu metot'da, o boş _constructor_ ile nesne oluşturan kısım. Ancak dikkat edeceğiniz gibi bu metoda da _InputElement_ nesnesi gelmektedir. Yani XML'e ait olan verileri bu metot içerisinde de okuyabiliriz. Örnek vermek gerekirse yukarıdaki _enum_ sınıfı için; 

```java
public enum MyEnum {

    RIGHT, LEFT, TOP, BOTTOM;

    static final XMLFormat XML = new XMLFormat(MyEnum.class) {
        public MyEnum newInstance(Class<MyEnum> cls, InputElement xml) throws XMLStreamException {
            return MyEnum.valueOf(xml.getAttribute("toStr", ""));
        }

        public void write(MyEnum myEnum, OutputElement xml) throws XMLStreamException {
            xml.setAttribute("toStr", myEnum.toString());
        }

        public void read(InputElement xml, Point point) throws XMLStreamException { }
    };

}
```

Yukarıda da görüleceği üzere, sınıfn yeni örneğini kendimiz yaratarak boş _constructor_ ile oluşturulmasının önüne geçebiliyoruz.
