---
layout: post
title:  "Moving to Jekyll + GitHub Pages from WordPress"
date:   2014-12-28 15:42:00
categories: Github Pages + Jekyll
tags: GitHub Pages Jekyll SEO Wordpress
description: A guide on moving from WordPress to GitHub Pages and Jekyll, setting up a blog without bootstrapping tools, with some tips on SEO, related posts and redirects.
---
I finally made the transition from WordPress to GitHub Pages, resulting in a minimal, hand-crafted blog, without using bootstrapping tools. This post contains what I went with, sticking to the bare essentials, plus some tips on SEO, related posts and redirects.
<!-- more -->

### Getting started

There are a lot of good bootstrapping tools out there, [Jekyll Now](https://github.com/barryclark/jekyll-now), [Poole](http://getpoole.com/), [Jekyll Bootstrap](http://jekyllbootstrap.com/), and the likes will get you up and running within minutes. I wanted to go with the vanilla option - this gave me a greater understanding of the platform, thus making me able to make changes quicker, and giving a finer control above the site. Also, building a minimal site requires a small effort, I was able to finish in a few days, what I wanted working only a couple of hours on it every day.

I read through some guides, and ended up following Mike Greiling's excellent series of posts (part [one](http://pixelcog.com/blog/2013/jekyll-from-scratch-introduction/), [two](http://pixelcog.com/blog/2013/jekyll-from-scratch-core-architecture) and [three](http://pixelcog.com/blog/2013/jekyll-from-scratch-extending-jekyll/)), which answered nearly all of my questions. The setup of the site was based on these articles, also serving as guidelines for further customizations.

As far as the *extra features* go, I tried to keep everything to a minimum, even dropped the *about* page, with the contact info in the footer. Also, GitHub Pages runs Jekyll in *safe mode*, meaning plugin support is off (except for the *Jekyll Redirect Plugin*). No worries though, everything can be achieved with a few lines of code, with a tutorial or a manual available for each and every step.

### The basics

Here's the list (more like a collection of links) of what I did besides the initial setup and playing around with the layout, and their respective sources.

* [Drafts](http://jekyllrb.com/docs/drafts/). Also, I made a symlink to a *_drafts* folder in my Google Drive (got the idea from Mike Greiling), so drafts are backed up and can be edited on all my devices.
* [Pagination](http://jekyllrb.com/docs/pagination/). I chose to only display *prev* and *next* buttons, no numbers.
* [Custom domain](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/).
* Social widgets at the end of each post. I ended up using [Twitter](https://about.twitter.com/resources/buttons#tweet), [Google Plus](https://developers.google.com/+/web/+1button/) and [Reddit](http://www.reddit.com/buttons/).
* Comments. Because of the heavy focus on Android, I'm rolling with [Google Plus comments](http://googlesystem.blogspot.com/2013/04/add-google-comments-to-any-web-page.html).
* Site stats. This is a no-brainer, [Google Analytics](www.google.com/analytics/).
* [404](https://help.github.com/articles/custom-404-pages/).
* Sitemap.xml. Generate it using [the built-in method](https://help.github.com/articles/sitemaps-for-github-pages/).
* Header color from Google's [Material design palette](http://www.google.com/design/spec/style/color.html)
* Importing posts from WordPress. Since I don't have many posts, and needed to update all of them, I did this manually. Used [this site](http://domchristie.github.io/to-markdown/) for Markdown conversion.
* [Markdown cheat sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
* [Liquid basics](https://github.com/Shopify/liquid/wiki/Liquid-for-Programmers) + [filters](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)

### SEO

I did some basic SEO on the site, by generating the proper `meta`-tags and related posts for each page. Also, each post automatically fills out the `alt` tag of the banner image (if applicable) with the title of the page.

#### Meta tags
First, I added the `tags`, `categories`, `name` and  `description` variables, then generated the tags in *head.html* with the following. Be sure to fill out a proper description, as it's the meta tag that matters.

{% highlight html %}
{% raw %}
<meta name="description" content="{% if page.description %}{{ page.description | strip_html | strip_newlines }}{% else %}{{ site.description | strip_html  | strip_newlines }}{% endif %}">
<meta name="keywords" content="{{page.tags | join: ' '}}, {{page.categories | join: ' ' }}"/>
<meta name="author" content="{{ site.name }}">
{% endraw %}
{% endhighlight %}

For example, this post has the following values:

{% highlight html %}
---
categories: jekyll
tags: Jekyll, GitHub Pages
description: A guide on moving from WordPress to GitHub Pages and Jekyll, setting up a blog without bootstrapping tools.
---
{% endhighlight %}

And the generated tags:
{% highlight html %}
<meta name="description" content="A guide on moving from WordPress to GitHub Pages and Jekyll, setting up a blog without bootstrapping tools, with some tips on SEO and redirects.">
{% if page.tags || page.category %}<meta name="keywords" content="{{page.tags | join: ' '}}, {{page.categories | join: ' ' }}"/>{% endif %}
<meta name="author" content="Andras Kindler">
{% endhighlight %}

#### Related posts

I got the idea from [this SO-thread](http://stackoverflow.com/questions/25348389/jekyll-and-liquid-show-related-posts-by-amount-of-equal-tags-2), and I modified David Jacquel's solution a bit. My take only gives the newest related link with the most matched tags.
{% highlight html %}
{% raw %}
{% assign bestMatch = 0 %}
{% for post in site.posts %}
	{% if page.id != post.id %}
    	{% assign sameTagCount = 0 %}

		{% for tag in post.tags %}
        	{% if page.tags contains tag %}
			
          		{% assign sameTagCount = sameTagCount | plus: 1 %}
        	
        	{% endif %}
    	{% endfor %}

    	{% if sameTagCount > bestMatch %}
    		{% assign bestMatch = sameTagCount %}
    		{% assign bestMatchPost = post %}
    	{% endif %}
	{% endif %}
{% endfor %}

{% if bestMatch > 0 %}
	<div class="post-related">
		Related: <a href="{{ bestMatchPost.url }}">{{ bestMatchPost.title }}</a>
	</div>
{% endif %}
{% endraw %}
{% endhighlight %}

### URL redirects

Page redirects. Moving from WordPress I purchased the **Site Redirect** update, which works exactly as advertised. However, the old link structure (/YYYY/MM/DD/...) conflicted with the new one (/blog/YYYY/...); this is where the [Jekyll Redirect Plugin](https://help.github.com/articles/redirects-on-github-pages/) comes handy. 

Add these lines to the *_config.yml* file:
{% highlight html %}
gems:
  - jekyll-redirect-from
{% endhighlight%}

Then add the old links to each page:
{% highlight html %}
redirect_from: "/your/old/url/here"
{% endhighlight%}

### Outro

Like all sites hosted on GitHub Pages, the source is completely available on [GitHub](https://github.com/andraskindler/andraskindler.github.io). The code itself is a bit messy (last time I did some web development was a long time ago), this will change in the future.