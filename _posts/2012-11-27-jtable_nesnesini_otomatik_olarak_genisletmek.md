---
layout: post
title: "JTable Nesnesini Otomatik Olarak Genişletmek"
date: 2012.11.27 13:38:00
categories: [Programming]
tags: [java, swing, jtable]
comments: true
image: 
  feature: jtable.png
--- 
JTable nesnesindeki tüm hücreleri, içerisindeki veriye göre, kod ile otomatik olarak kolon genişliklerini ayarlamak için; 

<!--more-->

```java
public static void autoResizeTable(JTable p_jTable, int p_nColumnIndex) {
    TableColumn jColumn = p_jTable.getColumnModel().getColumn(p_nColumnIndex);
    TableCellRenderer jHeaderRenderer = p_jtable.gettableheader().getdefaultrenderer();
    Component jHEaderComp = jHeaderRenderer.getTableCellRendererComponent(....);
    int nSize = jHEaderComp.getPrefferedSize().width;
    Component jCellComponent;
    TableCellRenderer jCellRenderer;

    for(int i = 0; i < p_jTable.getRowCount(); i++) {
        jCellRenderer = p_jTable.getCellRenderer(i, p_nColumnIndex);
        jCellComponent = jCellRenderer.getTableCellRendererComponent(...);
        nSize = Math.max(nSize, (jCellComponent.getPrefferedSize().width);
    }

    jColumn.setPrefferedWidth(nSize);
    jColumn.setWidth(nSize);
}
```

kod parçacığını kullanabilirsiniz. 

Aslında yaptığı şey tüm hücrelerin renderer komponentlerini çekip (ki renderer'ın hazırlanması esnasında, içerisindeki gerçek veriler ile hazırlandığı için, gerçek, yani olması gereken genişliklerde hazırlanmaktadır) en büyük genişliği bulup, kolonun kendisine bu genişliği vermektedir. 

Bu kod parçacığını herhangi bir kullanıcı iteraksiyonuna bağlayarak, excel'de olduğu gibi, tabloyu hücre içindeki tüm verileri gösterecek şekilde şekillendirebilirsiniz.
