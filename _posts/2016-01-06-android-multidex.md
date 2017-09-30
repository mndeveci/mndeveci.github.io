---
layout: post
title: "Android Multidex"
date: 2016.01.06 14:01:01
categories: [Programming]
tags: [android, libgdx, multidex, gradle]
comments: true
image: 
    feature: 65k-meme.jpg
---
Android projenizde, kullandığınız kütüphanelerin belirli bir büyüklüğü geçmesi halinde, Multidex özelliğini kullanmanız gerekmektedir. [Kaynak](https://developer.android.com/intl/ko/tools/building/multidex.html#avoid)

<!--more-->

Multidex özelliğini kullanırken dikkat etmeniz gereken yerler mevcut. Mesela konuya hata almanız üzerine yaptığınız değişiklikler ile gittiyseniz, ilk araştıracağınız yer olan stackoverflow'da aşağıdakine benzer bir yorumla karşılaşacaksınız. Projenizdeki `build.gradle` dosyasına aşağıdaki satırları ekleyin:

```
defaultConfig {
    // Enabling multidex support.
    multiDexEnabled true
}
```

Süper, tek hamle ile mükkemmel çözüme ulaştık(!). Projenizi derlediğinizde hiç bir hata almayacaksınız. Test cihazınızdaki android versiyonuyla ilgili olarak (tam olarak emin olamamakla birlikte bu olduğunu düşünüyorum) uygulamanızın başarılı bir şekilde çalıştığını da gördünüz, mükemmel. Gittiniz uygulamanızı markete yüklediniz. Arkadaşınızın telefonundan uygulamanızı indirdiniz, bir kaç ekranda gezerken hop, uygulama kapatıldı. Nasıl? Neden? Çıkan stacktrace'i kendinize gönderdiniz, eve gider gitmez kontrol ettiniz, `ClassNotFoundException`. Gradle dosyalarınızı kontrol ettiniz, kendi telefonunuzda denediniz yine hata yok. Ta ki, aynı cihazda tekrar deneyene dek. Düşündünüz, en son eklediğiniz `multiDexEnabled` özelliğini çıkarmaya karar verdiniz ancak bu sefer de proje derlenmedi.

Hata yaptığnız yer (benim hata yaptığım yer) şu oldu. `build.gradle` dosyasına yukarıdaki satırları eklemek tek başına çözüm değil! [Şu linkteki](https://developer.android.com/intl/ko/tools/building/multidex.html#mdex-gradle) dokümantasyonu okudutan sonra eksik olan kısımları tamamlamanız gerektiğini göreceksiniz. Gradle dosyasına ek olarak bir adet dependency eklemeniz gerek: `compile 'com.android.support:multidex:1.0.0'` daha sonra `AndroidManifest.xml` dosyanızı açıp, application tag'ı altına şunu eklemeniz gerekli `android:name="android.support.multidex.MultiDexApplication`. Tekrar uygulamanızı derleyip, problemli cihazlarda çalıştırdığınızda sorunun giderildiğini göreceksiniz.
