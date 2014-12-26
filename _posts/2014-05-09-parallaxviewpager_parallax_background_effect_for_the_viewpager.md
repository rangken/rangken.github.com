---
layout: post
title:  "ParallaxViewPager: parallax background effect for the ViewPager"
date:   2014-5-9 5:35:00
categories: android
tags: ViewPager ParallaxViewPager
redirect_from: "/2014/05/09/parallaxviewpager-parallax-background-effect-for-the-viewpager/"
---
The so-called parallax effect is common in both web design and in mobile apps, it can be found on the major platforms, from Windows Phone to iOS through Android. According to Wikipedia, _Parallax scrolling is a special scrolling technique in computer graphics, wherein background images move by the camera slower than foreground images, creating an illusion of depth (...)_. A popular, but not over-used effect, which goes well with a ViewPager and can do wonders with the user experience. The Android SDK doesn't provide a built-in way to achieve it, so I created my own interpretation.
<!-- more -->
  
There are already a few solutions to the problem online, but none of them was a perfect match for my needs. GitHub user ChrisJenx's [Paralloid library](https://github.com/chrisjenx/Paralloid) is a good one, but it doesn't work with the ViewPager. Most solutions available are based on a custom PagerTransformer, but I tried a different approach by extending the ViewPager and implementing a custom onDraw logic. I wrapped the whole thing into a custom view called ParallaxViewPager, which is available at [GitHub](https://github.com/andraskindler/parallaxviewpager/) and to be used as a Gradle dependency.

<p  align="center">
<img src="http://localhost:4000/img/post/parallaxviewpager.gif"/>
</p>

## Using the ParallaxViewPager

Setup requires little extra effort, using the ParallaxViewPager is just like using a standard ViewPager, with the same adapter. Of course, there's no silver bullet - the developer has to supply a background tailored to the current needs (eg. the number of items in the adapter and the size of the ViewPager).

First, include it in your project as a Gradle dependency:
{% highlight groovy %}
    dependencies {
        compile 'com.andraskindler.parallaxviewpager:parallaxviewpager:0.2.0'
    }
{% endhighlight %}

After creating a ParallaxViewPager either programmatically or in a layout xml, set the background with one of the following methods, or via xml:

*   setBackgroundResource(int resid)
*   setBackground(Drawable background) or setBackgroundDrawable(Drawable background)
*   setBackground(Bitmap bitmap)

And that's all, the ParallaxViewPager is fully functional. You can tweak the experience by modifying the scrolling effect of the background. You can specify how the view should scale the image with the _setScaleType(final int scaleType)_ method. This only works with _FIT_HEIGHT_. Choose from the following parameters:

*   FIT_HEIGHT
 means the height of the image is resized to matched the height of the View, also stretching the width to keep the aspect ratio. The non-visible part of the bitmap is divided into equal parts, each of them sliding in at the proper position. This is the default value.*   FIT_WIDTH
Yo can also set the amount of overlapping with the _setOverlapPercentage(final float percentage)_ method. This is a number between 0 and 1, the smaller it is, the slower is the background scrolling. The default value is 50 percent.

## Examples

Creating the ParallaxViewPager instance inside the _onCreate()_ of an activity:

{% highlight java %}
    //...
    final ParallaxViewPager parallaxViewPager = new ParallaxViewPager(this);
    parallaxViewPager.setAdapter(new MyPagerAdapter());
    parallaxViewPager.setBackgroundResource(R.drawable.background);
    setContentView(parallaxViewPager);
    //...
{% endhighlight %}

Creating the ParallaxViewPager in a layout xml, then modifying the overlapping percentage and assigning an Adapter:

activity_main.xml
{% highlight xml %}
<com.andraskindler.parallaxviewpager.ParallaxViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/parallaxviewpager"
    android:layout_width="match_parent"
    android:background="@drawable/background"
    android:layout_height="match_parent"/>
{% endhighlight %}

MainActivity.java
{% highlight java %}
    //....
    setContentView(R.layout.activity_main);
    final ParallaxViewPager parallaxViewPager = ((ParallaxViewPager) findViewById(R.id.parallaxviewpager));
    parallaxViewPager
        .setOverlapPercentage(0.25f)
        .setAdapter(new PagerAdapter()
    //...
{% endhighlight%}
## Wrap-up

The class is far from complete, there is room for a lot of improvement, so don't hold back, ideas, feedback and pull requests are welcome! You can find the project on [GitHub](https://github.com/andraskindler/parallaxviewpager/).