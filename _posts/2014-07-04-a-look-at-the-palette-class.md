---
layout: post
title:  "A look at the Palette class"
date:   2014-7-4 15:06:00
categories: Android development
tags: Palette Lollipop
redirect_from: "/2014/07/04/a-look-at-the-palette-class/"
description: A look on the capabilities and performance of the Palette class from the support library.
---
Back in February, Chris Banes' two excellent blog posts (color matching [part one](http://chris.banes.me/2014/02/18/colour-matching/) and [part two](https://chris.banes.me/2014/03/10/colour-matching-pt-2/)) demonstrated how can we extract the dominant colors from a bitmap. This can be a powerful asset to the user experience of your app: think about dynamically colored UI elements, or a filter based on the dominant color of your bitmap. The Palette class from the new Support Library announced at Google I/O does the same. 
<!-- more -->

Currently there is no official documentation, you can refer to [this article](http://chris.banes.me/2014/07/04/palette-preview/) for some info on the features. The class itself can extract the dominant colors from a bitmap, including these prominent colors:

*   vibrant
*   vibrant dark
*   vibrant light
*   muted
*   muted dark
*   muted light

To understand what this means, here are some examples. The first column contains the colors from a _generate()_ call with a number of six (note 1: sorted by the default order; note 2: I encountered an issue with one of the pictures where a param of 6 only resulted in 5 colors, so I generated 7 colors and used the first six results). The second column contains the prominent colors. For the first one I used ['The Starry Night'](http://www.wikiart.org/en/vincent-van-gogh/the-starry-night-1889) by Vincent van Gogh:  

<img src="http://andraskindler.com/img/post/palette_starry_night.jpg">

A picture of the [Carina Nebula](http://hqwide.com/wallpapers/l/1280x800/45/outer_space_nebulae_digital_art_artwork_carina_nebula_1280x800_44297.jpg):  

<img src="http://andraskindler.com/img/post/palette_carina.jpg">

[Nighthawks](http://upload.wikimedia.org/wikipedia/commons/a/a8/Nighthawks_by_Edward_Hopper_1942.jpg) by Edward Hopper:  
	
<img src="http://andraskindler.com/img/post/palette_nighthawks_at_the_diner.jpg">

## Including Palette from the Support Library

The first step is including the library in your project. You can do this with the following Gradle dependency:

{% highlight java %}compile 'com.android.support:palette-v7:+'{% endhighlight %}

## Palette in action

A Palette instance can be generated with static object methods, either with the _generate()_ or the _generateAsync()_ function. The difference between them is that with _generate()_ the developer has to take care of the wrapping it into an async thread, since this is a potentially long running operation which shouldn't run on the UI thread. On the other side, the _generateAsync()_ takes a _PaletteAsyncListener_ instance as parameter, which has an _onGenerated()_ callback, called when the palette generation is completed. The following example uses the latter.

{% highlight java %}
Palette.generateAsync(BitmapFactory.decodeResource(getResources(), R.drawable.test),
  new Palette.PaletteAsyncListener() {
    @Override public void onGenerated(Palette palette) {
      // do something with the colors
    }
});
{% endhighlight %}

Note that if you want to extract all six prominent colors, you'll have to have a parameter larger than or equal to six. You can also specify how many colors should be generated - by default, this number is 15. The resulting Palette instance contains PaletteItems, which represent colors, with their RGB and HSL representation and population. The _getPalette()_ method returns the whole list, however this is not ordered by population. You can also retrieve the prominent colors, discussed above. 

While bitmap operations usually take some time, the generate method appeared almost instantaneous, so I wanted to support this with some measurements. The following was made on a Nexus 5 running the L dev preview, with the help of the [TimingLogger](http://developer.android.com/reference/android/util/TimingLogger.html) class, with closing the app after each run. Each result represents the average of 5 measurements. Unsurprisingly, the time is proportional to the resolution and the number of required colors.

<p align="center"> 
<table>
<tbody>
<tr>
<th>picture</th>
<th>resolution</th>
<th>1 color</th>
<th>5 colors</th>
<th>15 colors</th>
</tr>
<tr>
<td>Starry Night</td>
<td>2560 × 1600</td>
<td>28.6 ms</td>
<td>31.6 ms</td>
<td>44 ms</td>
</tr>
<tr>
<td>Carina nebula</td>
<td>800 × 1280</td>
<td>19 ms</td>
<td>23.2 ms</td>
<td>26.2 ms</td>
</tr>
<tr>
<td>Nighthawks</td>
<td>6000 × 3274</td>
<td>25 ms</td>
<td>31.4 ms</td>
<td>33.4 ms</td>
</tr>
</tbody>
</table></p>
In conclusion, the Palette is an awesome tool to improve UX with dynamic, content-based colors. The performance impact is small (at least on a relatively high-end device), and the benefits look great, especially with the new theme and animation possibilities in the L dev preview.