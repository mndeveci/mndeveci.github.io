---
layout: post
title: "Spring Boot & AngularJS Projesi - Ön Yüz Ekleme"
date: 2016.06.15 21:23:23
categories: [Programming]
tags: [java, spring, angularjs, spring-boot, bootstrap, angular-ui-router]
comments: true
image: 
    feature: AngularJS-large.png
---
Merhaba,

Spring Boot ve AngularJS ile uygulama geliştirme yazısının ikinci adımındasınız. İlk yazı için [linki tıklayabilirsiniz](https://www.mndeveci.com/spring-boot-angularjs-projesi-baslangic/).

<!--more-->

##Spring Boot Projesine Statik Kaynak Ekleme


Yazının bu adımındaki kütüphaneleri anlatmaya geçmeden önce, Spring Boot ile statik kaynakları nasıl sunacağımza bir göz atalım. Spring Boot projenizdeki statik kaynakları, `/static`, `/public`, `/resources` veya `/META-INF/resources` klasörlerine kopyalayarak istemci tarafından erişilmelerine izin verebilirsiniz [Kaynak](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-spring-mvc-static-content).

Örnek olarak uygulamamızın ana sayfasını ekleyelim, projenizin `src/main/resources` klasörü altına, `public` klasörü ekleyip, bu klasör altına `index.html` dosyası ekleyip, içeriğine aşağıdakileri yazalım.
```html
<!DOCTYPE html>
<html>
<body>
    Hello world from static content...
</body>
</html>
```

Şimdi projemizi çalıştırıp, [http://localhost:8080/](http://localhost:8080/) adresine gidelim. Aşağıdaki sayfanın bizi karşıladığını göreceksiniz.

![Ekran Görüntüsü](http://res.cloudinary.com/mnd/image/upload/v1466025350/Screenshot_from_2016-06-16_00_15_23_tqwte4.png)

Projemizin bundan sonraki adımlarında kullanacağımız ve istemci tarafını etkileyecek dosyaları `src/main/resources/public` klasörü altından sunabileceğiz.

##AngularJS

Şimdi uygulamamıza ön yüz kütüphaneleri ekleyeceğiz. Bunlardan AngularJS ile başlayalım. AngularJS kökeni GWT'ye uzanan bir Web Uygulama Geliştirme Framework'ü. Web uygulamarında ön yüz iş mantığını yazan ekipler tarafından, daha kullanışlı bir katman olması için geliştirilmiştir. AngularJS HTML etiketleri içerisine, yine HTML etiketlerine benzer etiket veya bu etiketlere ait özellikler ekleyerek kontrol etmenize olanak sağlıyor.

Gelin uygulamamıza AngularJS'in ana kütüphanesini ekleyelim. [AngularJS](https://angularjs.org/)'in websitesine gidip, `CDN` adresini `https://ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js` alıp, az önce projemize eklediğimiz `index.html` dosyasının `<head>` etiketleri arasına ekleyelim.
```html
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js" 
            type="text/javascript"></script>
```

Daha sonra sayfanın AngularJS tarafından yönetildiğini söyleyebilmemiz için (bir başka deyişle sayfanın bir AngularJS uygulaması olduğunu belirtebilmemiz için), `html` etiketi içerisine, `ng-app` özelliğini boş olarak ekliyoruz. En basit özelliklerden biri olan HTML şablonları (*template*) ve `ng-model` bağlayıcısını (`binding`) kullanacağız. Tüm bunların ardından, `index.html` sayfamız aşağıdaki gibi olacaktır.

```html
<!DOCTYPE html>
<html ng-app>
<head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js"
            type="text/javascript"></script>
</head>
<body>
    <div>
        <label>İsminiz:</label>
        <input type="text" ng-model="myName" />
    </div>

    <h2>Merhaba; {{myName}}</h2>

</body>
</html>
```

Burada dikkat etmemiz gereken birinci nokta, `{{}}` parantezleri arasına yazılan değişken. AngularJS'de HTML dosyasına şablon oluşturmak için, uygulamanızdaki değişkenleri yazdırmak istediğinizde, yukarıdaki çift süslü parantez notasyonunu kullanabilirsiniz. Buradaki örnekte, `myName` bir değişken ve o değişken `<h2>` etiketi içerisinde yazdırılmaktadır. Dikkat ederseniz, `myName` değişkeni bir yerde daha geçmekte, o da `<input>` etiketi içerisi. Burada ise, input etiketi içerisindeki `ng-model` özelliği içerisine yazılmış durumda. `ng-model` sizin uygulamanızdaki değişkenleri ile HTML Form nesneleri arasındaki bağı kurmak için kullanılıyor. Mesela buradaki örnekte olduğu üzere, `<input>` form nesnesinin değeri, uygulamadaki `myName` değişkenine bağlı.

Şimdi sayfayı yenilediğimizde aşağıdaki ekran görüntüsünü alacağız;

![İlk Angular Ekran Görüntüsü](http://res.cloudinary.com/mnd/image/upload/v1466026503/Screenshot_from_2016-06-16_00_34_52_dqvmpq.png)

Sayfada dikkat edeceğiniz ilk özellik, siz form'a yazmaya başlar başlamaz sayfa içerisinin değiştiğini göreceksiniz. İşte burada AngularJS'in gücünü görmeye başlıyoruz :) Aynı sayfayı jQuery ile aynı yapıda yapmak istesek, `input` etiketine bir değişiklik dinleyici ekleyip, her değişiklik yaşandığında, `h2` etiketi içeriğini değiştirmek olabilirdi. Gördüğünüz üzere basit bir~iki HTML etiketi ile çözülebilen bir iş için, kontrol mantığı yazmak gerekecekti. Şimdi bu gücü şöyle düşünün. Elinizde dizi var ve siz diziyi sayfada `ul`, `li` etiketleri ile gösteriyorsunuz. AngularJS ile, diziniz her değiştiğinde sayfadaki 'ul' listesi de otomatik olarak güncellenecek. Bu örneği ileride sayfalarda listeleme yaparken göreceğiz.

###Yönlendirme (Routing) Ekleme

Uygulamamız tabi ki her zaman, değişken göster ve değişkenin değerini değiştir şeklinde olmayacak. Değişken değeri sunucudan okunacak, sonra tekrar sunucuya yazılacak. Sunucudan gelecek verilere göre sayfa değişecek vs. vs. Bütün bunları kontrol edebilmemiz için her sayfaya `Controller` sınıfları yazabileceğiz.

Yine aynı şekilde uygulamamız tek bir durum'dan (*state*) oluşmayacak. Bazı ekranlarda listeleme yapıp, bir sonraki ekranda değişiklik yapabileceğiz. Tüm ekranların da kendilerine ait `Controller` sınıfları olacak ve her ekran kendi iş kontrolünü düzenleyecek.

Tüm bunları yapabilmek için AngularJS kendine ait yönlendirme (*routing*) kütüphanesine sahip. Ancak bu kütüphane bazı noktalarda yetersiz kalabiliyor. Biz onun yerine [UI Router](https://github.com/angular-ui/ui-router) eklentisini kullanacağız. `UI Router` eklentisi, normal yönlendirme eklentisinin yapamadığı durum hiyerarşisi oluşturabilme kapasitesine sahip. Yani tüm uygulamanız bir ağaç yapısında tasarlayıp, bu ağacın her bir dalını bir durum olarak tanımlayabilirsiniz. Alt durumlar üst durumları bire bir aynısı olup, üst durumun bir kısmında çizdirilecektir. Şimdi `UI Router` eklentisini sayfamıza ekleyelim. Sayfamızın `head` etiketi arasına aşağıdaki satırları ekleyelim.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-router/0.2.18/angular-ui-router.min.js"
            type="text/javascript"></script>
```

Şimdi uygulamamızda bazı şeyler ciddiye binmeye başladığı için, klasörleme yapımızda bazı değişiklikler yapacağız. `index.html` dosyasını eklediğimiz klasöre, `js` isminde bir klasör daha ekleyip, içerisine `app.js` dosyası ekleyin. Yeni dosyamızın içerisini doldurmaya başlamadan önce bu dosyayı `index.html` sayfamızdan çağıralım.

```html
<script src="js/app.js" type="text/javascript"></script>
```

Şimdi `app.js` dosyasını açıp ilk modülümüzü yazmaya başlayalım.
```javascript

var myModuleConfig = function($stateProvider, $urlRouterProvider){

    // herhangi bir eşleşme olmaması halinde,
    //    hangi sayfaya gideceği durumunu belirtir
    $urlRouterProvider.otherwise("/");

    // bu satırdan itibaren, uygulamadaki
    //    durumları tanımlamaya başlıyoruz
    $stateProvider.state("home", {
        // adres çubuğunda görünecek yolun tanımı
        url: "/",
        // bu durumun sayfa olarak gösterim dosyasını işaret eder
        templateUrl: "/views/home.html"
    });

};
// yukarıda fonksiyon tanımında kullandığımız servis vs objelerinin
//    angular içerisinden buraya inject edilmesini sağlar
myModuleConfig.$inject = ["$stateProvider", "$urlRouterProvider"];

// angular'daki modül tanımı aşağıdaki gibidir, myFirstAngularModule modül adıdır.
//    modül adından sonraki dizi ise, modülün bağımlılıklarını belirtir
//    bizim sadece UI Router bağımlılığımız mevcut olduğu için onu belirtiyoruz
angular.module("myFirstAngularModule", ["ui.router"])
    // yukarıda yazdığımız config sınıfını modülümüze uyguluyoruz
    .config(myModuleConfig);
```

İlk iş mantığımızı oluşturmaya başladık. Şimdi `index.html` dosyasını tekrar açıp, `ng-app` özelliğini aşağıdaki gibi değiştirelim. Aşağıdaki değişiklik ile, bu sayfayı, `myFirstAngularModule` isimli modül yönetir diyoruz.
```html
<html ng-app="myFirstAngularModule">
```

Daha sonra sayfa içerisine yazdığımız, kod parçacığını alıp, `js` klasörünün yanına `views` klasör oluşturup, `home.html` dosyası içeriğine yazalım.

```html
<!-- views/home.html -->
<div>
    <label>İsminiz:</label>
    <input type="text" ng-model="myName" />
</div>
<h2>Merhaba; {{myName}}</h2>
```
`index.html` dosyasındaki `body` etiketi içerisini de aşağıdaki gibi değiştirelim. Bu değişiklik, ana yönlendirme durumunun içeriğinin, sayfanın hangi alanına çizdirileceği belirtilmiş olur.
```html
<div ui-view></div>
```
Şimdi tüm değişiklikleri kaydedip, tekrar [http://localhost:8080/](http://localhost:8080/) adresine gidelim.

![UI Router'lı Hali](http://res.cloudinary.com/mnd/image/upload/v1466028859/Screenshot_from_2016-06-16_01_14_08_mpws0n.png)

Az önceki sayfamız bire bir çalışmakta, ancak dikkat ederseniz, adres çubuğunun sonuna `#/` eklenmiş durumda. Bu da `UI Router` kütüphanesinin uygulamazı eklendiğini ve şu an `/` ile gösterilen ve ismi `home` olan durumu görüntülemekteyiz.

###Ön Yüz Kontrolü Ekleme

Şimdi sayfamıza biraz da iş mantığı ekleyelim. Bu sayfamızı / durumumuzu kontrol edecek kodumuzu, `Controller`'ı yazalım.
```javascript
// sayfamızı yönetecek kod parçacığı
// $scope servisi, sayfanızdaki değişkenlere, 
//     controller üzerinden erişmenizi sağlar
//     o yüzden ön yüzden erişilmesi istenen tüm değişkenleri
//     $scope altnda tanımlamanız gerekir
var myHomeController = function($scope){
    // name adında bir değişken tanımlıyoruz, 
    //   bunu input'a model olarak tanımlayacağız
    $scope.name = "";
    
    // listelemede göstereceğimiz bütün isimlerin listesi
    $scope.names = [];
    
    // isim listesine isim ekleme fonksiyonu
    $scope.addName = function() {
        $scope.names.push($scope.name);
        $scope.name = "";
    }
};
myHomeController.$inject = ["$scope"];

...
...

angular.module("myFirstAngularModule", ["ui.router"])
    .config(myModuleConfig)
    // myHomeController controller nesnesini kaydediyoruz
    .controller("myHomeController", myHomeController);
```

Yazdığımız `Controller`'ı durumumuz ile bağlıyoruz.

```javascript
$stateProvider.state("home", {
        url: "/",
        templateUrl: "/views/home.html",
        // durumu kontrol eden kod parçaçığı
        controller: "myHomeController"
    });
```

Son olarak sayfamızı (`home.html`), `Controller` sınıfındaki değişken ve aksiyonlara göre yeniden düzenliyoruz.

```html
<!-- views/home.html -->

<div>
    <label>İsminiz:</label>
    <input type="text" ng-model="name" />
    <button type="button" ng-click="addName()">Kaydet</button>
</div>

<ul>
    <!-- ng-repeat, bir dizi değişkeni üzerinde dönmenize olanak sağlar
         burada olduğu üzere, isimler üzerinde dönüp onu liste
         olarak yazdırmamızı sağlıyor -->
    <li ng-repeat="selectedName in names">
        {{selectedName}}
    </li>
</ul>
```

Tüm bunları kaydedip, sunucuyu tekrar başlattığımızda, karşımıza aşağıdaki ekran gelecektir.

![Listeye eleman ekleme](http://res.cloudinary.com/mnd/image/upload/v1466199991/list_example_jb837q.gif)

Siz her ekle butonuna bastığınızda, `input` içerisindeki değer, `Controller` içerisindeki `names` değişkenine ekleniyor, ve arka plandaki her değişiklik anında ön yüzde fark ediliyor, burada `names` dizinini listelememizde olduğu gibi.

İstemci tarafındaki iş kodunu yazmaya başladık ancak biraz da gözümüze hitap etmesi için bir tane de `css` kütüphanesi ekleyelim. Burada da *Twitter Bootstrap* kullanalım. [getbootstrap.com](getbootstrap.com) adresine gidip, `CDN` adreslerini alalım.

```html
    <!-- bootstrap -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap-theme.min.css" integrity="sha384-fLW2N01lMqjakBkx3l/M9EahuwpSfeNvV63J5ezn3uZzapT0u7EYsXMjQV+0En5r" crossorigin="anonymous">
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>
```

*Bootstrap*'ın çalışabilmesi için gerekli olan jQuery'yi de ekleyelim.

```html
    <!-- jquery -->
    <script   src="https://code.jquery.com/jquery-2.2.4.min.js"   integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44="   crossorigin="anonymous"></script>
```

Yine *Bootstrap*'ın sitesinden örnek şablonu (Navbar olanı seçtim) `index.html` sayfasına uygulayalım. `index.html` sayfasının son hali aşağıdaki gibidir.

```html
<!DOCTYPE html>
<html lang="en" ng-app="myFirstAngularModule">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- The above 3 meta tags *must* come first in the head; any other head content must come *after* these tags -->
    <meta name="description" content="">
    <meta name="author" content="">
    <link rel="icon" href="../../favicon.ico">

    <title>Navbar Template for Bootstrap</title>

    <!-- jquery -->
    <script   src="https://code.jquery.com/jquery-2.2.4.min.js"   integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44="   crossorigin="anonymous"></script>

    <!-- bootstrap -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap-theme.min.css" integrity="sha384-fLW2N01lMqjakBkx3l/M9EahuwpSfeNvV63J5ezn3uZzapT0u7EYsXMjQV+0En5r" crossorigin="anonymous">
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>
    <style>
        body {
            padding-top: 20px;
            padding-bottom: 20px;
        }

        .navbar {
            margin-bottom: 20px;
        }
    </style>


    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js"
            type="text/javascript"></script>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-router/0.2.18/angular-ui-router.min.js"
            type="text/javascript"></script>

    <script src="js/app.js" type="text/javascript"></script>
</head>

<body>

<div class="container">

    <!-- Static navbar -->
    <nav class="navbar navbar-default">
        <div class="container-fluid">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="#">Spring Boot & AngularJS</a>
            </div>
            <div id="navbar" class="navbar-collapse collapse">
            </div><!--/.nav-collapse -->
        </div><!--/.container-fluid -->
    </nav>

    <div ui-view></div>

</div> <!-- /container -->


</body>
</html>
```

`home.html` dosyasını da aşağıdaki gibi değiştirelim.

```html
<!-- views/home.html -->

<div class="form-group">
    <label>İsminiz:</label>
    <input class="form-control" type="text" ng-model="name" />
</div>

<button type="button" class="btn btn-primary" ng-click="addName()">Kaydet</button>

<hr />

<ul class="list-group">
    <!-- ng-repeat, bir dizi değişkeni üzerinde dönmenize olanak sağlar
         burada olduğu üzere, isimler üzerinde dönüp onu liste
         olarak yazdırmamızı sağlıyor -->
    <li class="list-group-item" ng-repeat="selectedName in names">
        {{selectedName}}
    </li>
</ul>
```

Tüm bunların ardından sayfamızın son hali aşağıdaki gibi olacaktır.

![Bootstrap eklenmiş hali](http://res.cloudinary.com/mnd/image/upload/v1466202105/Screenshot_from_2016-06-18_01_21_25_auwyfp.png)

Serinin bu yazısında, uygulamamızdan nasıl statik kaynaklarını sunabileceğimize göz attık. Sonrasında uygulamamıza *AngularJS* kütüphanesini ekledik ve örnek sayfamızı oluşturduk. Daha sonra ise yönlendirme mekanizmasını *UI Router* kütüphanesi ile gerçekleştirdik. Son olarak ise uygulamamıza *Bootstrap* kütüphanesini ekleyip ön yüzümüzü de geliştirdik.

Uygulamanın bu yazıdaki son halini, [https://github.com/mndeveci/spring-boot-angularjs-project-2](https://github.com/mndeveci/spring-boot-angularjs-project-2) adresinden klonlayabilirsiniz.

Serinin bundan sonraki yazısında ise, ön yüz ile arka planı birbirine bağlayıp, basit bir kaç listeleme ekranı gerçekleştireceğiz.

**Kaynakça**

* http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-spring-mvc-static-content
* https://angularjs.org/
* https://github.com/angular-ui/ui-router
* http://getbootstrap.com/
