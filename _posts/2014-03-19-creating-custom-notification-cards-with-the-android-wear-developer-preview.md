---
layout: post
title:  "Creating custom notification cards with the Android Wear Developer Preview"
date:   2014-3-19 07:24:00
image: android_wear_developer_preview.png
categories: android
tags: Android Wear
---
When Google presented Android Wear, we started to experiment with the Developer preview, and while at this time we're limited to using only notifications (since currently, there is no _real_ Android Wear SDK), the results are surprisingly good. This example will show you how to create a horizontally swipeable, card-like notification for Android Wear.
<!-- more -->

We're working on an [app](https://play.google.com/store/apps/details?id=com.ready.android) that aggregates the most recent interactions associated with the caller and shows them as cards when the phone is ringing. While the ringer screen gives a great way to get an idea of why they're calling, it is obviously limited: if you don't pick up in 5-6 seconds, they'll hang up for sure. This is why we've been actively working on the concept of a second-screen experience since day one, mostly thinking about tablets as the secondary source of information. However, a smartwatch makes much more sense: you can see the caller-related information not only on your phone before a call, but while you're talking, and you don't have to carry an extra tablet around.

<iframe width="715" height="402" src="//www.youtube.com/embed/0xQ3y902DEQ" frameborder="0" allowfullscreen></iframe>

The Android Wear Developer preview is not available for anyone, you'll have to register to get an invite (you can do so [here](http://developer.android.com/wear/preview/signup.html)). I won't go into details on how to get started with the SDK, since [Google's official site](http://developer.android.com/wear/preview/start.html) covers the subject perfectly.

Disclaimer: Google points out that the Wear Developer Preview is intended for development and testing purposes only, not for production apps. This means it will no longer be supported after the official SDK is released, so apps using it will most likely break.

As of now, the only way to interact with Wear devices is via notifications. All notifications are mirrored to the emulator (of course, they appear in a transformed way), but it is possible to create enhanced Notifications with the [WearableNotifications.Builder](http://developer.android.com/reference/android/preview/support/wearable/notifications/WearableNotifications.Builder.html) class. This is basically a wrapper around the [NotificationCompat.Builder](http://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html), allowing the developer to extend the notifications with Wear-exclusive capabilities, like receiving voice input, hiding the app icon, making them stackable, adding multiple actions with different icons and spanning a single notification through multiple pages.

This example will demonstrate the latter. Every page represents an event related to the caller (an email, a message, a meeting, etc.), encapsulated in the ultra-primitive _Card_ class. The first step is creating a _parent notification_, nothing new, just some standard calls on the NotificationCompat.Builder: setting a small and a large image (the latter represents the caller), a title and a description (and a PendingIntent, which is deliberately left out in this example).

{% highlight java %}
final NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(WearableActivity.this)
    setSmallIcon(R.drawable.inch_logo)
    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.caller_picture))
    .setContentTitle("Incoming call")
    .setContentText("Name of the caller");
{% endhighlight %}

The content cards are built while iterating through the list of Card instances. A [NotificationCompat.BigTextStyle](http://developer.android.com/reference/android/support/v4/app/NotificationCompat.BigTextStyle.html) is created for each Card, with the title and description set properly, each of these added to a list of Notifications.

{% highlight java %}
for (Card card : cards) {
    final NotificationCompat.BigTextStyle bigTextStyle = new NotificationCompat.BigTextStyle();
    bigTextStyle.setBigContentTitle(fakeCard.title)
        .bigText(fakeCard.description);

    notifications.add(new NotificationCompat.Builder(WearableActivity.this)
        .setStyle(bigTextStyle)
        .build());
}
{% endhighlight %}

Next, simply wrap the parent Notification into a WearableNotification and set the generated pages.

{% highlight java %}
Notification twoPageNotification = new WearableNotifications.Builder(notificationBuilder)
    .addPages(notifications)
    .build();
{% endhighlight %}

The final step is to show the Notification, this is done in the standard way.

{% highlight java %}
((NotificationManager) WearableActivity.this.getSystemService(Context.NOTIFICATION_SERVICE)).notify(NOTIFICATION_ID, twoPageNotification);
{% endhighlight %}

The sample (wrapped in an Activity) is available as a [Gist](https://gist.github.com/andraskindler/f227750251d61d95eb76). You can find more info and tutorials on the Android Wear Developer Preview [here](http://developer.android.com/wear/notifications/creating.html), and don't forget to check out the [design guidelines](http://developer.android.com/wear/design/index.html) and the [UI overview](http://developer.android.com/wear/design/user-interface.html).