---
layout: page
permalink: /bkdemo/
---
## BlueKai - Google Analytics Integration
---
<br>

#### Sections

1. [Objectives](#step1)

2. [Overview](#step2)

3. [Configuring the BlueKai tag](#step3)

4. [Configuring the Google Analytics tag code](#step4)

5. [Configuring the Google Analytics account](#step5)

6. [Exporting data from Google Analytics](#step6)

7. [Creating classification rules within BlueKai](#step7)

8. [Importing the analytics data into the DMP](#step8)

9. [Verifying that the data has been successfully imported](#step9)

---
<br>

<a name="step1"></a>

#### 1. Objectives

- Send BlueKai Universal User IDs (BKUUIDs) into the Google Analytics (GA) tag in order to associate page- and event-level data with BlueKai users.

- Export this Google Analytics data, tied to BKUUIDs, via the Google Analytics API.

- Import the result into the BlueKai DMP via the User Data API.

---
<br>

<a name="step2"></a>

#### 2. Overview

The simplest and most elegant way to achieve these objectives is to implement *postMessage* and *receiveMessage* functions inside and outside the BlueKai container iframe, respectively, so that the BKUUID can be sent from within the BlueKai container iframe to the external Google Analytics tag.

No code needs to be modified on the BlueKai container iframe, thus sparing client IT departments from the headache of redeployment, and the Google Analytics tag only needs to be modified slightly. The only real downside is the potential to miss analytics data if the BlueKai tag either doesn't exist on the page or fails to load properly (since the Google Analytics tag will need to wait for the BlueKai tag to load before firing). To mitigate this potential problem, the Google Analytics tag should be set up to fire on the page after a specified number of milliseconds if the BlueKai tag fails to load.

Let's begin.

---
<br>

<a name="step3"></a>

#### 3. Configuring the BlueKai tag

Within your BlueKai account, navigate to tag management (in the menu bar as **Manage > Tags**) and set up a tag that will fire across **all client site pages**. (See our [Knowledge Base article](https://kb.bluekai.com/display/PD/Managing+Tags) for more information on how to set up tags and schedules.) The tag should contain the following settings (you may need to change 'HTTP Type' according to the page protocol):

![The BlueKai tag]({{ site.url }}/assets/bk_ga_tag.png)

As shown above, the tag code consists entirely of this:

{% highlight html %}
<script>

  var bkid = '$_BK_UUID';
  parent.postMessage(bkid, '*');

</script>
{% endhighlight %}

This tag uses the **$_BK_UUID** macro to assign the BlueKai universal user ID (BKUUID) to a variable called **bkid**, and then sends a *postMessage* to the BlueKai iframe's parent (which should be the main page itself) containing that variable.

Next we'll configure Google Analytics to pick up that message.

---
<br>

<a name="step4"></a>

#### 4. Configuring the Google Analytics tag code

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

{% highlight js %}
  ga('send', 'pageview');
{% endhighlight %}

That line is what actually sends analytics data to the Google servers, so we're removing it to prevent anything from being sent to Google until the BKUUID can be inserted first.

Next, we have to set up a JavaScript function on the page itself to listen for the *postMessage* from the BlueKai iframe and then insert the BKUUID into the Google Analytics call. We can do this as follows:

{% highlight js %}
  function receiveMessage(event) {
    ga('set', 'dimension1', event.data);
    ga('send', 'pageview');
  }
  addEventListener('message', receiveMessage, false);
{% endhighlight %}

This above code sets the value of a new 'custom dimension' (more on that [here](https://support.google.com/analytics/answer/2709828?hl=en)) to the BKUUID, then sends the page view data (using the line of code we'd earlier removed from the default Google Analytics tag code) to Google's servers.

At this point we've successfully sent the BKUUID to Google Analytics on every page view. You can test this by loading any page containing the tag setup we've configured above. (Note: BlueKai tags take up to 1-2 hours to propagate, so you may need to wait for a bit before the BlueKai tag begins sending BKUUID data.) Here, for example, are the parameters sent to Google when its tag fires:

![Parameters sent to Google Analytics]({{ site.url }}/assets/ga_params.png)
<p>&nbsp;</p>
Notice the 'cd1' parameter second from the bottom? That's a 'custom dimension' containing the BlueKai ID received from the BlueKai tag.

Now we need to ensure that your Google Analytics account is set up to store this custom dimension data.

---
<br>

<a name="step5"></a>

#### 5. Configuring the Google Analytics account

The first step is to go to your Google Analytics account and click on 'Admin' on the top menu bar. On the resulting page, find your 'Property' (which is likely the middle one of three columns on the page), click on 'Custom Definitions,' and then select 'Custom Dimensions.'

![Google Analytics custom dimensions]({{ site.url }}/assets/custom_dimension.png)

On the resulting page, click on 'New Custom Dimension.' Give it a name like 'BK User ID Dimension,' change the Scope to 'User,' and make sure the box marked 'Active' is checked. Then hit 'Create.' Google Analytics will now save all incoming BlueKai UUIDs to this new dimension.

(Important note: if this is *not* your first custom dimension for this property, you need to ensure that the tag code we set up earlier matches the index of the dimension you just created. If it doesn't, that tag code -- `ga('set', 'dimension1', event.data);` -- needs to be revised to reflect 'dimension3' or whatever the new index is.)

---
<br>

<a name="step6"></a>

#### 6. Exporting data from Google Analytics

To test the process for exporting this data from Google Analytics, go to Google's API tool, [Query Explorer](https://ga-dev-tools.appspot.com/query-explorer/). Sign in to your Google Analytics account, select the appropriate Property, and then set the query parameters you'd like to export, as seen below:

![Google's Query Explorer]({{ site.url }}/assets/query_params.png)

Finally, click on 'Run Query.' The results will display all BKUUIDs that have been captured, along with their number of sessions and session duration (assuming you used the same metrics as in the above screenshot). More importantly, the page also displays the API query you would use to obtain this data in JSON format.

---
<br>

<a name="step7"></a>

#### 7. Creating classification rules within BlueKai

Before ingesting this Google Analytics data into the DMP, you have to create new data categories to hold the data first. This can be done via the [self-classification feature](http://docs.oracle.com/cloud/latest/marketingcs_gs/OMCDA/#Help/Platform/ManagingTaxonomy/Using-Self-Classification-Tools-to-Manage-your-Taxonomy_12878472.html).

In short, each category must have an associated rule that places users with specific key-value pairs (called "phints") into the appropriate DMP categories. Both the categories and the rules for them can be created via self-classification.

There is no specific "best practice" for transforming the JSON results from the step above into key-value pairs that can be ingested into BlueKai via the User Data API. But ideally this process would be automated via a script that turns JSON results from the Google Analytics API into a list of individual API call strings containing the BlueKai user ID and associated key-value pairs you'd like to use to classify those users.

In short, you need to have some way of turning results like these (likely in JSON format, however):

![Google Analytics data]({{ site.url }}/assets/ga_results.png)

...into BlueKai API calls like these:

    api.tags.bluekai.com/getdata/26881/v1.2?userid=x2Iix88P99OsB5PP&phint=page%3Dtestpage&bkuid=XXXXXXXXX&bksig=XXXXXXXXXXXXX

---
<br>

<a name="step8"></a>

#### 8. Importing the analytics data into the DMP

Now that the Google Analytics data has been exported in machine-readable format and BlueKai categories and rules have been created to receive this new data, the final step is to import the data into the BlueKai DMP. This is done via the User Data API. Overall documentation is [here](https://kb.bluekai.com/display/PD/User+Data+API), but you can also find a [programming example](https://kb.bluekai.com/display/PD/Programming+Example) for constructing an API call, as well as additional details on the [authentication workflow](https://kb.bluekai.com/display/PD/Authenticating+BlueKai+API+Calls).

As seen above, an individual BlueKai API call will look something like this:

    api.tags.bluekai.com/getdata/26881/v1.2?userid=x2Iix88P99OsB5PP&phint=page%3Dtestpage&bkuid=XXXXXXXXX&bksig=XXXXXXXXXXXXX

Keep in mind that a separate API call must be made for **each** user ingested into the DMP.

---
<br>

<a name="step9"></a>

#### 9. Verifying that the data has been successfully imported

Typically, the BlueKai UI shows a category's unique user population with a lag of 1-3 days. Assuming that enough users have been ingested via the BlueKai User Data API, you should be able to verify that they were successfully transferred by checking the category population in the **Create Audience** screen.
