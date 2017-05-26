---
title: Android冷门知识
date: 2017-05-15 15:23:43
tags: Android
---
记录日常开发中冷门知识，随意而写，未整理  
### RecyclerView
RecyclerView 的 Layoutmanager 可以直接申明在 xml 中,具体代码可查看RecyclerView.createLayoutManager 方法.

```xml

<android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:scrollbars="none"
        app:layoutManager="LinearLayoutManager" />
    
```

### Layout

允许嵌套内部控件超出父布局

> android:clipChildren="false"

允许嵌套内部控件在padding间距中显示

> android:clipToPadding="false"

ScrollView 内部嵌套有ListView ，会默认定位到ListView的第一条位置，解决方案：
> 在ScroollView 内第一个布局添加 android:descendantFocusability="blocksDescendants"

如果ScroolView 中有 EditText ，会导致其无法获取焦点，另一种做法：
> 在ScroollView 内第一个布局添加 android:focusableInTouchMode="true"，
> (ScroollView 的第一个子布局也是它的唯一子布局)
