---
layout: post
title: "Writing Spring Boot Applications with Kotlin"
date: 2017.09.30 20:23:00
categories: [Programming]
tags: []
comments: true
image: 
  feature: kotlin_800x320.png
---

A couple of years ago [JetBrains announced](https://blog.jetbrains.com/kotlin/2011/07/hello-world-2/) that they are working on a new JVM language for their internal works. And later on they announce this new language, Kotlin. Ever since then the language got more famous, and got the attention of Google. And now, Google officially supports in Android developments.

<!--more-->

There are couple of things for Kotlin, that got people's attention. The very first reason is Kotlin works flawlessly with Java. If you want to try other JVM languages, you often come to a point that you need to choose either Java or the language you are trying for a project. You can not use both. For example Scala has its own collection library, and if you want to try Scala within Java, you need to alter so many things in the code with the existing libraries. And that will prevent to use wonderful features of Scala. But if you use Kotlin, you are good to go. You can build your project using both Kotlin and Java, or you can use any Java library without having major issues (due to some design principles, Kotlin may cause slightly minor issues with some regular Java libraries/frameworks).

So why Kotlin? Kotlin has a lot of great feature. It lets you to write same business logic with less code, and it pushes developer in a way that follows best patterns in programming. For example, every class and every class method you create in Kotlin is final. You need to say explicitly that the class or method is **not final**. Otherwise you can not extend the class nor override the method. 

Every parameter you define in Kotlin is not null. It means you can not assign null to that parameter. You need to mark that variable as null assignable, if you are not sure it could be null or not. One example could be using a Java library. If you want to use a Java library and call one its methods, you will see that compiler will warn you to change variable type to nullable.

Some of the other features;

* It has a class type of data. Which means you can define your data holder classes (entities or dto's) with minimal effort.

* It has built-in singletons. When you define your class as object, then you have single object in your living environment.

* It allows you to write extension functions. Now you can create functions on classes that you imported from another library without extending the classes.

* Stream functions are available directly at collections. You don't need to start a stream, you can just filter, map, flat map or reduce collections directly.

* It can be compiled to Java6, which is very good for Android developers. You have all the new features that Java 8 have in your Android development environment.

* It can be compiled to Javascript. You can now write server side and client side codes in one language.

* And it can be compiled to native executables. That means, in the future you may not need JVM to run Kotlin applications because they would be platform specific executables.

To show a quick application demo, about how to write a Spring Boot Application with Kotlin, I will use my last project, which I create a simple application to show Hibernate Envers.

Lets create a new application and add the `pom.xml` file, we need to change it slightly to work with Kotlin.

```xml
    ...
    
    <properties>
        <kotlin.compiler.incremental>true</kotlin.compiler.incremental>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <kotlin.version>1.1.60</kotlin.version>
    </properties>

    ...

    <dependencies>

        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-stdlib-jre8</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
        
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-reflect</artifactId>
            <version>${kotlin.version}</version>
        </dependency>

    </dependencies>

    <build>
        <sourceDirectory>${project.basedir}/src/main/kotlin</sourceDirectory>
        <plugins>

            ...

            <plugin>
                <groupId>org.jetbrains.kotlin</groupId>
                <artifactId>kotlin-maven-plugin</artifactId>
                <version>${kotlin.version}</version>
                <executions>
                    <execution>
                        <id>compile</id>
                        <phase>compile</phase>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>test-compile</id>
                        <phase>test-compile</phase>
                        <goals>
                            <goal>test-compile</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <jvmTarget>1.8</jvmTarget>
                </configuration>
            </plugin>

        </plugins>
    </build>

</project>

```

If you are using JIdea, then it is a piece of cake to use Kotlin, you just need to create a Kotlin file, and IDE will ask you to add Kotlin support. Lets walk through the changes in `pom.xml` file. First we define a property to use it Kotlin version. At the moment of writing this post, `1.1.3-2` is the latest version. As a dependency, we need to add `kotlin-stdlib-jre8`. And finally we need to add maven plugin to automatically compile our `.kt` files.

I am going to re-create entities in Kotlin. First `City` entity. 
```kotlin
@Entity
@Audited
data class City(
        @Id
        var cityCode: Int = 0,

        var name: String = "")

```
That's it. The power of data classes is shown above here, this class has all the possible constructors (no-args to all-args), getters, setters, equals, hashcode and toString function with just 7 lines of code. The parenthesis after class name is the constructor of Kotlin class. The only ugly thing that you would see here, you need to define properties of class in the constructor and your eyes need some time to get used to it :)

`RevisionsEntity` and `EntityWithRelation` classes are;
```kotlin
data class RevisionsEntity (

        @Id
        @GeneratedValue
        @RevisionNumber
        var revisionId: Long = 0L,

        @RevisionTimestamp
        var revisionDate: Date? = null

)


data class EntityWithRevision<T> (

        var revision: RevisionEntity,

        var entity: T
)
```
Again we are using the power of data classes. One down side of data class is you can not extend a data class. There are lots of debates on the internet about this. At first, they said that it would be allowed to extend any data class for sake of simplicity and escape from ambiguity about hashcode and equals methods. But later on they decide to introduce this (extending data classes) in the future versions.

Moving onto the repositories, first `CityRepository`
```kotlin
interface CityRepository : CrudRepository<City, Int> {

    fun findByName(name: String) : List<City>

}
```
As you can extend classes or interfaces with colon. 

And `CityRevisionRepository`
```kotlin
@Repository
@Transactional
class CityRevisionRepository(
        @PersistenceContext
        var entityManager: EntityManager,

        @Audited
        var cityRepository: CityRepository) {

    fun listCityRevisions(cityCode: Int): List<EntityWithRevision<City>> {
        val auditReader = AuditReaderFactory.get(entityManager)
        return auditReader.getRevisions(City::class.java, cityCode)
            .map {
                val cityRevision = auditReader.find(City::class.java, cityCode, it)
                val revisionDate = auditReader.getRevisionDate(it)
                
                EntityWithRevision(RevisionsEntity(it.toLong(), revisionDate), cityRevision)
            }
    }

}
```
And here you can see that we again defined class parameters in constructor, and these parameters are available in the class. And if we look at the return statement, you can see that *'embedded streams'* there. In Java, you need to call methods to convert collection objects to streams and vice versa. But in Kotlin, you don't need to. Here we are calling `map` method directly and we are returning it. If we were using Java we need to first call `stream` method and do the mapping and at last line we need to call `collect(Collectors.toList())` method to return lists.

Lets create our entity revision listener;
```kotlin
class EntityRevisionListener: RevisionListener {

    override fun newRevision(revision: Any?) {
        println("New revision is create: $revision")
    }

}
```
And we see some of the other functioanlities of Kotlin. First the type `Any`, it basically means any type can be here, like `Object` in Java. Second the question mark after the type. This marks the variable as nullable, which means the variable that you receive here can be null. You can also see that `override` is a keyword in Kotlin, you need to explicitly say, I'm overriding this function from parent class. And last thing is, you can use your variables, inside String's. As you can see I am referring `revision` parameter, in print out string by simply calling `$revision`. No ugly concatenate operators or long function calls to format my string.

Next stop is, controllers;
```kotlin
@RestController
@RequestMapping("/api/cities")
class CityController(val cityRepository: CityRepository) {


    @GetMapping
    fun listAll() = cityRepository.findAll()
    
    @GetMapping("/{cityCode}")
    fun get(@PathVariable cityCode: Int) = cityRepository.findOne(cityCode)
    
    @PostMapping("/{cityCode}")
    fun save(@RequestBody city: City) = cityRepository.save(city)
    
    @PutMapping("/{cityCode}")
    fun update(@RequestBody city: City, @PathVariable cityCode: Int): City {
        val cityObject= cityRepository.findOne(cityCode)
        cityObject.name = city.name
        return cityRepository.save(cityObject)
    }
    
    @DeleteMapping("/{cityCode}")
    fun delete(@PathVariable cityCode: Int) = cityRepository.delete(cityCode)

}
```
In the controller class above, you can see that a method can be directly mapped to another method within the method signature.
Instead of filling a method body, with only one return statement, you can directly map a method to another instance member method.
And the return type of the upper method (in this case, controller's `findAll`, `get`, `save` and `delete`) is gathered from the referenced method.

And the other controller would be;

```kotlin
@RestController
@RequestMapping("/api/cities/history")
class CityRevisionController(val cityRevisionRepository: CityRevisionRepository) {

    @GetMapping("/revisions/{cityCode}")
    fun getCityRevisions(@PathVariable cityCode: Int) = cityRevisionRepository.listCityRevisions(cityCode)

}
```

And like the previous one, it only contains single method which would be mapped to the repository function, you can just map them each other.

And for the last part, the Spring application initializer class;

```kotlin
@SpringBootApplication
class App {

    @Bean
    fun demo(cityRepository: CityRepository) : CommandLineRunner {
        return CommandLineRunner {
            cityRepository.save(
                    listOf(
                            City(34, "Istanbul"),
                            City(6, "Ankara"),
                            City(35, "Izmir")
                    )
            )
        }
    }

}

fun main(args: Array<String>) {
    SpringApplication.run(App::class.java, *args)
}
```

The first thing that you are going to see that, you don't need a class to run a main method. Indeed you don't need a class for any method in Kotlin.
That means you don't need bunch of `Utility` classes (classes with static methods for doing utility stuff).

And for the other part, just like the java version of this application, we are going to create a `CommandLineRunner` bean to initialize some data in the system.
As you can see, Koltin also have some metods for easily creating collections. For example in here, we use `listOf(...)` method to create a list from the given elements.

After all of this, if you run the application from IDE or from command line (`gradle bootRun`) you would see the same application that we implemented previously.

This project just covers some of the functionalities of Kotlin, if you want to deep dive in Kotlin, you can watch the following conference talks

* [Introduction to Kotlin by Hadi Hariri](https://youtu.be/X1RVYt2QKQE)
* [An introduction to Kotlin by example by Dmitry Kandalov](https://youtu.be/pjC8C1xid3k)
* [Kotlin for Java Programmers by Venkat Subramaniam](https://www.youtube.com/watch?v=7EVXypZDOos)

And you can find the source code of this blog post in github; [https://github.com/mndeveci/kotlin-spring-boot-hibernate-envers](https://github.com/mndeveci/kotlin-spring-boot-hibernate-envers)

Thanks for reading!