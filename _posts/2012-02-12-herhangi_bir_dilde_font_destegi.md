---
layout: post
title: "Herhangi Bir Dilde Font Desteği"
date: 2012.02.12 18:32:00
categories: [Programming]
tags: [java, swing, font]
comments: true
image:
  feature: mandarin.jpg){: .lazyload}

---

Herhangi bir dilde kullanmak istediğiniz font'un desteğini öğrenmek için küçük bir java metodu yazabilirsiniz. Bunun için tek yapmanız gereken `Font` nesnesinin `canDisplayUpTo()` metodunu kullanmak. Metoda parametre olarak da o dilde yer alan özel bir karakter göndererek yapabilirsiniz. Örnek olarak Türkçe dilindeki karakterleri destekleyen fontları listelemek için: 

```java
Font[] allFonts = GraphicsEnvironment.getLocalGraphicsEnvironment().getAllFonts();
String sample = "\u00C7"; // unicode karakter
for (int j = 0; j < allFonts.length; j++) {
    if (allFonts[j].canDisplayUpTo(sample) == -1) {
        System.out.println(allFonts[j].getFontName());
    }
}
```

[http://helpdesk.objects.com.au/java/find-which-font-supports-chinese](http://helpdesk.objects.com.au/java/find-which-font-supports-chinese)
