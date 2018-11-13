---
layout: post
title: "Resim Boyutlama / Image Resizing"
date: 2011.03.13 14:31:00
categories: [Programming]
tags: [java, programlama, howto, image-resize]
comments: true
---
Java'nın kendi Image nesnesinde getScaledInstance(int newWidth, int newHeight, int hints) şeklinde bir metot mevcut ancak bazı durumlarda (açıkçası tam olarak belirleyemediğim) yeniden boyutlandırmayı başaramıyor. Bunun yerine biraz daha uzun olan ancak boyutlandırma konusunda daha başarılı bir kod parçası mevcut. O da Graphics nesnesine, elimizdeki imaj nesnesinden yeni bir imaj nesnesi üzerine çizdirmek. 

<!--more-->

Basit olarak yazmak gerekirse; 

```java
BufferedImage resizedImage = new BufferedImage(IMG_WIDTH, IMG_HEIGHT, type);
Graphics2D g = resizedImage.createGraphics(); 
g.drawImage(originalImage, 0, 0, IMG_WIDTH, IMG_HEIGHT, null); 
g.dispose();
```

Ancak boyutlandırılan resimdeki kalitenin çok düşük olduğunu göreceksiniz. Buna ek olarak RenderingHints ekleyerek durumu biraz daha güzelleştirebiliriz. 

```java
BufferedImage resizedImage = new BufferedImage(IMG_WIDTH, IMG_HEIGHT, type);
Graphics2D g = resizedImage.createGraphics(); 
g.drawImage(originalImage, 0, 0, IMG_WIDTH, IMG_HEIGHT, null); 
g.setComposite(AlphaComposite.Src); 
g.setRenderingHint(RenderingHints.KEY_INTERPOLATION,RenderingHints.VALUE_INTERPOLATION_BILINEAR); 
g.setRenderingHint(RenderingHints.KEY_RENDERING,RenderingHints.VALUE_RENDER_QUALITY); 
g.setRenderingHint(RenderingHints.KEY_ANTIALIASING,RenderingHints.VALUE_ANTIALIAS_ON); 
g.dispose();
```

[http://www.mkyong.com/java/how-to-resize-an-image-in-java/](http://www.mkyong.com/java/how-to-resize-an-image-in-java/ "mkyong.com")
