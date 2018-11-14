---
layout: post
title: "Oracle Apex PLSQL'den Mesaj Verme"
date: 2014.10.31 01:56:00
categories: [Programming]
tags: [oracle, apex, plsql]
comments: true
---
Oracle Apex'de PLSQL işlemi sonrasında kullanıcıya mesaj vermek için:

```sql
begin  
	IF :P1_COMPUTER like 'PC%'  THEN  
		INSERT INTO...;  
		COMMIT;  
		apex_application.g_print_success_message := '<span style="color:green">Computer created</span>';  
	ELSE  
		apex_application.g_print_success_message := '<span style="color:red">No valid computer name</span>';  
	END IF;  
END;
```
