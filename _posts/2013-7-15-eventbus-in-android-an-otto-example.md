---
layout: post
title:  "Eventbus in Android: an Otto example"
date:   2013-07-15 09:00:00
categories: android
tags: Otto
redirect_from: "/2013/07/15/eventbus-in-android-an-otto-example/"
---
Loose coupling is a great invention: it allows components to have less dependencies, resulting in a far more reusable code. A common way to achieve this is via using interfaces, with a downside of an excessive amount of boilerplate code. Instead of using interfaces, loose coupling can also be achieved with the publisher-subcsriber model. One of Square's open source library can be very helpful in this case, it requires far less extra code than using interfaces. This post will show the basic usage of Otto.
<!-- more -->

The library is forked from Google's [Guava](https://code.google.com/p/guava-libraries/), which has an EventBus class; Square's take on the EventBus is simply called Bus, and is optimized for Android. Quoted from the [official documentation](http://square.github.io/otto/):

<i>Unlike the Guava event bus, Otto will not traverse the class hierarchy and add methods from base classes or interfaces that are annotated. This is an explicit design decision to improve performance of the library as well as keep your code simple and unambiguous.</i>

This model is also a great way to communicate between an activity and a fragment and between fragments. This way the fragments don't need to know about each other, making the app architecture much more generic and decoupled.

Usage is pretty easy, just create a Bus instance, register it, subscribe the desired classes and start publishing. Bus is intended to use as a singleton, and I recommend going this route; a good idea is to declare it in the Application subclass. While it is possible to instantiate the Bus with an empty constructor, I highly recommend specifying the thread enforcement policy. The following line create a Bus, which can communicate on any thread instead of forcing it to use the main thread.

{% highlight java %}
Bus eventbus = new Bus(ThreadEnforcer.ANY);
{% endhighlight %}

If you want to receive events, you have to register your class(es) with the _register()_ method. If a class is just sending events, not receiving them, it is not necessary to register it with the Bus. To receive an event, create a public method with the _@Subscribe_ annotation, for example:

{% highlight java %}
@Subscribe 
public void onSampleEvent(SampleEvent event) {
    // do your work
}
{% endhighlight %}

It is possible to send and receive events with a String or an Integer parameter, I recommend creating your own classes even if the only variable is a primitive. This way your code will be much cleaner, and keep in mind that events are identified by their parameters, so for example if you want to have two events with a String parameter and two different subscribers, both will receive the event if a producer fires. 

Producing events is also very straightforward, just call the _post()_ method of the Bus with the proper parameter. Sometimes it is required that each new subscriber receives an event, which is a common case if the classes subscribe dynamically at runtime. This is also possible, just supply the publisher method with the _@Produce_ annotation, this way subscribing classes receive an event instantly after subscription. It is required to register the producer with the Bus if you're using the _@Produce_ annotation.

Here's a basic example. Let's start with the event class; this is what will be passed through the Bus:

{% highlight java %}
public class TestEvent {

    public String message;

    public TestEvent(String message) {
        this.message = message;
    }
}
{% endhighlight %}

The Bus instance will be accessible from an Application subclass. Important: don't forget to declare this in the AndroidManifest.xml: put a _name_ attribute in the application tag with the class' name.

{% highlight java %}
public class BaseApplication extends Application{

    private static Bus mEventBus;

    public static Bus getEventBus() {
        return mEventBus;
    }

    @Override
    public void onCreate() {
        super.onCreate();

        mEventBus = new Bus(ThreadEnforcer.ANY);
    }
}
{% endhighlight %}

Create an activity which will catch the event with a _@Subscribe_-annotated method:

{% highlight java %}
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        final View root = getLayoutInflater().inflate(R.layout.activity_main, null);
        setContentView(root);
        BaseApplication.getEventBus().register(this);
    }

    @Subscribe
    public void onTestEvent(TestEvent event) {
        Toast.makeText(this, event.message, Toast.LENGTH_SHORT).show();
    }

}
{% endhighlight %}

And that's all, now all you have to do is call the following from anywhere, and a Toast will appear with the given message.

{% highlight java %}
BaseApplication.getEventBus().post(new TestEvent("test message"));
{% endhighlight %}

So that's it, Otto provides an easy-to-use solution for decoupling component communication. The source code is available on [GitHub](https://github.com/square/otto), the jar can be downloaded from the [official site](http://square.github.io/otto/), or you can reference it as a Gradle dependency:

{% highlight groovy %}
compile 'com.squareup:otto:1.3.5'
{% endhighlight %}