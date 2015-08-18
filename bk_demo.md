---
layout: page
permalink: /bkdemo/
---
## BlueKai - Google Analytics Integration
---
<br>

#### Objectives

1. Send BlueKai Universal User IDs (BKUUIDs) into the Google Analytics (GA) tag in order to associate page- and event-level data with BlueKai users.

2. Export this Google Analytics data, tied to BKUUIDs, via the Google Analytics API.

3. Import the result into the BlueKai DMP via the User Data API.

---
<br>

#### Possible solutions

There are at least three possible ways to achieve the above objectives.

1. Configure the BlueKai container to fire tags *outside* of the iframe, thus allowing a Javascript variable containing the BKUUID to be accessible to the Google Analytics tag.  

2. Assume that both the Google Analytics tag and the BlueKai ID swap tag are both firing within some sort of external tag management system, such that the Google Analytics tag can access the BKUUID in a universal data layer or something similar.

3. Implement *postMessage* and *receiveMessage* functions inside and outside the iframe, respectively, so that the BKUUID can be sent from within the BlueKai container iframe to the external Google Analytics tag.

**Option 1** is possible but presents multiple challenges. First, it would require a redeployment of BlueKai container tags throughout the client site: allowing tags to fire outside of the BlueKai iframe is not the default setting, so changing this would require adding a line of code to the standard BlueKai tag code on client sites. Secondly, this option would also expose client sites to more vulnerability, given the ability to fire tags directly onto the page itself.

**Option 2** may be possible but cannot be easily explored without access to various third-party tag management systems. Each implementation would differ depending on the tag management product being used, thus precluding the possibility of a universally applicable solution.

**Option 3** is the simplest and most elegant of the three. No code needs to be modified on the BlueKai container iframe, thus sparing client IT departments from the headache of redeployment, and the Google Analytics tag only needs to be modified slightly. The only real downside is the potential to miss analytics data if the BlueKai tag fails to load properly (since the Google Analytics tag will need to wait for the BlueKai tag to load before firing).

Thus we will proceed with **Option 3**.

---
<br>

#### Instructions

Here's a typical Google Analytics tracking tag:

{% highlight html %}
<script>

    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
      (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
      m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
      })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

    ga('create', 'UA-XXXXXXXX-1', 'auto');
    ga('send', 'pageview');

</script>
{% endhighlight %}

Assuming some variation of the above tag already exists on the client site, the first thing we need to do is **remove** the following line of code:

{% highlight html %}
  ga('send', 'pageview');
{% endhighlight %}

That line is what actually sends analytics data to the Google servers, so we're removing it to prevent anything from being sent to Google until the BKUUID can be inserted.

Now, within BlueKai tag management, set up a tag to fire across **all client site pages** with the following settings:

![The BlueKai tag]({{ site.url }}/assets/ga_bk_tag.png)

This tag assigns the BlueKai universal user ID (BKUUID) to a variable called 'bkid' and then sends a *postMessage* to the BlueKai iframe's parent (which should be the main page itself) containing that variable.

The last thing we have to do to implement the Google Analytics tag is to set up a function on the page itself to listen for the *postMessage* and then insert the BKUUID into the Google Analytics call. We can do this as follows:

{% highlight js %}
  function receiveMessage(event) {
    ga('set', 'dimension1', event.data);
    ga('send', 'pageview');
  }
  addEventListener('message', receiveMessage, false);
{% endhighlight %}

This above code sets the value of a new 'custom dimension' (more on that [here](https://support.google.com/analytics/answer/2709828?hl=en)) to the BKUUID, then sends the page view data (which we'd earlier removed from the default Google Analytics tag code) to Google's servers.
