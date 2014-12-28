---
layout: post
title:  "QuickScroll: a customizable, always visible scrollbar for ListView and ExpandableListView"
date:   2013-8-28 09:30:00
categories: Android development
tags: QuickScroll ListView ExpandableListView
redirect_from: "/2013/08/28/quickscroll-a-customizable-always-visible-scrollbar-for-listview-and-expandablelistview/"
description: Bringing extended scrolling features to Android's native ListView and ExpandableListView.
---
Recently I had to create an app with a ListView and an always visible, customized scrollbar at the right side. The look and feel had to be similar to the native fastscroll of the ListView, with a handle and an indicator displaying the initials of the current content. So I created a View which is easy to reuse, and now I decided to make it available for everyone on Github.

<!-- more -->

Check out the demo application, available in the [Play Store](https://play.google.com/store/apps/details?id=com.andraskindler.quickscrollsample).

<p  align="center">
    <img src="http://andraskindler.com/img/post/quickscroll_3.png" style="width: 320px; height: auto;"/>
</p>

QuickScroll is a library aimed at creating similar scrolling experiences to the native Contacts app's People view. It has the same scrolling capabilities as the ListView's native fastscroll, but comes with a much a much more customizable design, two indicator styles and an always visible scrollbar. The two most common modes are popup and indicator. Works seamlessly with ListView and ExpandableListView.

Usage is pretty easy:

**1.** Include the widget in your layout. Currently, you can only put it to the right side of the ListView, this can be done via a RelativeLayout, a LinearLayout or any other positioning trick of your choice. The default width is 30 dp, but if you really want to change it, you can do it via an object method.

{% highlight java %}
<com.andraskindler.quickscroll.QuickScroll
android:id="@+id/quickscroll"
android:layout_width="wrap_content"
android:layout_height="match_parent"/>
{% endhighlight %}

**2.** Implement the Scrollable interface in your BaseExpandableListAdapter or BaseAdapter subclass. You must override the methods below.
The first function returns the corresponding String to display at any given position:

{% highlight java %}
@Override
public String getIndicatorForPosition(int childposition, int groupposition) {
    return null;
}
{% endhighlight %}

The second function is responsible for is for implementing scroll behaviour. This can be used to perform special tasks, e.g. if you want to snap to the first item starting with a letter in an alphabetically ordered list or jump between groups in an ExpandableListView. If you want the normal approach, simply return childposition.

{% highlight java %}
@Override
public int getScrollPosition(int childposition, int groupposition) {
    return 0;
}
{% endhighlight %}

**3.** Initialize the QuickScroll view:
{% highlight java %}
final QuickScroll quickscroll = (QuickScroll) layout.findViewById(R.id.quickscroll);
quickscroll.init(QuickScroll.TYPE_INDICATOR, listview, adapter, QuickScroll.STYLE_HOLO);
{% endhighlight %}

The first parameter assigns the type, the second and third binds the ListView (or ExpandableListView) and its adapter. The last one defines the style, currently there are two available:

*   STYLE_HOLO creates a scrollbar fitting into stock holo design.
*   STYLE_NONE creates a transparent scrollbar, so you can create a fully customised underlying layout.

Both styles also come with a _WITH_HANDLE suffix, creating a holo-themed handlebar on the scrollbar.

**4. (Optional)** Do some customizing: change the colors, sizes and font styles. This can be done via object methods (check out the [sample project](https://github.com/andraskindler/quickscroll)).

Feel free to fork on [github](https://github.com/andraskindler/quickscroll), every comment and contribution is welcome. Keep in mind that this is a beta version, so there might be bugs and glitches; don't refrain from pointing them out.

<p  align="center">
    <img src="http://andraskindler.com/img/post/quickscroll_4.png" style="width: 320px; height: auto;" />
</p>