---
layout: post
title: "Spring Boot & AngularJS Projesi - Başlangıç"
date: 2016.04.10 14:10:00
categories: [Programming]
tags: [java, spring, boot, angularjs]
comments: true
---
Merhabalar,

Bu yazımda sonraki yazılarda kullanılmak üzere bir projenin başlangıcını yapacağım. Proje ana hatlarıyla Spring Boot ve AngularJS projesi olacak. Sunucu tarafında Java 1.8 ve Spring Boot 1.3.5, istemci tarafında ise AngularJS 1.5.6 kullanılacak.

<!--more-->

Öncelikle sunucu tarafından başlayalım. Spring Boot nedir? Spring Boot, sizi Spring teknolojileriyle, çabuk bir şekilde, geliştirmenize ortam hazırlayan bir ara teknoloji ürünüdür. Spring'in bütün teknolojileriyle anında geliştirmeye başlayabilirsiniz. Normalin aksine minimum konfigürasyon gerektirir. Ancak bu hiç bir konfigürasyon yapmayacağınız anlamına gelmez. Default bütün değerler olduğu gibi gelmekle birlikte, sizin değiştirmek istediğiniz değerlerde düzeltme yapmanız gerekecektir.

Projeye başlarken favori editörünüz ya da IDE'nizde bir maven projesi oluşturun. Oluşturduğunuz projenin `pom.xml`dosyasına aşağıdaki satırları yazın.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.mndeveci.spring</groupId>
    <artifactId>boot-rest-example</artifactId>
    <version>1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.5.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

Burada yazanlarla ilgili olarak;

Aşağıdaki satırlar projenizin, bir başka projeden türediğini belirtir. Spring Boot'un başlangıç üst projesini kullanmaktayız. Bu da hali hazırda `spring-core` kütüphanesini bağımlılık olarak eklemekte ve ek olarak bir kaç adet build plugin'i eklemektedir. Detaylı olarak incelemek isterseniz, `~/.m2/repository/org/springframework/boot/spring-boot-starter-parent/1.3.5.RELEASE/spring-boot-starter-parent-1.3.5.RELEASE.pom` dosyasına göz atabilirsiniz.
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.5.RELEASE</version>
</parent>
```

Uygulamamız bir web uygulaması olacağı için `spring-boot-starter-web` bağımlılığını da ekliyoruz. Son olarak da `maven` süreçlerinde ihtiyacı olanları karşılamak üzere `spring-boot-maven-plugin` ekliyoruz.

Bütün bunları gerçekleştirdikten sonra `pom.xml` dosyanızda herhangi bir hata ile karşılaşmazsanız problem yok demektir.

Bir sonraki adımda, geliştirdiğimiz uygulamayı test edebilmek için, boş bir adet `Controller` sınıfı yazacağız.

Uygulamanızın ana paketi altında, controller adında bir paket oluşturup, aşağıdaki kodu yazıyoruz.

```java
package com.mndeveci.spring.boot.rest.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {

    @RequestMapping("/hello")
    public String hello() {
        return "Hello...";
    }

}
```

Görüleceği üzere tek bir metot ve ondan dönen "Hello..." dizisi mevcut. Burada dikkat edilmesi gereken birinci nokta `@RestController`. Bu annotation ilgili sınıftaki bütün metotlara `@ResponseBody` annotation'ı eklemekle aynı işi yapmaktadır. Yani bu sınıfın bütün metotları birer REST servisi son noktası olarak işlev görmektedir.

Bunun yanında `@RequestMapping` annotation'ı bu metotun hangi sunucu yolundan sunulacağını belirtir. Bizim buradaki örneğimizde adres çubuğuna sunucu adresinden sonra `/hello` yazdıktan sonra bu metotun çalışacağını gösterir.

Tüm bunların ardından uygulamayı test edebilmek için, aşağıdaki `App.java` sınıfını yazıyoruz.

```java
package com.mndeveci.spring.boot.rest;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

}
```

Buradaki annotation ise, bu sınıfın bir Spring Boot Uygulaması olduğunu belirtiyor ve main metotu içerisinde ise Spring Boot uygulamasını çalıştırıyor. Şimdi bu sınıfı çalıştıralım ve [http://localhost:8080/hello](http://localhost:8080/hello) adresine gidelim.

![Örnek Ekran Görüntüsü](http://res.cloudinary.com/mnd/image/upload/v1465509582/boot-hello_pqbalz.jpg)

Gördüğünüz gibi, hello metotumuz çalıştı ve metot içerisinden dönen dizi değeri yazdırıldı.

Şimdi elimizde basit bir REST servis veren uygulama hazır. Şimdi buna veritabanı ekleyelim.

Geliştirmede kolaylık olması açısından, H2 in memory veritabanı kullanacağız. Bunun için aşağıdaki bağımlılığı `pom.xml` dosyasına ekleyelim. Veritabanına ek olarak JPA katmanı için, yine Spring Boot kütüphanesinden `spring-boot-starter-data-jpa` bağımlılığını ekleyeceğiz.
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```

Veritabanında saklamak üzere örnek bir `Entity` nesnesi oluşturalım. Bu nesne şehir bilgilerini tutsun ve içerisinde şehir kodu ve adı bilgileri bulunsun. Sınıfımızı da `model` adında bir pakete yerleştirelim.
```java
package com.mndeveci.spring.boot.rest.model;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class City {

    @Id
    private Integer cityCode;

    private String name;

    // ...
    // getters / setters / equals / hashcode / toString
}
```

Şimdi bu `Entity` nesnemizi veritabanıyla konuşturacak `Repository` sınıfını yazalım.

```java
package com.mndeveci.spring.boot.rest.repository;

import com.mndeveci.spring.boot.rest.model.City;
import org.springframework.data.repository.CrudRepository;

public interface CityRepository extends CrudRepository<City, String> {
}
```

Dikkat ettiyseniz sadece bir Interface yazıp, onu da `CrudRepository` Interface'inden türettim. Bu da Spring Data JPA projesinin bir yeniliği. Siz bir Interface yazıp bu Interface'i burada olduğu gibi `CrudRepository` Interface'inden türettikten sonra, bazı belli başlı veritabanı işlemleri için hazır metotlarınız oluyor. Eğer `CrudRepository` kaynak kodu içerisine giderseniz, listeleme (`findAll`), tek kayıt getirme (`findOne`), kaydetme ve güncelleme (`save`), silme (`delete`) metotlarının tanımlı olduğunu göreceksiniz. Uygulamayı başlattığınızda bu interface'ler üzerinden bu sınıfların "implement" edilmiş halleri uygulama classpath'i üzerinde yer alacaktır. Spring Data JPA, bunların yanı sıra, kendine ait basit bir sorgulama notasyonu da sunmakta. Örneğin;
```java
List<City> findByName(String name);
```
metotu yazdığınızda, bu metot veritabanında aşağıdaki sorguyu çalıştırıp size `City` listesi olarak geri döndürecektir.
```sql
SELECT * FROM CITY WHERE NAME = :name;
```
Bu ve bütün kullanımları için [bu linki](http://docs.spring.io/spring-data/jpa/docs/1.10.1.RELEASE/reference/html/#repositories.query-methods) inceleyebilirsiniz.

Şimdi örnek olması açısından elimizdeki `City` nesnesini listeleyen bir 'enpoint' yazalım. `WelcomeController` sınıfını açıp `CityRepository` sınıfını 'Autowire' ile bağlayıp, bu sınıftaki listeleme metotunu kullanalım.
```java
// ...
    @Autowired
    CityRepository cityRepository;

    @RequestMapping("/cities")
    public Iterable<City> getCities() {
        return cityRepository.findAll();
    }
// ...
```
Şimdi bütün değişiklikleri kaydedip `App` sınıfımzı çalıştırıp, taraycımızda [http://localhost:8080/cities](http://localhost:8080/cities) adresine gidelim. Sayfada sadece `[]` sonucunu göreceğiz. Veritabanına gidip sorgumuzu attık ancak veritabanında hiç Şehir bilgisi olmadığı için boş dizin olarak cevap döndü. Önceden de belirttiğim üzere kullandığımız veritabanı H2 In Memory veritabanı. Bu da uygulama çalıştığında yeni boş bir veritabanı oluşturup sonra tabloları oluşturacaktır. Uygulama çalışırken yapılacak değişiklikler bellek de olup biteceği için, uygulamayı kapattığınız anda yaptığınız tüm değişiklikler uçacaktır.

Şimdi başlangıçta test verisi oluşturmak üzere kod parçacığını oluşturalım. `App.java` sınıfımız içerisine aşağıdaki satırları ekliyoruz.

```java
    @Bean
    public CommandLineRunner demo(final CityRepository cityRepository) {
        return s -> {

            List<City> cities = new ArrayList<>();
            
            City cityInfo = new City();
            cityInfo.setCityCode(34);
            cityInfo.setName("Istanbul");
            cities.add(cityInfo);

            cityInfo = new City();
            cityInfo.setCityCode(6);
            cityInfo.setName("Ankara");
            cities.add(cityInfo);

            cityInfo = new City();
            cityInfo.setCityCode(35);
            cityInfo.setName("Izmir");
            cities.add(cityInfo);

            cityRepository.save(cities);
        };
    }
```
`CommandLineRunner` sınıfı, Spring Boot ile, uygulama başladıktan sonra komut satırından çalıştırabileceğiniz kod parçacıkları yazmanıza olanak sağlıyor. Bu tanımladığımız 'Bean' sınıfına, `CityRepository` sınıfnı parametre olarak veriyoruz. Bu da aynı sınıfı `@Autowired` ile bağlamakla aynı. Daha sonra da örnek olması için 3 adet şehir nesnesi oluşturup kaydediyoruz.

Şimdi tekrar uygulamayı çalıştırıp [http://localhost:8080/cities](http://localhost:8080/cities) adresine gidersek aşağıdaki çıktıyı göreceğiz;

`[{"cityCode":6,"name":"Ankara"},{"cityCode":34,"name":"Istanbul"},{"cityCode":35,"name":"Izmir"}]`

Test verisi, artık biz uygulamayı her çalıştırdığımızda veritabanı içerisinde hazır olacaktır.

Şimdiye kadar bir Spring Boot projesi oluşturup, o projeye 'web' ve 'jpa' özelliklerini ekleyip, test için H2 veritabanı kullanıp bir REST Web Servisi oluşturduk. Bundan sonraki yazılarda, bu projeye ön yüz ekleyip, o önyüz üzerinden veri listeleme ve değiştirme işlemleri gerçekleştireceğiz.

Proje kaynak kodlarını [https://github.com/mndeveci/spring-boot-angularjs-project-1](https://github.com/mndeveci/spring-boot-angularjs-project-1) adresinden klonlayabilirsiniz.


**Kaynakça**

* [Building RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Spring Data JPA Referans Dokümanı](http://docs.spring.io/spring-data/jpa/docs/1.10.1.RELEASE/reference/html/)
