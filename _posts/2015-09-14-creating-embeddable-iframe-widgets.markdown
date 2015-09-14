---
title:  "Creating embeddable iframe widgets"
excerpt: "My trials in creating an embeddable widget."
date:   2015-09-14 04:22:00
tags: iframes web development widgets embedding
---
In general, just about any coding question you can imagine has already been answered somewhere online. (Usually at [Stack Overflow](http://stackoverflow.com/).) It may not be an identical problem to yours, but it's generally close enough to adapt the answer to your specific needs.

One area that defies these expectations is embeddable iframe widgets. It's not that guides don't exist. They do, but there tends to be one or another thing that isn't fully covered. Oftentimes, that thing is the functionality of the widget itself: e.g. why certain decisions were made in the structure of the widget and the way calls are made to the external server.

What follows isn't a guide. It's more of a rambling narrative on my thought process for designing an embeddable widget, a process I'm still going through, and why I made the functionality choices I did.

First, the widget. Along with [Victoria Kwan](https://twitter.com/victoriakwan_), early last year I created a site called [SCOTUS Search](http://scotussearch.com/). It's the only free, searchable online database of Supreme Court oral argument transcripts in existence (to my knowledge), and I've been working on it off and on for the past year and a half.

Recently I began to built out an embeddable widget. Widgets are so ubiquitous these days most people probably don't even realize what they are. Here, for example, is an embedded widget:

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">check this out How do you override Twitter&#39;s Embeddable Widget Height on HTML Site?: My HT... <a href="http://t.co/Dh8NdpGWmC">http://t.co/Dh8NdpGWmC</a> <a href="https://twitter.com/twitter">@twitter</a> <a href="https://twitter.com/hashtag/data?src=hash">#data</a> <a href="https://twitter.com/hashtag/ad?src=hash">#ad</a></p>&mdash; ralph waldo emerson (@smshay) <a href="https://twitter.com/smshay/status/631757077174616064">August 13, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

You probably know this better as a tweet. But it's actually a widget: the content inside the widget is being loaded from an external server (Twitter's, in this case) based on a small snippet of code placed on a web page (mine, for this example). Here's what that snippet looked like for the above Twitter widget:

{% highlight html %}
<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">check this out How do you override Twitter&#39;s Embeddable Widget Height on HTML Site?: My HT... <a href="http://t.co/Dh8NdpGWmC">http://t.co/Dh8NdpGWmC</a> <a href="https://twitter.com/twitter">@twitter</a> <a href="https://twitter.com/hashtag/data?src=hash">#data</a> <a href="https://twitter.com/hashtag/ad?src=hash">#ad</a></p>&mdash; ralph waldo emerson (@smshay) <a href="https://twitter.com/smshay/status/631757077174616064">August 13, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{% endhighlight %}

So yeah, SCOTUS Search has a widget too. Here's an example of the one I'm working on:

<iframe id="statementFrame3394349" style="width: 100%; max-width: 400px; min-width: 250px; height: 300px; min-height: 300px; margin-top: 10px; margin-bottom: 10px; display: block; border-width: 0px;" scrolling="yes"></iframe><script type="text/javascript">document.getElementById("statementFrame3394349").src="http://www.scotussearch.com/statementwidget/QQ51Vr92OeqpHJ7T6_PO2w/3394349?page="+encodeURIComponent(window.location.href);</script>

And here's the code snippet I placed on this page in order to display it:

{% highlight html %}
<iframe id="statementFrame3394349" style="width: 100%; max-width: 400px; min-width: 250px; height: 300px; min-height: 300px; margin-top: 10px; margin-bottom: 10px; display: block; border-width: 0px;" scrolling="yes"></iframe><script type="text/javascript">document.getElementById("statementFrame3394349").src="http://www.scotussearch.com/statementwidget/QQ51Vr92OeqpHJ7T6_PO2w/3394349?page="+encodeURIComponent(window.location.href);</script>
{% endhighlight %}

OK, so what's the point of all these widgets? After all, it's just as easy, in theory, to simply copy and paste someone's tweet or Supreme Court statement directly into the body of a page.

The benefit comes mainly from the additional functionality provided by making a connection to the external server. For example, my SCOTUS Search widget checks to see whether the site visitor is logged in. If so, it displays a little star at the bottom of the widget to allow the user to 'favorite' or 'un-favorite' that statement, an action which will be automatically saved to the user's SCOTUS Search account.

Twitter's widget was created for similar reasons. For one, its minimalist design is prettier than plain-text. But more importantly, there are multiple links to various actions within the widget itself: notably Reply, Retweet, and Favorite buttons. Incorporating some of the core functionality of your web app into embeddable widgets and thereby massively expanding your web footprint can be a huge win for any company or organization.

Let's go back to the Twitter widget. What's going on in that code snippet?

The first thing to notice is the `blockquote` element. Remember how I just said that widgets load text dynamically from an external server? Well, Twitter's doing something a little cleverer here. When you're on Twitter and click on the link to embed a tweet, the little code snippet you copy actually includes the full text of the tweet itself.

Twitter does this in case the main step -- your browser calling the Twitter server for the tweet data when the widget loads (we'll get to this in a minute) -- fails for some reason. In that case, at least you'll still have a slightly formatted version of the tweet, albeit without all the cool Twitter functionality to go along with it. Here, for example, is how that above tweet would look if the call to Twitter's server didn't go through:

![The tweet without the snazzy functionality]({{ site.url }}/assets/twitter_no_widget.png)

So if that `blockquote` line is included in the Twitter widget snippet, how does the tweet show up properly with all the Reply and Retweet functions? The answer lies in the line of code immediately following the `blockquote` one:

{% highlight html %}
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{% endhighlight %}

This line creates the first call to the Twitter server, by requesting a file named **widgets.js**. That's a JavaScript file, which then finds the `blockquote` element on the page and replaces it with a far snazzier iframe version. That way, as long as JavaScript is enabled on your browser, everything should load seamlessly; but even if not, at least you'll have the tweet text available.

Now here's an interesting twist. Try taking that Twitter widget snippet and removing the actual text of the `blockquote` element, so it looks like this:

{% highlight html %}
<blockquote class="twitter-tweet" lang="en"><a href="https://twitter.com/smshay/status/631757077174616064">August 13, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
{% endhighlight %}

Now if you save that snippet to an HTML file and then load it in a browser, the tweet still displays normally with all of its text. How did that happen?! The answer is that, once the browser is able to make a connection to Twitter's server, it doesn't even need the `blockquote` text on the page: it's going to locate that tweet in Twitter's external database and download it to the page anyway.

It does this by using the tweet's ID number, which is the long number nested inside the `blockquote` element above: 631757077174616064. The **widgets.js** script calls the Twitter server with this ID to obtain the tweet information. So even if the text of the tweet itself isn't included in the `blockquote`, the embedded widget will still load the text.

So, in order, the event flow goes something like this:

- Page loads, including the blockquote
- JavaScript file **widgets.js** loads and executes, sending the tweet ID to Twitter and receiving data (including the tweet text) in return
- Blockquote is replaced with an embedded iframe widget

In total, this snippet requires two principal round-trip calls to Twitter's server: first **widgets.js** is retrieved, and then the tweet ID is sent to Twitter, which returns the text of the tweet (along with all the other components of the iframe) to the page. (I believe there are other calls as well, such as CSS files, but I'm not counting those as significant server calls for these purposes.)

Similarly, when I created the first version of my iframe widget for SCOTUS Search, it also took two round trips to my server to display the iframe. The first call sent the ID number of the statement the widget needed to display, and the server responded by returning a JavaScript snippet to the page that, once loaded, would create an iframe with the source set to the SCOTUS Search server (requiring a separate call).

The reason I'd initially designed a widget that took two server calls to function properly (apart from inexperience) was because I wanted the second call to include the page URL where the widget would be displayed as a parameter in the call. So the first call returned a script that would grab the current page URL, then the second call would actually send it.

What I realized recently, in redesigning the widget, is that it shouldn't take two calls just to load an iframe widget: a JavaScript snippet can grab the page URL without calling an external server, and then this URL can be attached as a parameter to a server call that also includes the ID number of the statement to display.

Hence the SCOTUS Search snippet above (and reproduced here):

{% highlight html %}
<iframe id="statementFrame3394349" style="width: 100%; max-width: 400px; min-width: 250px; height: 300px; min-height: 300px; margin-top: 10px; margin-bottom: 10px; display: block; border-width: 0px;" scrolling="yes"></iframe><script type="text/javascript">document.getElementById("statementFrame3394349").src="http://www.scotussearch.com/statementwidget/QQ51Vr92OeqpHJ7T6_PO2w/3394349?page="+encodeURIComponent(window.location.href);</script>
{% endhighlight %}

In the above snippet, an iframe is created but without any source URL specified. This is followed by some JavaScript which sets the source of the just-created iframe to the SCOTUS Search server. But part of that server call includes the parameter `"page="+encodeURIComponent(window.location.href)`. This adds the current page URL to the server call without having to call the server beforehand.

(Side note: my original version of the widget actually included both the current *and* the referring page URLs in the final server call. But ultimately I decided this was probably a bridge too far: while it's fairly standard for a widget to pick up URL data from the page it's on, it's probably neither necessary nor particularly user-friendly to grab the previous page's URL as well. In any case, this is simply a judgment call from a user privacy standpoint: from a technical perspective, both the current and referring page URLs are equally easy to grab with JavaScript.)

As you can see, there are many paths to creating an embeddable iframe widget. In my case, I chose to make the embedding code snippet a little longer (by directly creating the iframe and then including JavaScript code to set its source) in order to reduce the number of server calls from two to one. In Twitter's case, the widget still uses two server calls, but the code snippet also includes the text of the tweet itself in case the calls never go through. A lot of these decisions depend on the particular circumstances of your app or site: in my case, I'm hosting SCOTUS Search on Heroku and am currently using only two dynos (web servers, essentially), so conserving server calls could result in some serious performance upgrades if traffic increases. In the case of Twitter, which likely uses some kind of distributed content delivery network (CDN), this is less of an issue.

And that's that for now.
