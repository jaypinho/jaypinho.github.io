---
title:  "Metering my news consumption"
excerpt: "Part 1 of an n-part series on the creation of Newsmeter."
date:   2015-05-10 16:25:00
tags: news journalism reading newsmeter
---
Two months ago I began working on a software project I dubbed Newsmeter.

The idea grew out of my desire to keep track of how much online news I consume on a daily basis, which news sites I visit the most, and which topics I return to the most frequently. As someone who considers himself a political junkie, I figured it'd be especially fun to gauge the extent of my interest in news and, more granularly, determine what I read about the most. (It didn't hurt that The Knight Foundation was running a ['News Challenge'](https://www.newschallenge.org/challenge/elections/entries/newsmeter-keeping-track-of-your-news-consumption-and-competing-with-friends-to-kill-off-the-daily-me) grant to fund projects related to civic engagement.)

Time-tracking extensions aren't exactly new, of course. One I've downloaded, [timeStats](https://chrome.google.com/webstore/detail/timestats/ejifodhjoeeenihgfpjijjmpomaphmah?hl=en), gives users a broad sense of which domains they spend the most time on and allows for the manual creation of custom domain categories so users can organize their online behavior into 'Work,' 'Fun,' and so on:

![The Chrome timeStats extension]({{ site.url }}/assets/timestats.png)

The problem I'm trying to solve was more specific. Forget email, social networks, and everything else: what, and how much, news am I consuming? That's all I care about. Secondly, I don't really want to have to actively manage my own tracking. I'm looking for a more 'set-it-and-forget-it' approach: anything that requires manual categorization or extensive setup is unappealing.

How, then, to reconcile my need to get the most granular data possible with my disinterest in manually collecting it? The answer, it seems, is a combination of a Ruby on Rails app (server-side) and a Chrome extension (on the browser side): the former to structure incoming data feeds and provide an intuitive reporting UI, and the latter to send scraped data to the server for every news page the user visits.

The preliminary result looks a little something like this:

![Newsmeter]({{ site.url }}/assets/newsmeter.png)

Across the top is a (fairly dense) area chart showcasing the number of distinct pages I've visited on news sites in the past 7 days. As you can see, my consumption is overwhelmingly dominated by [<em>The New York Times</em>](http://www.nytimes.com). I can also click on any one of the news organization titles below the chart to remove them and automatically redisplay my results. For example, here's my past week without <em>The New York Times</em>:

![Newsmeter, sans The New York Times]({{ site.url }}/assets/newsmetersansnyt.png)

You'll notice that the page views listed below the chart don't change dynamically with the chart above: my <em>New York Times</em> views live on even when they're removed from the area chart. I'm not sure yet if this is something I want to change, or if it makes more sense to keep the chart and the page views list below it functionally separate.

Right now Newsmeter has a fairly limited function set. Here, for example, is a search result for articles about 'Iran:'

![Newsmeter search for 'Iran']({{ site.url }}/assets/newsmeteriran.png)

Notice the various pieces of metadata associated with each page view: the title of the article, author, publication, time spent on the page (in the blue box), and an excerpt or snippet describing the piece. All of these data points are collected automatically, in the background, by my Chrome extension. The user doesn't have to do anything. Keyword metadata, where applicable, is saved as well, which helps power search results like the ones shown above. Here, for example, is a list of the keywords automatically scraped from the <em>New York Magazine</em> article titled ['Israel Reportedly Spied on U.S. Nuclear Talks With Iran'](http://nymag.com/daily/intelligencer/2015/03/israel-spied-us-nucelar-talks-iran.html):

![Newsmeter keywords]({{ site.url }}/assets/newsmeternymag.png)

There's still so much of the UI to build out. Here, for example, is another pull from my Postgres database containing a ranking of topics (based on scraped keywords) that I've read about in the past two months, including the number of articles I've read that are associated with each topic:

![Newsmeter topics]({{ site.url }}/assets/newsmetertopics.png)

Anyway, I think I'll stop there for now. My plan with this multi-part blog post series on Newsmeter is to keep a record of the progress I'm making over time and to walk readers through my thinking and decision-making processes both on a technical front (how to organize my database schema, for example, which I hope to get to later) and on a strategic front (i.e. what am I trying to accomplish here? which new feature is most important to add? etc.). Please do get in touch with me via email or Twitter (they're both listed at the bottom of this page), and let me know what you think!