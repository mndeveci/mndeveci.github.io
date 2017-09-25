---
layout: post
title:  "Spring Boot & AngularJS Projesi - CRUD Ekranları"
date:   2016-07-16 00:24:45
categories: [Programming]
tags: [java, spring, spring-boot, angularjs, bootstrap, hibernate]
comments: true
---

Merhabalar,

Önceki yazılarımda ilk girişimlerini yaptığımız Spring Boot & AngularJS Projesi'nde son adım olarak, veritabanında kaydedeceğimiz nesnelerin CRUD(Create/Read/Update/Delete) ekranlarını gerçekleştireceğiz.

Önceki yazılara ulaşmak için;

1. Adım [Başlangıç](https://www.mndeveci.com/spring-boot-angularjs-projesi-baslangic/)
2. Adım [Ön Yüz Ekleme](https://www.mndeveci.com/spring-boot-angularjs-projesi-on-yuz-ekleme/)

##AngularJS '$resource' Eklentisi

Kodlamaya başlamadan önce, ön yüzde kullanacağımız yeni özelliklere göz atalım. Aynen sunucu katmanında olduğu gibi, ön yüzde de `Repository` mantığında bir yapı oluşturabilirsiniz. Bunun için AngularJS'in ek kütüphanesi olan `$resource` kütüphanesini kullanacağız. `$resource` hakkında dokümantasyonu [linkte](https://docs.angularjs.org/api/ngResource/service/$resource) bulabilirsiniz.

Örnek vermek gerekirse, `locahost:8080/api/users/` restful servisiniz olduğunu düşünelim. Bunu `$resource` ile yönetmek için:
```
.query()  --->  [GET]    /api/users/   --->  Liste şeklinde tüm nesneleri döner
.get(1)   --->  [GET]    /api/users/1  --->  ID'si 1 olan nesneyi döner
.save()   --->  [POST]   /api/users    --->  POST request'i gövdesinde gönderilen 
                                             yeni nesneyi kaydeder
.delete() --->  [DELETE] /api/users/1  --->  ID'si 1 olan nesneyi siler
```

Yukarıda görmüş olduğunuz olağan davranışı ve metotlarıdır. Biz buna ek olarak, yeni nesne kaydetme ile varolan nesneyi güncellemeyi ayırt etmek istiyoruz. Bunun için de ek olarak aşağıdaki metotu ekleyeceğiz:

```
.update() --->  [PUT]    /api/users/1  --->  ID'si 1 olan ve UPDATE request'i 
                                             gövdesinde gönderilen nesneyi günceller
```

`$resource` kütüphanesini kullanabilmek için, `index.hmtl` sayfamıza aşağıdaki `script`'i ekliyoruz
```html
<script src="https://code.angularjs.org/1.5.6/angular-resource.min.js" type="text/javascript"></script>
```
ve `app.js` dosyamıza da, bağımlılık olarak `ngResource`'u da ekliyoruz.
```javascript
angular.module("myFirstAngularModule", ["ui.router", "ngResource"])
```

##Java Katmanı Kontroller Sınıfı

Tüm bunların ardından, bu yapıya uygun, Java / Spring tarafındaki `Controller` sınıfını oluşturalım. Oluşturacağımız sınıf, yukarıdaki yapı ile bire-bir örtüşmesi gerekecek.

```java
package com.mndeveci.spring.boot.rest.controller;

import com.mndeveci.spring.boot.rest.model.City;
import com.mndeveci.spring.boot.rest.repository.CityRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/cities")
public class CityController {

    @Autowired
    private CityRepository cityRepository;

    @RequestMapping
    public Iterable<City> listAll() {
        return cityRepository.findAll();
    }

    @RequestMapping(value = "/{cityCode}", method = RequestMethod.GET)
    public City get(@PathVariable Integer cityCode) {
        return cityRepository.findOne(cityCode);
    }

    @RequestMapping(value = "/{cityCode}", method = RequestMethod.POST)
    public City save(@PathVariable Integer cityCode, @RequestBody City city) {
        return cityRepository.save(city);
    }

    @RequestMapping(value = "/{cityCode}", method = RequestMethod.PUT)
    public City update(@PathVariable Integer cityCode, @RequestBody City city) {
        City cityObject = cityRepository.findOne(cityCode);

        cityObject.setName(city.getName());

        return cityRepository.save(cityObject);
    }

    @RequestMapping(value = "/{cityCode}", method = RequestMethod.DELETE)
    public boolean delete(@PathVariable Integer cityCode) {
        cityRepository.delete(cityCode);
        return true;
    }


}
```

Burada dikkat etmemiz gereken bir kaç nokta mevcut. `@RestController` ile `@RequestMapping` annotation'larını daha önce de görmüştük. Sırasıyla birisi bu sınıfın bir RESTFUL servis sunduğunu (bütün metotların sonuçlarının `JSON` vb. ara içeriğe dönüşeceğini) gösterir, ikincisi ise, bu metotların kök `URL` noktasını gösterir. Zaten dikkat ederseniz diğer metotlarda da `@RequestMapping` annotation'ını görebilirsiniz. Metotlardaki `@RequestMapping` annotation'ındaki tek fark ise, bu metotun çağrılabileceği `HTTP` metotlarınında bazılarında belirtilmesi. Yukarıda `$resource` kütüphanesini anlatırken, kaydetme işleminin `HTTP` `POST` request'i ile gerçekleşeceğini belirtmiştik. Bu yüzden `save` metotu üzerindeki `@RequestMapping` annotation'ında ek olarak `method` değişkeni de tanımlanmış durumda. 

İlk kez kullandığımız diğer iki annotation'dan biri, `@RequestBody`. Bu annotation ile, devamında verdiğimiz nesnenin, gönderilen `Request` içerisinde `JSON` olarak gösterildiğini belirtiyoruz. Gelen `JSON` nesnesinin özellikleri, oluşturulacak bu sınıfa aktarılacaktır. AngularJS ile bir nesneyi sunucuya gönderebilmek için `$http.post('/gondermek/istediginiz/url/', gonderilecekNesne)` şeklinde ikincil parametre olarak verebilirsiniz.

Son olarak da, `@PathVariable` annotation'ı. İsminden de anlaşılacağı üzere, `URL` uzantısı içerisinde parametrik olarak verebileceğimiz bir alanı okuyup, okunan değeri devamında verilen değişkene çıkarmamızı sağlar. Buradaki örnekte ise, `/api/cities/34` adres satırındaki, `34` değişkenini çıkarmamızı sağlar. Bu sayede bu değişken ile veritabanı sorguları yapabiliyor olacağız.

Burada dikkat edilecek bir diğer konu da, güncelleme işlemi sırasında, verilen nesnenin `ID` alanı ile nesneyi veritabanından isteyip, nesnenin alanlarını değiştirip, daha sonra güncelleme işlemini gerçekleştirmemiz. Bunun nedeni basit bir validasyon aslında. İstemci tarafından gönderilecek nesnenin veritabanında gerçekten var olup olmadığını test edebilmek için.

##İstemci Tasarımı

İstemci tarafında daha önceki yazımda belirttiğim üzere, `state`'lerimiz (durumlar) olacak. Bunların tanımını aşağıdaki gibi planlıyoruz.

```
├─ home
├─ viewCities
├─ admin
   └─ cities
      ├─ add
      └─ edit
```

* `home` durumunda sadece bir bilgi mesajı göstereceğiz
* `viewCities` durumunda ise sistemdeki bütün `City` nesnelerini göstereceğiz
* `admin -> cities` durumunda ise `City` nesnelerini admin olarak listeleyeceğiz
* `cities -> add` durumunda `City` nesnesinin ekleme ekranını hazırlayacağız
* `cities -> edit` durumunda `City` nesnesini düzenleme ekranını hazırlayacağız

Şimdi bütün bu durumları uygulamada oluşturmak için, `app.js` dosyasını açıp, `myModuleConfig` fonksiyonu içerisindeki durum tanımlarını aşağıdaki gibi değiştiriyoruz.

```javascript
$stateProvider.state("home", {
        url: "/",
        templateUrl: "/views/home.html"
    }).state("viewCities", {
        url: "/view-cities/",
        templateUrl: "/views/view-cities.html",
        controller: "viewCitiesController"
    }).state("admin", {
        url: "/admin/",
        templateUrl: "/views/admin/index.html"
    }).state("admin.cities", {
        url: "cities/",
        templateUrl: "/views/admin/cities/index.html",
        controller: "adminCitiesListController"
    }).state("admin.cities.add", {
        url: "add/",
        templateUrl: "/views/admin/cities/add.html",
        controller: "adminCitiesAddController"
    }).state("admin.cities.edit", {
        url: "edit/:cityCode",
        templateUrl: "/views/admin/cities/edit.html",
        controller: "adminCitiesEditController"
    });
```

Tüm bunlarda kullanacağımız `cityService` nesnemizi oluşturalım. Bu nesne tüm uygulamada `City` nesnelerini listeleme, getirme, ekleme, düzenleme ve silme işlemlerinde yardımcı olacak. Nesneyi oluşturup modülümüze `Factory` olarak tanıtıyoruz.

```javascript
var citiesService = function($resource){
    return $resource("/api/cities/:id", {
        id: "@cityCode"
    }, {
        update: {
            method: "PUT"
        }
    });
};
citiesService.$inject = ["$resource"];

angular.module("myFirstAngularModule", ["ui.router", "ngResource"])
    ...
    ...
    .factory("citiesService", citiesService);
```

Öncelikle ana iki duruma yönlendirme yapabilmek için, `index.html` sayfasına, iki duruma link veriyoruz.

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

    <title>Spring Boot & AngularJS</title>

    <!-- jquery -->
    <script   src="https://code.jquery.com/jquery-2.2.4.min.js"   integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44="   crossorigin="anonymous"></script>

    <!-- bootstrap -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap-theme.min.css" integrity="sha384-fLW2N01lMqjakBkx3l/M9EahuwpSfeNvV63J5ezn3uZzapT0u7EYsXMjQV+0En5r" crossorigin="anonymous">
    <style>
        body {
            padding-top: 20px;
            padding-bottom: 20px;
        }

        .navbar {
            margin-bottom: 20px;
        }
    </style>


    <!-- bootstrap -->
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>

    <!-- angular -->
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js" type="text/javascript"></script>
    <script src="https://code.angularjs.org/1.5.6/angular-resource.min.js" type="text/javascript"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-router/0.2.18/angular-ui-router.min.js"  type="text/javascript"></script>

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
                <ul class="nav navbar-nav">
                    <li ui-sref-active="active"><a ui-sref="viewCities">Şehirleri Listele</a></li>
                    <li ui-sref-active="active"><a ui-sref="admin">Yönetim</a></li>
                </ul>
            </div><!--/.nav-collapse -->
        </div><!--/.container-fluid -->
    </nav>

    <div ui-view></div>

</div> <!-- /container -->


</body>
</html>
```

Şimdi adım adım durumların sayfalarını ve controller nesnelerini gözden geçirelim. Anasayfa'yı aşağıdaki gibi değiştiriyoruz. Bu sayfada uygulamaya gelecek kullanıcılara kısa bir hoşgeldin mesajı veriyoruz.

```html
<!-- views/home.html -->

<h3>Spring Boot & AngularJS | Örnek Uygulamaya Hoşgeldiniz</h3>

```

Bir sonraki durum olan `viewCities` için aşağıdaki sayfayı kullanacağız.

```html
<h1>Şehirleri Listele</h1>

<div class="panel panel-info">
    <div class="panel-heading">
        <h1 class="panel-title">
            Filtrele
        </h1>
    </div>
    <div class="panel-body">
        <div class="form-group">
            <label>Filtre</label>
            <input type="text" class="form-control" ng-model="filterText" />
        </div>
    </div>
</div>

<ul class="list-group">
    <li class="list-group-item" ng-repeat="city in cities | filter: filterText">
        <span class="label label-default">
            {{city.cityCode}}
        </span>
        &nbsp;
        {{city.name}}
    </li>
</ul>
```

Burada tüm şehirleri `ng-repeat` ile listeliyoruz. Ancak tek fark olarak, listelemeyi dinamik olarak filtrelemek için, `city in cities | filter: filterText` şeklinde yazıyoruz. `cities` dizin değişkenini, yine sayfada tanımladığımız `filterText` değişkeni ile sınırlıyoruz. AngularJS içerisinde Filtre mantığı, aslında ilgili nesneyi değiştirmesi anlamına geliyor. Buradaki örnekte, listenin elemanlarını sınırlıyor. Örneğin TL simgesini göstermek istediğiniz bir filitre yazıp, bunu bir rakama uygulayabilirsiniz ki bu da AngularJS Filtre ile yapılabilecek bir durumdur. Filtreler hakkında daha detaylı bilgi almak için [linki](https://docs.angularjs.org/guide/filter) tıklayabilirsiniz.

Yine bu sayfanın controller'ı ise;

```javascript
var viewCitiesController = function($scope, citiesService){
    $scope.cities = citiesService.query();
    $scope.filterText = "";
};
viewCitiesController.$inject = ["$scope", "citiesService"];

...
...

module.controller("viewCitiesController", viewCitiesController);
```

Tüm `City` nesnelerini sunucudan getirebilmek için, bunun için yazdığımız `$resource` servisini buraya getirip, `.query()` metotu ile sunucudan tüm nesneleri listeliyoruz. Filtrelemede kullandığımız `filterText` değişkenini ise boş olarak oluşturuyoruz.

`admin` durumu için ise, ekranı ikiye bölen, sol tarafta linkleri bulundurup, sağ tarafta ise detayları tutabilmek için aşağıdaki sayfayı kullanıyoruz.

```html
<div class="row">

    <div class="col-md-2">

        <h3>Yönetim</h3>

        <ul class="nav nav-pills nav-stacked">
            <li role="presentation" ui-sref-active="active"><a ui-sref="admin.cities">Şehirleri Yönet</a></li>
        </ul>
    </div>

    <div class="col-md-10" ui-view=""></div>

</div>
```

`admin.cities` durumu için aşağıdaki sayfayı kullanıyoruz.

```html
<div ui-view=""></div>

<h4>Şehirler</h4>

<button type="button" class="btn btn-primary" ui-sref="admin.cities.add">
    Ekle
</button>

<br /><br />

<table class="table">
    <thead>
    <th>Şehir Kodu</th>
    <th>Şehir Adı</th>
    <th>Düzenle</th>
    <th>Sil</th>
    </thead>
    <tbody>
    <tr ng-repeat="city in cities">
        <td>{{city.cityCode}}</td>
        <td>{{city.name}}</td>
        <td>
            <button type="button" class="btn btn-warning" ui-sref="admin.cities.edit({cityCode: city.cityCode})">
                Düzenle
            </button>
        </td>
        <td>
            <button type="button" class="btn btn-danger" ng-click="delete(city)">
                Sil
            </button>
        </td>
    </tr>
    </tbody>
</table>
```

Sayfanın en üst kısmını, `admin.cities.add` ve `admin.cities.edit` durumları için kullanılabilir bırakıyoruz (`div` etiketi içerisinde `ui-view` yazarak). Bir tablo ile bütün şehirleri listeleyip, son iki kolonda, düzenleme ve silme işlemleri için butonlar yerleştiriyoruz. Düzenleme ayrı bir durum olduğu için onu link olarak yerleştiriyoruz. Dikkat ederseniz, link'e parametre vermek için, link adının sonuna parantez yerleştirip, içine parametre olarak `isim => değer` olacak şekilde, obje notasyonunda parametre veriyoruz. Silme işlemini ise, controller içerisindeki `delete()` metoduna bırakıyoruz.

Bu sayfanın controller'ı ise;

```javascript
var adminCitiesListController = function($scope, citiesService, $state){
    $scope.cities = citiesService.query();

    $scope.delete = function(city){
      if(confirm(city.name + " şehrini silmek istediğinize emin misiniz?")) {
          city.$delete(function(){
              $state.reload();
          });
      }
    };
};
adminCitiesListController.$inject = ["$scope", "citiesService", "$state"];

...
...

module.controller("adminCitiesListController", adminCitiesListController);
```

`$resource` nesnesinde çektiğiniz ya da oluşturduğunuz her nesne üzerinden, kaydetme, silme, güncelleme işlemlerini çağırabilirsiniz. Burada da görüleceği üzere, silme işlemi, gönderilen nesne üzerinden `$delete` metotu çağrılarak yapılıyor. Metot ilk parametre olarak, işlem bittikten sonra çalıştırılmak istenen kod parçacığını istiyor. Burada da mevcut durumu tekrar, güncel haliyle yineleme işlemini gerçekleştiriyoruz.

Bir diğer durum olan yeni şehir ekleme sayfası aşağıdaki gibidir;

```html
<h4>Şehir Ekle</h4>

<form ng-submit="save()">

    <div class="form-group">
        <label>Şehir Kodu</label>
        <input type="number" class="form-control" ng-model="city.cityCode" required="required" />
    </div>

    <div class="form-group">
        <label>Şehir Adı</label>
        <input type="text" class="form-control" ng-model="city.name" required="required" />
    </div>

    <button type="submit" class="btn btn-success">
        Ekle
    </button>


</form>
```

`form` etiketine verdiğimiz, `ng-submit` ile, form sunucya gönderildiğinde (tüm validasyonlar doğrulandıktan sonra) çağrılacak metotu belirler. Aslında bu metotda, illa bir sunucu iteraksiyonu olmayabilir. AngularJS ile bu form HTML form olmaktan çıkmış, tamamen bizim kontrolümüz altında bir form haline gelmiştir. Biz burada `save()` metodunu çağırıyoruz. Bu metot içerisinde de, `$resource` serisi üzerinden yeni şehir nesnesini kaydediyoruz.

Bu sayfanın controller metotu ise;

```javascript
var adminCitiesAddController = function($scope, citiesService, $state){
    $scope.city = new citiesService();

    $scope.save = function(){
        $scope.city.$save(function(){
            $state.go("admin.cities", {}, {reload: true});
        });
    };
};
adminCitiesAddController.$inject = ["$scope", "citiesService", "$state"];

...
...

module.controller("adminCitiesAddController", adminCitiesAddController);
```

Şehir nesnesini, `$resource` üzerinden oluşturuyoruz ki, kaydetme işlemi esnasında servis üzerindeki `$save` metotunu kullanabilmek için.  Kaydetme işlemi bittikten sonra da, `admin.cities` durumunu tekrar yüklüyoruz. Buradaki tek fark ise, üçüncü parametre olarak verdiğimiz, `{reload: true}` gidilecek durumun controller'ının tekrar çalışmasını tetikler. Bu da şehirler listesini yeni eklenecek elemanları da içerecek şekilde güncellemesini sağlar.

Son olarak da şehir nesnesini düzenleme sayfası aşağıdaki gibidir;

```html
<h4>Şehir Düzenle</h4>

<form ng-submit="update()">

    <div class="form-group">
        <label>Şehir Kodu</label>
        <input type="number" disabled class="form-control" ng-model="city.cityCode" />
    </div>

    <div class="form-group">
        <label>Şehir Adı</label>
        <input type="text" class="form-control" ng-model="city.name" required="required" />
    </div>

    <button type="submit" class="btn btn-success">
        Güncelle
    </button>


</form>
```

Ekleme sayfası ile neredeyse aynı sayfa, tek farkı ise, şehir kodu alanının salt okunur olması. Bu sayfanın controller'ı ise;

```javascript
var adminCitiesEditController = function($scope, citiesService, $state, $stateParams){
    $scope.city = citiesService.get({id: $stateParams.cityCode});

    $scope.update = function(){
        $scope.city.$update(function(){
            $state.go("admin.cities", {}, {reload: true});
        });
    };
};
adminCitiesEditController.$inject = ["$scope", "citiesService", "$state", "$stateParams"];

...
...

module.controller("adminCitiesEditController", adminCitiesEditController);
```

Burada dikkat ederseniz, değiştirmek istediğimiz şehir nesnesini yine `$resource` servisi üzerinden getiriyoruz. Hangi nesneyi istediğimizi ise, nesnenin `ID` alanını parametre olarak veriyoruz. Bu duruma gönderilen parametreleri (buradaki şehir kodunu) alabilmek için, `$stateParams` servisini bu metota ekliyoruz. Bu servis üzerinden tanımlı değişkenlere ulaşarak (burada sadece `cityCode`) gerekli çağrımızı yapıyoruz. 

Güncelleme metodu içerisinde, `$update` metodunu çağırıyoruz, dönüşte ise, ekleme sayfasında olduğu gibi, `admin.cities` durumunu güncelleyerek çağırıyoruz.

---

Tüm projenin son halini GitHub üzerinde [https://github.com/mndeveci/spring-boot-angularjs-project-3-final](https://github.com/mndeveci/spring-boot-angularjs-project-3-final) linkinde bulabilirsiniz.

**Kaynakça**

* https://docs.angularjs.org/api/ngResource/service/$resource