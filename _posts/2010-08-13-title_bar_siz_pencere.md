---
layout: post
title: "Title Bar'sız Pencere"
date: 2010.08.13 20:18:00
categories: [Programming]
tags: [java, programlama, swing, undecorated-frame]
comments: true
---
Java-Swing'de yarattığınız JFrame'i, işletim sistemine ait title bar olmadan göstermek için, constructor çağrısından hemen önce 

<!--more-->

```java
myFrame.setUndecorated(true);
```

demelisiniz ki, title bar ve çerçeveleyen kısım olmaksızın ekranınızı gösterebilirsiniz. Ancak dediğim gibi bunu constructor'dan sonraki ilk metod olarak çağırmanız gerekmektedir. Programınız çalışır vaziyette, frame görünür iken bunu yapmak isterseniz işler biraz daha değişecektir. Çok hassas ve düşük seviye bir işlem olduğundan ötürü öncelikle pencereyi dispose edip daha sonra bu işlemleri gerçekleştirmek gerekir. Şöyle ki; 

```java
myFrame.setVisible(false); 
myFrame.dispose(); 
myFrame.setUndecorated(true); 
myFrame.setVisible(true);
```

Bu sayede pencerenizi aslında yok edip, undecorated özelliğini değiştirip tekrar aktif hale getiriyorsunuz. Çalışma anında undecorated yaparsanız, AWT'nin iç yapısından bir exception alacaksanız. Ancak bu, forumlarda bu bir bug olarak geçmektedir. 

Tam olarak test kodu ve ekran görüntüsü; 

```java
import java.awt.BorderLayout; 
import java.awt.event.ActionEvent; 
import java.awt.event.ActionListener; 

import javax.swing.JFrame; 
import javax.swing.JLabel; 
import javax.swing.JToggleButton; 
import javax.swing.SwingUtilities; 

public class MyFrame { 

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {

            @Override
            public void run() {
                final JFrame myFrame = new JFrame("My Title Bar");
                myFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                myFrame.setLayout(new BorderLayout());
                myFrame.setUndecorated(true);
                myFrame.add(new JLabel("My Frame"), BorderLayout.CENTER);

                final JToggleButton myButton = new JToggleButton("Set Undecorated");
                myButton.setSelected(true);
                myButton.addActionListener(new ActionListener() {
                    @Override
                    public void actionPerformed(ActionEvent e) {
                        myFrame.setVisible(false);
                        myFrame.dispose();
                        myFrame.setUndecorated(myButton.isSelected());
                        myFrame.setVisible(true);
                    }
                });

                myFrame.add(myButton, BorderLayout.NORTH);
                myFrame.setSize(400, 300);
                myFrame.setVisible(true);
            }

        });

    }

}
```

![]({{ site.baseurl }}/img/post/undecorated.png){: .lazyload}
![]({{ site.baseurl }}/img/post/decorated.png){: .lazyload}
