---
layout: post
title: "Spring @Repository ve @Service Sınıfları"
date: 2014.09.18 04:33:00
categories: [Programming]
tags: [java, spring, hibernate]
comments: true
---
Spring + Hibernate projesinde, database ile alakalı olmayan metodları, `@Repository` ile annotate ettiğiniz sınıflara koymayın. Gereksiz yere `jdbc` connection'ı alıp geri verecektir. Onun yerine `@Service` ile annotate ettiğiniz katmanda implemente edin.

Tam tersi olarak, `@Service` sınıflarında, jdbc ile ilgili metotları tutmayın. Onları tek bir metot içinde `@Repository` sınıfında hatta mümkünse `@Transactional` olan bir sınıf/metot içerisinde gerçekleştirin. Bu işlemlerin hem aynı Transaction içinde olmasını hem de tek bir `jdbc` bağlantısı üzerinden tamamlanmasını sağlayacaktır
