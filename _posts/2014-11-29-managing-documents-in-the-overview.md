---
layout: post
title:  "Multitasking on steroids: managing documents in the Overview"
date:   2014-11-29 19:22:27
categories: Android development
image: 2014-11-29-concurrent_overviews.png
tags: Overview, Multitasking, Lollipop
redirect_from: "/2014/11/29/managing-documents-in-the-overview/"
description: A tutorial about the new multitasking features of Android 5.0. This guide will show you how to customize your overview entry.
---
The multitasking screen has undergone some huge changes in the 5.0 release. It also has a new name: _overview_, with the items called _documents_. Before Lollipop, this screen could only display one item per app, but now it is possible to open multiple tasks per app, each of them appearing as a separate document in the overview. Moreover, the appearance of each document can be tailored to the app - this post will show you how. 
<!-- more -->

**Opening multiple Documents and Tasks** Starting an activity with a new document can be specified in the launching intent, by adding the _FLAG_ACTIVITY_NEW_DOCUMENT_ flag. Also, by setting the _FLAG_ACTIVITY_MULTIPLE_TASK_ flag the system creates a new task as well, with the Activity as root. Check out the snippet below: 
{% highlight java %}
final Intent intent = new Intent(this, ThirdActivity.class)
    .setFlags(Intent.FLAG_ACTIVITY_NEW_DOCUMENT)
    .setFlags(Intent.FLAG_ACTIVITY_MULTIPLE_TASK); 
startActivity(intent);
{% endhighlight %}

This can also be done in the manifest, by setting the _documentLaunchMode_ attribute of the activity to either _intoExisting_ (opens a new document, but reuses the current task), or _always_ (opens a new document and starts a new task). The number of documents in the overview can be limited manually in the manifest with the _maxRecents_ attribute. However, this cannot exceed 50, or 25 for devices with low RAM. Also, this number is for the whole application, not for individual activities. 

{% highlight xml %}
<activity android:maxRecents="8" .../>
{% endhighlight %}

**Customizing the appearance of Documents** 

The looks of each card can be specified programmatically, which is a good way of expressing the branding of the app. The title, the icon and the color of the header background can be defining an _ActivityManager.TaskDescription_; this should be done in the _onCreate()_ function in the Activity. 

{% highlight java %}
protected void onCreate(Bundle savedInstanceState) { 
    //... 
    final ActivityManager.TaskDescription taskDescription = new ActivityManager.TaskDescription("1st Activity", BitmapFactory.decodeResource(getResources(), R.drawable.ic_icon), Color.GREEN); setTaskDescription(taskDescription); 
    //... 
}
{% endhighlight %}

This is available for sites as well - while the document belonging to Chrome has a grey header by default, webpages can override this setting to have a color of their own. The color setting also effects the statusbar. A few sites have already adopted this feature, check out for example [AndroidPolice](http://androidpolice.com) in Chrome. Just add the following tag to the _head_ of the page (_content_ specifies the color). 

{% highlight html %}
<meta name="theme-color" content="#ababab"/>
{% endhighlight %}

**Persistence across reboots** 

The documents in the overview can be reopened accross reboots. This can be specified by setting the _persistableMode_ attribute of the _activity_ tag. Three options are available (more info [here](https://developer.android.com/reference/android/R.attr.html#persistableMode)): 

  * _persistRootOnly_ \- the default setting. The task persists after a restart, the launch intent is used.
  * _persistNever_ \- the task will not persist after a reboot.
  * _persistAcrossReboots_ \- the task and the activity persists after a reboot.

**Managing the tasks of an application** 
The [AppTask](https://developer.android.com/reference/android/app/ActivityManager.AppTask.html) class was also introduced in API level 21. This class can be used to list and manage the current tasks of the application. 

{% highlight java %}
ActivityManager activityManager = (ActivityManager) getSystemService(ACTIVITY_SERVICE); 
for (ActivityManager.AppTask appTask : activityManager.getAppTasks()) { 
    // ... 
}
{% endhighlight %}

The new multitasking is more than just a gimmick. A proper setup can result in a better user experience with multiple documents, and modifying the appearance of the header can mean a more recognisable brand.