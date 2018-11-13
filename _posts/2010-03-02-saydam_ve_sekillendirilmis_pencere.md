---
layout: post
title: "Saydam ve Şekillendirilmiş Pencere"
date: 2010.03.02 00:46:00
categories: [Programming]
tags: [java, swing]
comments: true
---
Bu kod Windows'da denenmiştir. `AWTUtilities` sınıfı standart olmadığı için başka sistemlerde problem çıkarabilir.

<!--more-->

```java
import com.sun.awt.AWTUtilities;
import java.awt.FlowLayout; 
import java.awt.Point; 
import java.awt.Shape; 
import java.awt.event.MouseEvent; 
import java.awt.event.MouseMotionListener; 
import java.awt.geom.RoundRectangle2D; 
import javax.swing.JButton; 
import javax.swing.JCheckBox; 
import javax.swing.JFrame; 
import javax.swing.JLabel; 

public class TranslucentTest implements MouseMotionListener { 

    private JFrame myFrame;
    private Point lastPoint;

    public TranslucentTest() {
        myFrame = new JFrame();
        myFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        myFrame.setLayout(new FlowLayout());
        myFrame.setUndecorated(true);

        for(int i = 0; i < 10; i++) {
            myFrame.add(new JLabel("Label" + i));
        }

        for(int i = 0; i < 10; i++) {
            myFrame.add(new JButton("Button" + i));
        }

        for(int i = 0; i < 10; i++) {
            myFrame.add(new JCheckBox("CheckBox" + i));
        }

        myFrame.addMouseMotionListener(this);
        Shape myShapeOfWindow = new RoundRectangle2D.Float(0, 0, 400, 300, 10, 10);

        AWTUtilities.setWindowOpacity(myFrame, (60f/100f));
        AWTUtilities.setWindowShape(myFrame, myShapeOfWindow);

        myFrame.setVisible(true);
        myFrame.setSize(400, 300);

    }

    public void mouseDragged(MouseEvent e) {
        int nXDiff = e.getPoint().x - lastPoint.x;
        int nYDiff = e.getPoint().y - lastPoint.y;
        nXDiff += myFrame.getLocation().x;
        nYDiff += myFrame.getLocation().y;
        myFrame.setLocation(nXDiff, nYDiff);
    }

    public void mouseMoved(MouseEvent e) {
        lastPoint = e.getPoint();
    }

    public static void main(String[] args) {
        new TranslucentTest();
    }

}
```

![]({{ site.baseurl }}/img/post/translucent.png){: .lazyload}
Örnek ekran:
