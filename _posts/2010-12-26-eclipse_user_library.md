---
layout: post
title: "Eclipse User Library"
date: 2010.12.26 01:05:00
categories: [Programming]
tags: [howto]
comments: true
---
Eclipse'de proje geliştirirken, eklemiş olduğunuz 'User Library' de, isim ya da konum değişikliği olması halinde, bu konumların tutulduğu dosyada yapılacak küçük bir değişiklik ile arayüz ile uzunca uğraşmanıza gerek kalmadan bu bilgileri değiştirebilirsiniz. Örneğin dışarıdan aldığınız bir library'nin versiyonundaki değişiklik ve onu test için farklı bir klasöre koymak olabilir. Projede de bu yeni versiyonu kullanmak istiyorsunuz ama 30 tane jar dosyasının path bilgisini değiştirmek zor tabi ki. Aşağıdaki dosyada ilgili library'lerin bulunduğu path'lerin bilgileri tutulmaktadır. Buradaki path bilgilerini değiştirerek projenizi yeniden açtığınızda istediğiniz yeni path bilgilerin girilmesini sağlayabilirsiniz. 
`.metadata/.plugins/org.eclipse.core.runtime/.settings/org.eclipse.jdt.core.prefs`
