---
layout: post
title: "JPA (Hibernate) Database Auditing"
date: 2016.09.22 20:11:00
categories: [Programming]
tags: [hibernate, jpa, hibernate-envers]
comments: true
image: 
    feature: clock-1274699_1280.jpg
---
For developers, it is crucial to keep the history of the codes in a project. Such tools are named as `Version Control Systems` like Git, CVS, SVN etc. You can find all the changes with their descriptions and authors. Why don't a business unit need such a feature like this. They may need a revision of a database entity in a defined time period.

<!--more-->

If you are using JPA (Hibernate) as your entity framework, then you got the solution with [**Hiberante Envers** ](http://docs.jboss.org/envers/docs/). Hibernate Envers enables database record revisions based on the tables. You just need to annotate the entity class with `@Audited` annotation and you are good to go.

For this example, we will use the Spring Boot project that I created in my previous blog posts. You can clone this project from github.

`git clone https://github.com/mndeveci/spring-boot-angularjs-project-3-final.git`

First thing needed to do, is adding the Hibernate Envers dependency in `pom.xml` file. 

```
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-envers</artifactId>
    <version>4.3.11.Final</version>
</dependency>
```

We need to use this version in order not to conflict with the our project's Hibernate version. If you want to check this in a custom project, you can do this with [Maven Dependency Plugin](http://maven.apache.org/plugins/maven-dependency-plugin/usage.html). Running `mvn dependency:tree` will put all the dependencies in a tree format. If you are using IntelliJ Idea, you can check this in the maven window, at the Dependencies tree. And you can check the dependencies of any library at [mvnrepository.com](http://mvnrepository.com/) website.

After adding the dependency we will need an entity for the revision itself. It will keep an id of the revision and the change date.

```java
@Entity
@RevisionEntity
public class RevisionsEntity {

    @Id
    @GeneratedValue
    @RevisionNumber
    private Long revisionId;

    @RevisionTimestamp
    private Date revisionDate;

    // getters setters equals hashcode

}
```

`@RevisionEntity` will mark this Entity as the RevisionEntity. `@RevisionNumber` annotation will mark `revisionId` field as the revision number. This field is the primary key of the table and it is auto generated. And for the last field, `@RevisionTimestamp` marks it as the revision date. This field will be automatically set by Hibernate Envers when data manipulation is made.

Since we have created the base `Entity` of Hibernate Envers, now we can mark our own entites to be persisted with revision information. In order to do that, we mark our entity as `@Audited` annotation above the  class declaration.

```java
@Entity
@Audited
public class City {

    @Id
    private Integer cityCode;

    // -- remaining of the class

}
```

If we now run the application and change some of the `City` entries, Hibernate Envers will create revision records of that `City` entity automatically.

Lets create another `Controller` for listing the revisions of specific `City` object.

```java
@RestController
@RequestMapping("/api/cities/history")
public class CityRevisionController {

    @Autowired
    private CityRevisionRepository cityRevisionRepository;


    @RequestMapping("/revisions/{cityCode}")
    public List<City> getCityRevisions(@PathVariable Integer cityCode) {
        return cityRevisionRepository.listCityRevisions(cityCode);
    }



}
```

And lets create the revision repository of the `City` object.

```java
@Repository
@Transactional
public class CityRevisionRepository {

    @PersistenceContext
    private EntityManager entityManager;

    @Autowired
    private CityRepository cityRepository;

    public List<City> listCityRevisions(Integer cityCode) {
        AuditReader auditReader = AuditReaderFactory.get(entityManager);
        City cityObject = cityRepository.findOne(cityCode);

        List<Number> revisions = auditReader.getRevisions(City.class, cityCode);

        List<City> cityRevisions = new ArrayList<>();
        for (Number revision : revisions) {
            cityRevisions.add(auditReader.find(City.class, cityCode, revision));
        }

        return cityRevisions;
    }
}
```

As you can see, in order to access revisions of specific entry, we need `AuditReader` instance. And in order to instantiate one we need to use `EntityManager` and `AuditReaderFactory`. After instantiate `AuditReader` instance, you can check its methods. Basically it will allow you to do these:

* Listing the revision numbers of specific entity object
* Getting the specific item from its revision number
* Getting the date of the revision number
* Getting the revision number of specific date

Above there we are using the listing option for specific object then we iterate over the revision numbers of it and gather the history item at specific revision number. You can also get the object at a certain time.

To demonstrate, lets run the application, and make some changes over some cities, then call the `/api/cities/history/revisions/{cityCode}` with a valid (and changed) `cityCode`. For this purpose, I will edit the city `Ankara` and call the url above, the result will be:

```
[{
    "cityCode": 6, "name": "Ankara"
  }, {
    "cityCode": 6, "name": "Ankara1"
  }, {
    "cityCode": 6, "name": "Ankara"
  }, {
    "cityCode": 6, "name": "Ankara2"
  }, {
    "cityCode": 6, "name": "Ankara"
  } ]
```
The result contains all changes from the beginning. Lets add this feature to the user interface. Whenever user wants to change the a city, show them the previous changes.

In order to do that, we need to fetch all edits when user enters the edit screen. We are going to change the `adminCitiesEditController` in the `app.js` file.

```javascript
var adminCitiesEditController = function($scope, citiesService, $state, $stateParams, $http){
    $scope.city = citiesService.get({id: $stateParams.cityCode});
    $scope.edits = [];

    $http.get("/api/cities/history/revisions/" + $stateParams.cityCode)
        .success(function(data){
            $scope.edits = data;
        });

    $scope.update = function(){
        $scope.city.$update(function(){
            $state.go("admin.cities", {}, {reload: true});
        });
    };
};
adminCitiesEditController.$inject = ["$scope", "citiesService", "$state", "$stateParams", "$http"];
```

We have created an array that would contain the previous edits, called `edits` and we need to add the `$http` service to fetch them from the server.

In order to show them edit the `edit.html` file with the following changes.

```html
<br />

<div class="panel panel-warning" ng-if="edits && edits.length > 0">

    <ul class="list-group">

		<li class="list-group-item" ng-repeat="edit in edits">
			<span class="text-muted">
				Edit #{{$index}}:
			</span>
			&nbsp;
			{{edit.name}}
		</li>

	</ul>

</div>
```

That will iterate over `edits` variable and print all `name` property of city. These changes will affect the edit screen as:

![Edit Screen](http://res.cloudinary.com/mnd/image/upload/v1479655458/Untitled_gg9wxu.jpg)

This seems good but adding edit time will make it better. Lets create an enumerated class that will contain both audited entity and the revision entity.

*For the sake of simplicity I will keep the classes with the entity objects. For real world, it would be better to keep them in a data transfer object, which is a dummy one that will only keep object the properties (No entity or other information related annotations)*

```java
public class EntityWithRevision <T> {

    private RevisionsEntity revision;

    private T entity;

    public EntityWithRevision(RevisionsEntity revision, T entity) {
        this.revision = revision;
        this.entity = entity;
    }

    public RevisionsEntity getRevision() {
        return revision;
    }

    public T getEntity() {
        return entity;
    }
}
```

And we will change the `listCityRevisions` method of the `CityRevisionRepository` class.

```java
public List<EntityWithRevision<City>> listCityRevisions(Integer cityCode) {
	AuditReader auditReader = AuditReaderFactory.get(entityManager);
	City cityObject = cityRepository.findOne(cityCode);

	List<Number> revisions = auditReader.getRevisions(City.class, cityCode);

	List<EntityWithRevision<City>> cityRevisions = new ArrayList<>();
	for (Number revision : revisions) {
		City cityRevision = auditReader.find(City.class, cityCode, revision);
		Date revisionDate = auditReader.getRevisionDate(revision);

		cityRevisions.add(
				new EntityWithRevision(
						new RevisionsEntity(revision.longValue(), revisionDate), cityRevision));
	}

	return cityRevisions;
}
```

As demonstrated above, we need to fetch the edit time of the specific revision from its revision number. And we encapsulate all the information in `EntityWithRevision` class and send them back to browser. To show the revision numbers and the edit time, we need to edit the `edit.html` file as follows;

```html
<div class="panel panel-warning" ng-if="edits && edits.length > 0">
	<ul class="list-group">
		<li class="list-group-item" ng-repeat="edit in edits">
			<span class="text-muted">
				Edit #{{edit.revision.revisionId}}: @:{{edit.revision.revisionDate | date : 'yyyy.MM.dd HH:mm:ss'}}
			</span>
			&nbsp;
			{{edit.entity.name}}
		</li>
	</ul>
</div>
```


![Listing edits with their times](http://res.cloudinary.com/mnd/image/upload/v1479707644/Capture_qgnxag.jpg)

So far we tracked down the edits and list them to the user. What if we want to make changes (adding extra information to `RevisionEntity` object) during edits. Then we need to define an entity listener class which will implement `RevisionListener` in the Hibernate Envers package. Lets create a class which will extend `RevisionListener` interface.

```java
public class EntityRevisionListener implements RevisionListener {

    @Override
    public void newRevision(Object o) {
        System.out.println("New revision is created: " + o);
    }
}
```

Interface has only one method which send the `RevisionEntity` instance of current edit. You can cast them to your revision class and edit its properties. This feature is useful with user logging. When you want to log the user who just edited the entity, you can do that in this method.

After creating the `RevisionListener` class, we need to define it above the `RevisionEntity` class itself;

```java
@RevisionEntity(value = EntityRevisionListener.class)
public class RevisionsEntity {
 // ....
```
After we run the application and make some changes on cities, we can see the log line at the system out.


```
New revision is created: RevisionsEntity{revisionId=null, revisionDate=Tue Nov 22 21:29:17 EET 2016}
```

As you can see, `Hibernate Envers` gives you great ability to log changes with additional information and access the objects previous states through the time. You can find more information and detailed documentation at its official site.

You can find the source codes of this project at [https://github.com/mndeveci/spring-boot-hibernate-envers](https://github.com/mndeveci/spring-boot-hibernate-envers)
