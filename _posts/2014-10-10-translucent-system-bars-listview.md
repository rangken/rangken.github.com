---
layout: post
title:  "Translucent system bars + ListView done right"
date:   2014-10-10 12:00:00
categories: android
tags: RecyclerView ListView
---
About a year ago, Google introduced the translucent UI in [KitKat](https://developer.android.com/about/versions/kitkat.html), making the system bars transparent, with a hint of black gradient. After activating, apps are able to draw under the status bar (the one on top of the screen, with the notifications) and the navigation bar (this is optional, only present devices with no hardware buttons, at the bottom of the screen, contains the back, home and app switcher keys), which can result in a slick and modern user experience if done right.
<!-- more -->
But while the UI can spread under both bars, touch events in these areas will be ignored by the app, making the items in the bottom and top partially unclickable (see the screenshot on the left). This is why the layout has to be adjusted accordingly. But what can be done with a fullscreen ListView to keep the neat scrolling effect where items go under the status and nav bars, while the first and last items remain clickable?

My first solution was to add an empty view to the head and the tail of the ListView, so that the first and last items aren't blocked by the system bars. But as pointed out by _darktiny_ in the comments, this method is too complex. He suggested a simpler technique, so I modified the post.

The idea is to add a top and bottom padding matching the status and navigation bars (if they're translucent), and setting the _clipToPadding_ attribute to false. The latter means that when the list items and the padding moves simultaneously when scrolling the list.

As a first step, enable the translucent system bars in the _styles.xml_:

{% highlight java %}
<style>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
</style>
{% endhighlight %}

Next, we'll determine if the translucent system bars are enabled, and if so, apply the proper padding - the height of the status bar on top, the height of the navigation bar on bottom.

{% highlight java %}
//...
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT){
    listView.setPadding(0, getStatusBarHeight(this), 0, getNavigationBarHeight(this));
    listView.setClipToPadding(false);            
}
//...
{% endhighlight %}

Calculating the paddings:

{% highlight java %}
private int getStatusBarHeight(final Context context) {
    final int resourceId = context.getResources().getIdentifier("status_bar_height", "dimen", "android");
    if (resourceId > 0) {
        return context.getResources().getDimensionPixelSize(resourceId);
    }
    return 0;
}

private int getNavigationBarHeight(final Context context) {
    final int id = context.getResources().getIdentifier("config_enableTranslucentDecor", "bool", "android");
    if (id != 0 && context.getResources().getBoolean(id)) {
        final int resourceId = context.getResources().getIdentifier("navigation_bar_height", "dimen", "android");
        if (resourceId > 0) {
            return context.getResources().getDimensionPixelSize(resourceId);
        }
    }
    return 0;
}
{% endhighlight %}

The result:

<p align="center">
    <img src="http://localhost:4000/img/post/translucent_listview.gif"/>
</p>

That's all, translucent system bars and ListViews can work well together with this fairly simple solution. The code runs on APLI LEVEL < 19 as well. We use this pattern is well-tested, we use it in [Ready](https://play.google.com/store/apps/details?id=com.ready.android) wherever applicable.