---
title: Android animated-vector 小例子
date: 2017-05-22 18:17:46
tags: Android
---

   Android 从5.0开始引入 VectorDrawable，用于实现矢量图。默认情况下，Android L 之前的版本并不支持 vector 标签，
但是在 `com.android.support:appcompat-v7:23.2.0`以上的包中，Google 对vector进行了部分兼容。以下内容，均为兼容 api14以上的代码。

* 引入appcompat,我这里引入的是25.3.1
```
compile "com.android.support:appcompat-v7:25.3.1"
```
* 在 Module的 build.gradle 文件中，加入
```
vectorDrawables.useSupportLibrary = true
```
 > 如果不加的话，只能兼容静态 vector 标签，而且兼容方式是通过生成位图来做的，在 L版本以前的设备会使用工程生成的 png文件，L版本以后不受影响，但是apk 体积会增大。

* 编写vector,这里写一个播放图标效果,静态效果如下,
<img src="/images/play_drawable.png " width="100" height="100"/>  
文件名`svg_flash_play.xml`
```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="25dp"
    android:height="25dp"
    android:viewportHeight="50.0"
    android:viewportWidth="50.0">
    <path
        android:name="p1"
        android:pathData="M12 ,38 v-28"
        android:strokeColor="#fff"
        android:strokeWidth="4" />
    <path
        android:name="p2"
        android:pathData="M21 ,38 v-30"
        android:strokeColor="#fff"
        android:strokeWidth="4"
        android:trimPathEnd="0.4" />
    <path
        android:name="p3"
        android:pathData="M29 ,38 v-34"
        android:strokeColor="#fff"
        android:strokeWidth="4" />
    <path
        android:name="p4"
        android:pathData="M38 ,38 v-32"
        android:strokeColor="#fff"
        android:strokeWidth="4"
        android:trimPathEnd="0.45" />
</vector>
```

* 编写animated-vector，animated-vector 可以看作是一个关联动画与vector适配器,文件名：`animation_svg_play.xml`
```xml
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/svg_flash_play">
    <target
        android:name="p1"
        android:animation="@animator/anim_p1"/>
    <target
        android:name="p2"
        android:animation="@animator/anim_p2"/>
    <target
        android:name="p3"
        android:animation="@animator/anim_p3"/>
    <target
        android:name="p4"
        android:animation="@animator/anim_p4"/>
</animated-vector>
```
    drawable属性关联需要执行动画的vector，这里写之前的vector文件。

* 这里的四个 target 对应四个属性动画，四个文件差不多，这里只贴其中之一,`anim_p1.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="300"
    android:propertyName="trimPathEnd"
    android:repeatCount="infinite"
    android:repeatMode="reverse"
    android:valueFrom="1"
    android:valueTo="0.3" />
```
 属性讲解：
   1. propertyName ：对应要执行动画的属性名，这里写的 `trimPathEnd`,该属性是 vector 下 path 标签的属性,属性取值范围：0-1
   2. repeatMode 有两个取值，reverse和restart，代表反转与重新开始执行动画。
   3. valueFrom 起始值，这里写的1，对应之前 `propertyName="trimPathEnd"`,动画开始的时候，第一条线是 画满的，
   4. valueTo 结束值，这里写的0.3，对应之前 `propertyName="trimPathEnd"`,第一次动画结束时，第一条线会只画到0.3的长度。

* 设置
  在 ImageView 中设置 `app:srcCompat="@drawable/animation_svg_play"` 即可，不能直接用 android:src,这样会报错的。
  代码中开启动画:  
    ```java
    ImageView imageView = (ImageView) findViewById(R.id.image_view);
    Drawable drawable = imageView.getDrawable();
    if (drawable instanceof Animatable){
         ((Animatable) drawable).start();
    }
    ```
    如果需要在java 代码中设置图片，可以这么写  
    ```
    AnimatedVectorDrawableCompat animatedVectorDrawableCompat = AnimatedVectorDrawableCompat.create(
        getContext(), R.drawable.animation_svg_play
    );
    imageView.setImageDrawable(animatedVectorDrawableCompat);
    ```

* 效果
  ![](/images/play.gif)

代码已提交 [github](https://github.com/panzhenglian/VectorDemo)

