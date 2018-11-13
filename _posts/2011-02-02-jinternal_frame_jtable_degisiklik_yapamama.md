---
layout: post
title: "JInternalFrame / JTable Değişiklik Yapamama"
date: 2011.02.02 22:50:00
categories: [Programming]
tags: [java, programlama, howto, jtable, jinternalframe]
comments: true
---
Java SE6'da ilginç bir problem mevcut. Eğer ki JInternalFrame üzerine JTable nesnesi koyarsanız. Tüm hücreleri değiştirilebilir olsa dahi, herhangi bir hücre de değişiklik yapamıyorsunuz. Tabi bunu sağlamak için tablonuzdaki terminateEditOnFocusLost değişkeninin true olması gerekli. 

<!--more-->

`JTable.putClientProperty("terminateEditOnFocusLost", Boolean.TRUE);`

İşte bu andan sonra tablo nesnesi üzerinde kullanıcı değişiklik yapamıyor. Değişiklik yapması için ya bir başka nesneden oraya focus'u tab tuşu ile geçirecek. Ya da tablo nesnesinin değişiklik yapılamayan hücresine bir defa tıklayıp daha sonra tıklanabilir hücresine tıklayacak. Ancak o zaman değişiklik yapabiliyor kullanıcı. 

Buna sebep olarak, içerideki tablo nesnesi focus'u üzerine alır almaz, ondan önce sahip olana focus geri gidiyor. KeyboardFocusManager nesnesine PropertyChangeListener eklerseniz, tabloya tıkladıktan sonra, focus'un tablodan bir başka nesneye (bu JInternalFrame'in kendisi ya da bir JTextField olabilir) verildiğini göreceksiniz. 

Ancak nette rast geldiğim bir yazı bunun zaten bilinen bir bug olduğu, ancak Java 7 ile beraber çözüleceği yazıyordu. Çözüm olarak ya Java 7 yi bekleyeceksiniz ya da aşağıdaki kod parçacağı gibi JInternalFrame'i extend ederek çözeceksiniz. 

```java
import java.awt.KeyboardFocusManager;
import java.awt.Window; 
import java.beans.PropertyChangeEvent; 
import java.beans.PropertyChangeListener; 
import javax.swing.JInternalFrame; 
import javax.swing.SwingUtilities; 
/* 

* Copies the fix for bug 6505027 from Java 7 

* http://hg.openjdk.java.net/jdk7/jdk7/jdk/rev/f727cac13697 

* Some support code (private) needed to be copied as well 

*/ 
public class OKInternalFrame extends JInternalFrame { 

    public OKInternalFrame() {
        addPropertyChangeListenerIfNecessary();
    }

    private java.awt.Component lastFocusOwner;

    private void setLastFocusOwner(java.awt.Component lastFocusOwner) {
        this.lastFocusOwner = lastFocusOwner;
    }

    private static boolean initializedFocusPropertyChangeListener = false;

    private static void addPropertyChangeListenerIfNecessary() {
        if (!initializedFocusPropertyChangeListener) {
            PropertyChangeListener focusListener = new FocusPropertyChangeListener();
            initializedFocusPropertyChangeListener = true;
            KeyboardFocusManager.getCurrentKeyboardFocusManager().
            addPropertyChangeListener(focusListener);
        }
    }

    private static class FocusPropertyChangeListener implements PropertyChangeListener {

        @Override
        public void propertyChange(PropertyChangeEvent e) {
            if (e.getPropertyName().equals("permanentFocusOwner")) {
                updateLastFocusOwner((java.awt.Component) e.getNewValue());
            }
        }
    }

    private static void updateLastFocusOwner(java.awt.Component component) {
        if (component != null) {
            java.awt.Component parent = component;
            while (parent != null && !(parent instanceof Window)) {
                if (parent instanceof OKInternalFrame) {
                    // Update lastFocusOwner for parent.
                    ((OKInternalFrame) parent).setLastFocusOwner(component);
                }
                parent = parent.getParent();
            }
        }
    }

    @Override
    public void restoreSubcomponentFocus() {
        if (isIcon()) {
            super.restoreSubcomponentFocus(); //delegated to super implementation, because it's correct there
        } else {
            java.awt.Component component = KeyboardFocusManager.getCurrentKeyboardFocusManager().getPermanentFocusOwner();

            if ((component == null) || !SwingUtilities.isDescendingFrom(component, this)) {
                // FocusPropertyChangeListener will eventually update
                // lastFocusOwner. As focus requests are asynchronous
                // lastFocusOwner may be accessed before it has been correctly
                // updated. To avoid any problems, lastFocusOwner is immediately
                // set, assuming the request will succeed.
                setLastFocusOwner(getMostRecentFocusOwner());

                if (lastFocusOwner == null) {
                    // Make sure focus is restored somewhere, so that
                    // we don't leave a focused component in another frame while
                    // this frame is selected.
                    setLastFocusOwner(getContentPane());
                }

                if (!lastFocusOwner.hasFocus()) {
                    lastFocusOwner.requestFocus();
                }
            }
        }
    }
}
```

Gönderinin orjinaline [linkten](http://babickababa.blogspot.com/2010/12/terminateeditonfocuslost-making.html)
