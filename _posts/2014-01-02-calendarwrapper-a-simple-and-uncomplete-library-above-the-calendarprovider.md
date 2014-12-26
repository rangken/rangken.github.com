---
layout: post
title:  "CalendarWrapper - a simple (and uncomplete) library above the CalendarProvider"
date:   2014-1-2 10:20:00
categories: android
tags: CalendarWrapper CalendarProvider
redirect_from: "/2014/01/02/calendarwrapper-a-simple-and-uncomplete-library-above-the-calendarprovider/"
---
CalendarWrapper is a thin layer above the CalendarProvider, making able to access data via object methods, without the extra hassle of mapping cursors.
<!-- more -->

Not long ago I was looking into calendar synchronisation, because we made our application, [Ready](https://play.google.com/store/apps/details?id=com.ready.android) able to communicate with the calendar accounts on the device. The requirement was that the owner can choose which calendar(s) should be used, the events created in the app will be synced to the selected calendar, with support for attendees and reminders. 

Android provides a specific Intent for this use case (the so-called [calendar intent](http://developer.android.com/guide/topics/providers/calendar-provider.html#intents)), which starts the the default calendar's _new event_ activity, so the user can fill in every detail. In API level 14 (Ice Cream Sandwich) Google introduced the [CalendarProvider](http://developer.android.com/guide/topics/providers/calendar-provider.html), with APIs to create, read, update and delete events, reminders, calendars, attendees, etc. stored in the provider.

The CalendarProvider is one of the built-in _content providers_ in the Android platform. It manages access to a structured set of data stored in SQLite tables, usable by any third-party application. To access data stored in a content provider, one must use a ContentResolver, which provide CRUD (_create, read, update and delete_) functionality, but in some cases writing SQLite statements is not avoidable (for example operations more sophisticated than _SELECT *_). 

Queries return a [Cursor](http://developer.android.com/reference/android/database/Cursor.html), which can be traversed and mapped to objects. Similarly, inserting and updating requires objects to be mapped to a [ContentValues](http://developer.android.com/reference/android/content/ContentValues.html) for the ContentResolver to process properly. Working with content providers require a lot of boilerplate code, since every operations requires mapping objects and database rows to and from. This is why we decided to write a wrapper around the CalendarProvider to simplify basic operations. 

## Overview

The developer can work with objects, the classes take care of mapping transparently. Besides CRUD functionality, it is also possible to write SQLite statements like before to perform elaborate queries. We're calling it _CalendarWrapper_ (yeah, one of the most fancy names), the classes are available on [GitHub](https://github.com/readydev/calendarwrapper). But beware: the CalendarWrapper package is highly incomplete, and is tested only for our specific use-cases. 

The following tables (and therefore, functionality) is implemented:

*   Calendars
*   Events
*   Attendees
*   Reminders

So the whole project consists of 4 classes corresponding with the list above, each representing a table of the CalendarProvider's underlying database. 

## CalendarProvider for human beings

Each class has a static _getForQuery_ method, which is responsible for querying the corresponding table. These methods have three input parameters (besides the _ContentResolver_ instance); the first is for selection (basically a standard SQL query goes here), the second one contains the query parameters (in a String array), the third determines ordering (again, an SQL statement). Assigning null to each will result in selecting all the rows, but writing more sophisticated queries is also possible. A couple of examples:

Query all calendars ordered alphabetically by name:
{% highlight java %}
final String ordering = CalendarContract.Calendars.NAME + " ASC";
final List<Calendar> calendars = Calendar.getCalendarsForQuery(null, null, ordering, getContentResolver());
{% endhighlight %}

Query an event with a specific title:
{% highlight java %}
final String query = "(" + CalendarContract.Events.TITLE + " LIKE ?)";
final String[] args = new String[]{"test"};
final List<Event> events = Event.getEventsForQuery(query, args, null, getContentResolver());{% endhighlight %}

Query rows with a specific attendee by email address:
{% highlight java %}
final String query = "(" + CalendarContract.Attendees.ATTENDEE_EMAIL + " LIKE ?)";
final String[] args = new String[]{"hello@grabready.com"};
final List<Attendee> attendees = Attendee.getAttendeesForQuery(query, args, null, getContentResolver());
{% endhighlight %}

The _Event_, _Attendee_ and _Reminder_ classes provide CRUD functionality. The instances can be written to the database, deleted and updated (with the exception of the Attendee class, it didn't make sense to edit an attendee row) with object methods. I tried to force some constraints, for example it is not possible to persist an Attendee or Reminder object without an event attached to it.

The _Calendar_ class is much more humble, it provides a method to list the connected calendars (a _getForQuery_ function like the other three) and a method to list the calendars _writable_ (meaning having one of the required permissions) by the device's owner.

## CalendarWrapper in action

Because the CalendarProvider is basically a method of accessing data from an SQLite database (as of all ContentProviders), the operations are considered I/O. This means it doesn't matter how fast they are (or seem), it's a bad practice to perform them on the UI thread. You can use any kind of threading (AsyncTasks, Futures, etc.); the following example will show you how to do this with RxJava. It creates an event, then attaches an attendee and an email reminder 30 minutes before start.

{% highlight java %}
Observable.create(new Observable.OnSubscribeFunc<Event>() {
    @Override
    public Subscription onSubscribe(Observer<? super Event> observer) {
        final Event event = new Event();
        event.title = "test event";
        event.description = "test description";
        event.startDate = Long.toString(System.currentTimeMillis());
        event.endDate = Long.toString(System.currentTimeMillis() + 60 * 60 * 1000);
        event.create(getContentResolver());
        observer.onNext(event);
        observer.onCompleted();
        return Subscriptions.empty();
        }
    })
    .map(new Func1<Event, Event>() {
        @Override
        public Event call(Event event) {
            final Attendee attendee = new Attendee();
            attendee.email = "test@test.com";
            attendee.status = CalendarContract.Attendees.ATTENDEE_STATUS_INVITED;
            attendee.relationship = CalendarContract.Attendees.RELATIONSHIP_ATTENDEE;
            attendee.type = CalendarContract.Attendees.TYPE_OPTIONAL;
            attendee.addToEvent(getContentResolver(), event);
            final Reminder reminder = new Reminder();
            reminder.method = CalendarContract.Reminders.METHOD_EMAIL;
            reminder.minutesBefore = 30;
            reminder.addToEvent(getContentResolver(), event);
            return event;
        }
    })
    .subscribeOn(Schedulers.threadPoolForIO())
    .observeOn(Schedulers.threadPoolForIO())
    .publish().connect();
{% endhighlight %}

And don't forget to add the following permissions to the androidmanifest.xml:
{% highlight java %}
<uses-permission android:name="android.permission.READ_CALENDAR" />
<uses-permission android:name="android.permission.WRITE_CALENDAR" />
{% endhighlight %}

## Wrap-up

As you can see, CalendarWrapper is not complete, only the basic operations are available. The [Instances](http://developer.android.com/reference/android/provider/CalendarContract.Instances.html) table is currently entirely not available, data validation and constraint checking is not implemented in most cases, and the other classes are also unfinished in many ways. 

In its current form it is only good for a few use cases, nevertheless we hope it will come in useful for some. Feel free to fork the project on [GitHub](https://github.com/readydev/calendarwrapper), pull requests are welcome!