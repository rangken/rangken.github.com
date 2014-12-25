---
layout: post
title:  "The new JobScheduler API"
date:   2014-12-24 14:35:20
categories: android
tags: JobScheduler
---
As a part of Lollipop, Google introduced Project Volta, which emphasises on improving battery life. It contains a bunch of new APIs as well, focusing on measuring and reducing power consumption. One of these is the new JobScheduler API, which, as the name states, bundles tasks and defers them until certain conditions are met.
<!-- more -->
This also means that, unless a deadline is seat, that the developer has no control over when the device will start the job, hence this depends on things like plugging in the charger or connecting to a WiFi network. Good examples include time-insensitive and power-hungry tasks, like creating a backup of photos, syncing a music playlist or caching a movie.

Use the JobInfo class to specify the parameters of a task, constructed via the JobInfo.Builder. The following criteria can be set:

*   plugged into an outlet
*   connected to a certain kind of network
*   being in [idle mode](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html#setRequiresDeviceIdle(boolean))

The JobInfo class contains some handy parameters besides setting the conditions. A backoff-policy can be provided with the  It is also possible to create recurring tasks, which run within the given interval, not more than once per period. Also, jobs can be persisted between restarts.

Also, a maximum delay can be provided, meaning the job has to be executed until the given timeframe even if the conditions aren't met. Similarly, a minimum latency can be issued, however these are only available for non-periodic tasks.

TODO The following example

Also keep in mind that the JobScheduler is available since API level 21 only - there's no compatibility version. However, an app can subscribe to the _ACTION_POWER_CONNECTED_ and _ACTION_BATTERY_CHANGED_ broadcasts, so with some extra coding, it is possible to schedule jobs to start when the device is plugged in.

And again, this gist contains the sample code of this tutorial.

https://developer.android.com/about/versions/android-5.0.html
https://developer.android.com/reference/android/app/job/JobScheduler.html
https://developer.android.com/reference/android/app/job/JobInfo.html
https://developer.android.com/reference/android/app/job/JobInfo.Builder.html
https://developer.android.com/samples/JobScheduler/src/com.example.android.jobscheduler/service/TestJobService.html