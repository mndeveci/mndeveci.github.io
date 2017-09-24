---
layout: post
title:  "Data Encryption with JPA/Hibernate and Jasypt"
date:   2017-03-17 20:07:19
categories: [Programming]
tags: [java, spring, hibernate, jpa, jasypt]
comments: true
---
In this post I am going to give a quick demo about how [Jasypt](http://www.jasypt.org/) (Encryption framework for Java) can be used with Hibernate.

I am going to use my latest Spring Boot & AngularJS project which can be found at [github](https://github.com/mndeveci/spring-boot-angularjs-project-3-final).

With Jasypt, you can easily encrypt Strings (texts), Numbers, dates, binaries and application configuration files. And you can use these encryption types in hibernate too ([You can find all available types in here](http://www.jasypt.org/hibernate.html)). But you can not query nor map entities with encrypted fields. If you want to filter results you need to do that at Java level which the data will be decrypted.

To begin coding, I cloned the repository above and copy `pom.xml` file and `src` folder in another folder called `spring-boot-hibernate-jasypt`. This example project keeps a list of `City` object in the database. I am going to encrypt, `name` file of the city and persist cities with that information. I am also going to create another entity, which will be used to retrieve `City` objects without any decryption(in order to test the encryption).

I am going to add the `Jasypt` dependencies at the `pom.xml` file.


```xml
...

<properties>
    <java.version>1.8</java.version>
    <jasypt.version>1.9.2</jasypt.version>
</properties>

...

<dependencies>

    ...

    <dependency>
        <groupId>org.jasypt</groupId>
        <artifactId>jasypt</artifactId>
        <version>${jasypt.version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.jasypt</groupId>
        <artifactId>jasypt-hibernate4</artifactId>
        <version>${jasypt.version}</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

In order to encrypt a field in the entity object you need to define `TypeDef` (which is a hibernate functionality). In `TypeDef` we are going to define the type class and encryption parameters (like encryption method and encryption password). For this encryption we are going to use `EncryptedStringType` type class, `PBEWithMD5AndDES` algorithm type, and a securely generated password. We are going to add this type definition at the top of `City` entity. And for encrypting `name` field we are going to annotate it with type definition.

```java
import org.hibernate.annotations.Parameter;
import org.hibernate.annotations.TypeDef;
import org.jasypt.hibernate4.type.EncryptedStringType;

@Entity
@TypeDef(
    name = "encryptedStr",
    defaultForType = EncryptedStringType.class,
    typeClass = EncryptedStringType.class,
    parameters = {
        @Parameter(name = "algorithm", value = "PBEWithMD5AndDES"),
        @Parameter(name = "password", value = "s56k3N5xh782")
    }
)
public class City {

    @Id
    private Integer cityCode;

    @Type(type = "encryptedStr")
    private String name;

    // getters and setters

}
```

So with these settings, whenever a `City` object will be persisted, its `name` field will be encrypted and whenever retrieving object this field will be decrypted.

Since we are using an in-memory-database for the ease of development, we can not observe the data objects, in order to see fields without encryption, we are going to create bare version of `City` object. This will look like;

```java
@Entity
@Table(name = "City")
public class CityWoEnc {

    @Id
    private Integer cityCode;

    private String name;

    // getters and setters
}
```

And its repository class;

```java
package com.mndeveci.spring.boot.rest.repository;

import com.mndeveci.spring.boot.rest.model.City;
import org.springframework.data.repository.CrudRepository;

import java.util.List;

public interface CityRepository extends CrudRepository<City, Integer> { }
```

And finally a controller to reach it;

```java
package com.mndeveci.spring.boot.rest.controller;

import com.mndeveci.spring.boot.rest.model.CityWoEnc;
import com.mndeveci.spring.boot.rest.repository.CityWoEncRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/cities-wo-enc")
public class CityWoEncController {

    @Autowired
    private CityWoEncRepository cityWoEncRepository;

    @RequestMapping("/")
    public Iterable<CityWoEnc> listCitiesWoEnc() {
        return cityWoEncRepository.findAll();
    }

}
```

We set up everything we need, start the Spring Boot application and lets test the decrypted end point first. When we go to [http://localhost:8080/api/cities/](http://localhost:8080/api/cities/) we will see list of cities with name property is decrypted;

```javascript
[
    {
        "cityCode": 6,
        "name": "Ankara"
    } , {
        "cityCode": 34,
        "name": "Istanbul"
    } , {
        "cityCode": 35,
        "name": "Izmir"
    }
]
```

And when we go to [http://localhost:8080/api/cities-wo-enc/](http://localhost:8080/api/cities-wo-enc/) application is going to list cities but the name property will stay encrypted;

```javascript
[
    {
        "cityCode": 6,
        "name": "C4An6y2MErbVsh4aQb68fA=="
    } , {
        "cityCode": 34,
        "name": "OHqHYj+Rm+Yp+6uQsxvqK0GUF0/J6lwG"
    } , {
        "cityCode": 35,
        "name": "NCQk6WkMhZb/FqmeldNkXg=="
    }
]
```

Some fancy city names :)

Whenever a DBA or any user nor application tries to see the data, it is going to be like the one above. Of course there are many possible situations where an encryption will be needed.

For my case, we needed an internal application for voting the `Work of the Month`, and the votes are private, we needed to block people to see the voting results until the winner is selected. But our problem is that nearly everyone has read-only access to those tables. So we implement the application with Jasypt and all the necessary fields are encrypted.

You can access the final source code of this demo at [https://github.com/mndeveci/spring-boot-hibernate-jasypt](https://github.com/mndeveci/spring-boot-hibernate-jasypt)