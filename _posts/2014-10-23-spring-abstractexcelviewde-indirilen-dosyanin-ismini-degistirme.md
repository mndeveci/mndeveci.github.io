---
layout: post
title: "Spring AbstractExcelView'de İndirilen Dosyanın İsmini Değiştirme"
date: 2014.10.23 07:50:00
categories: [Programming]
tags: [java, spring, excel]
comments: true
---
Spring'de `AbstractExcelView` ile oluşturduğunuz excel dosyasının adı genelde, url'in son parçası olur, kod ile bir isim vermek için:

```java
@RequestMapping(value="/excel", method = RequestMethod.POST)
public ModelAndView getReportExcel(HttpServletResponse response, Model model) {
	...
	
	response.setHeader("Content-Disposition", "attachment; filename=\"my_report_file.xls\"");
	
	...
}
```
