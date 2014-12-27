---
layout: post
title:  "Using RxJava with Android"
date:   2013-10-28 10:22:27
categories: Android development
tags: RxJava, Retrofit 
redirect_from: "/2013/10/28/using-rxjava-in-android/"
description: RxJava is a powerful library, which helps helps build fault-tolerant, reactive applications, also available on Android.
---
This post is the first in a series about RxJava and Android. This tutorial will show you how to build a REST API client in Android with RxJava observables (and based on Square's Retrofit).
<!-- more -->

If you're into Android (and Java) development, there is a good chance you've already heard about RxJava, which is a Java implementation of Reactive Extensions developed by Netflix. Reactive Extensions is a _library to compose asynchronous and event-based programs using observable collections and LINQ-style query operators_, quoted from the corresponding [MSDN site](http://msdn.microsoft.com/en-us/data/gg577609.aspx). Netflix made the library available for the public on Github, supporting Java 6 or newer, making it available to use with Android apps as well.

Let's start the example with adding the required libraries to your project. If you're using gradle, just add the following dependencies to the build script:

{% highlight groovy %}
    compile 'com.netflix.rxjava:rxjava-android:0.17.5'
    compile 'com.squareup.retrofit:retrofit:1.5.0'
{% endhighlight %}

In this post I use the [OpenWeatherMap API](http://api.openweathermap.org/), which is a free weather data API. It is quite easy to configure, just supply your location (the city name or the geocoordinates) via a query param, see [this example](http://api.openweathermap.org/data/2.5/weather?q=Budapest,hu). By default it serialises into JSON (but you can switch to XML and HTML as well) accurarcy and the temperature units can also be changed. See more [here](http://api.openweathermap.org/API).

Usually implementing an api call requires the following steps (each with it's own amount of boilerplate code):

1.  creating the model classes (and supplying annotations if necessary)
2.  implementing the network layer for request / response management, with error handling
3.  writing the code that performs the call in a background thread (usually in some form of an AsyncTask), with a callback function capable of presenting the response on the UI thread

## Creating the model classes

The first point can be (partially) automated using JSON-POJO generators like [jsonschema2pojo](http://www.jsonschema2pojo.org/). The model class for the OpenWeather API can be copied from this container.

{% highlight java %}
public class WeatherData {

    public Coordinates coord;
    public Local sys;
    public List&lt;Weather&gt; weathers;
    public String base;
    public Main main;
    public Wind wind;
    public Rain rain;
    public Cloud clouds;
    public long id;
    public long dt;
    public String name;
    public int cod;

    public static class Coordinates {
        public double lat;
        public double lon;
    }

    public static class Local {
        public String country;
        public long sunrise;
        public long sunset;
    }

    public static class Weather {
        public int id;
        public String main;
        public String description;
        public String icon;
    }

    public static class Main {
        public double temp;
        public double pressure;
        public double humidity;
        public double temp_min;
        public double temp_max;
        public double sea_level;
        public double grnd_level;
    }

    public static class Wind {
        public double speed;
        public double deg;
    }

    public static class Rain {
        public int threehourforecast;
    }

    public static class Cloud {
        public int all;
    }

}
{% endhighlight %}

## Networking with Retrofit

The second point (implementing the network layer) requires a lot of boilerplate code, however Square's [Retrofit](http://square.github.io/retrofit/) library solves these tasks with only a few lines. You only have to create an interface (with annotated parameters describing the request), then use the RestAdapter.Builder to build the client. Retrofit also takes care of JSON serialisation and deserialisation.

{% highlight java %}
 private interface ApiManagerService {
    @GET("/weather")
    WeatherData getWeather(@Query("q") String place, @Query("units") String units);
}
{% endhighlight %}

The HTTP annotation consists of the request method (in this example this is GET, but you can also use POST, PUT, DELETE and HEAD with Retrofit) and the relative url (the base url is supplied via the RestAdapter.Builder). The _@Query_ annotation concatenates queryparams into the request; in this call we have a place (the name of the location) and the measurement unit.

Let's take a look at an actual API call (of course this must be performed in a non-UI thread). The code is pretty self-explanatory:

{% highlight java %}
//...
final RestAdapter restAdapter = new RestAdapter.Builder()
    .setServer("http://api.openweathermap.org/data/2.5")
    .build();

final ApiManagerService apiManager = restAdapter.create(ApiManagerService.class);
final WeatherData weatherData = apiManager.getWeather("Budapest,hu", "metric");
//...
{% endhighlight %}

So that's it, you just created a functioning API call with only a few lines code, congrats! Again, Retrofit is much more powerful than this basic sample; you can read more [here](http://square.github.io/retrofit/).

## Going reactive with RxJava

Now let's jump into the third step: the RxJava-part! This example will show you how to use it for managing async API calls, but this is only one of many possible use-cases, RxJava is capable of much more. Quoted from Netflix's Github Wiki:
<i>RxJava is a Java VM implementation of Reactive Extensions: a library for composing asynchronous and event-based programs by using observable sequences.</i>
 
<i>It extends the observer pattern to support sequences of data/events and adds operators that allow you to compose sequences together declaratively while abstracting away concerns about things like low-level threading, synchronization, thread-safety, concurrent data structures, and non-blocking I/O.</i>
 
It supports Java 5 or higher and JVM-based languages such as Groovy, Clojure, and Scala. In this post I assume that you have a little knowledge about RxJava. If that's not true, I strongly recommend reading [these](http://www.reactivemanifesto.org/) [two](http://techblog.netflix.com/2013/02/rxjava-netflix-api.html) articles and the first few pages of the [Netflix Github wiki page](https://github.com/Netflix/RxJava/wiki) before continuing.

In the last part of this example you'll extend the API manager with the ability to emit observables, then use these to perform n simultaneous calls to the same url with different queryparams.

First, replace the interface you created above with this class:

{% highlight java %}
public class ApiManager {

    private interface ApiManagerService {
        @GET("/weather")
        WeatherData getWeather(@Query("q") String place, @Query("units") String units);
    }

    private static final RestAdapter restAdapter = new RestAdapter.Builder()
        .setServer("http://api.openweathermap.org/data/2.5")
        .build();
    private static final ApiManagerService apiManager = restAdapter.create(ApiManagerService.class);

    public static Observable&lt;WeatherData&gt; getWeatherData(final String city) {
        return Observable.create(new Observable.OnSubscribe&lt;WeatherData&gt;() {
            @Override
            public void call(Subscriber&lt;? super WeatherData&gt; subscriber) {
                try {
                    subscriber.onNext(apiManager.getWeather(city, "metric"));
                    subscriber.onCompleted();
                } catch (Exception e) {
                    subscriber.onError(e);
                }
            }
        }).subscribeOn(Schedulers.io());
    }

}
{% endhighlight %}

Let's take a look at the _getWeatherData()_ method! It returns an Observable, created by an _Observable.create()_ call where you'll have to implement the [Observable.OnSubscribe](http://netflix.github.io/RxJava/javadoc/rx/Observable.OnSubscribe.html) interface. After subscribed to, the Observable begins work, emitting the results as params in the _onNext()_ function. Since we want these API calls to run parallel, you only do one call in an observable, then finishing with _onComplete()_. The _call()_ method is also important, this decides what kind of thread to use. Call it with _Schedulers.threadPoolForIO()_ for IO- and network-bound work.

The last step is to perform the API calls. The following code performs parallel async calls to the same base url with different query parameters.

{% highlight java %}
Observable.from(cities)
            .flatMap(new Func1&lt;String, Observable&lt;WeatherData&gt;&gt;() {
                @Override
                public Observable&lt;WeatherData&gt; call(String s) {
                    return ApiManager.getWeatherData(s);
                }
            })
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(new Action1&lt;WeatherData&gt;() {
                @Override
                public void call(WeatherData weatherData) {
                    // do your work
                }
            });
{% endhighlight %}

An _Observable.from()_ call on the array containing the city names creates an observable which will emit the Strings in the array on different threads. Then a _flatMap()_ call will transform the emitted Strings into observables; this is where the _ApiManager.getWeatherData()_ call occurs.

Again, subscribe on the I/O threadpool, but in Android, if you want to display the results in the UI, you'll have to post it on the UI thread, because _only the original thread that created a view hierarchy can touch its views_, as per the popular exception. This can easily be achieved using the _observeOn()_ method with _AndroidSchedulers.mainThread()_. The _subscribe()_ call triggers the observable and it's up to the user to define what to do with the result.

This example shows the versatility and robustness of RxJava. Without Rx, you'd need to iterate through each address, creating a new thread, making the API call and posting back to the UI thread on an async callback. Rx allows you to do this with only a few lines of code, with powerful functions to create, combine, filter and transform observables.

RxJava is a great way to utilize concurrency in Android apps, and although it takes some time getting used to, right now I can't think of a better way of handling async calls. The _reactive extensions_ library is well thought-out, we've been using the RxJava implementation in Android apps for a few weeks now (our [startup](http://getinch.com/)'s async tasks are completely based on it), and the more you dive into it, the more it will amaze you.

Want more? Check out the follow-up posts for [error handling options](http://andraskindler.com/2013/rxjava-and-android-error-handling/) and [Subjects](http://andraskindler.com/2013/rxjava-and-android-working-with-subjects/).