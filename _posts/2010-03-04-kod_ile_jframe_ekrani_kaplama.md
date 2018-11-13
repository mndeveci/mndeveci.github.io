---
layout: post
title: "Kod İle JFrame Ekranı Kaplama"
date: 2010.03.04 00:46:00
categories: [Programming]
tags: [java, swing]
comments: true
---
Java da JFrame i kod ile tam ekrana getirmek için. 

<!--more-->

```java
import java.awt.*; 
import javax.swing.*; 

public class Test extends JFrame {
    public Test() {
        GraphicsEnvironment env =
        GraphicsEnvironment.getLocalGraphicsEnvironment();
        this.setMaximizedBounds(env.getMaximumWindowBounds());
        this.setExtendedState(this.getExtendedState() | this.MAXIMIZED_BOTH);
    }

    public static void main(String[] args) {
        JFrame.setDefaultLookAndFeelDecorated(true);
        Test t = new Test();
        t.setVisible(true);
    }
} 
```
