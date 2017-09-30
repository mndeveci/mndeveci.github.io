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

<pre class="prettyprint"> 
import java.awt.*; 
import javax.swing.*; 

public class test extends JFrame 
{ 
public test() 
{ 
GraphicsEnvironment env = 
GraphicsEnvironment.getLocalGraphicsEnvironment(); 
this.setMaximizedBounds(env.getMaximumWindowBounds()); 
this.setExtendedState(this.getExtendedState() | this.MAXIMIZED_BOTH); 
} 

public static void main(String[] args) 
{ 
JFrame.setDefaultLookAndFeelDecorated(true); 

test t = new test(); 
t.setVisible(true); 
} 
} 
</pre>
