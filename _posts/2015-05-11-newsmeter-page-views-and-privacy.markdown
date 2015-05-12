---
title:  "Newsmeter: page views and privacy"
excerpt: "Part 2 of an n-part series on the creation of Newsmeter."
date:   2015-05-11 23:25:00
tags: news journalism reading newsmeter
---
When I [submitted Newsmeter](https://www.newschallenge.org/challenge/elections/entries/newsmeter-keeping-track-of-your-news-consumption-and-competing-with-friends-to-kill-off-the-daily-me) to the Knight News Challenge, one of the questions I made sure to address centered on privacy:

> <strong>How will Newsmeter protect its users' privacy while collecting all this data?</strong>
> 
> Newsmeter will never store any data that's collected from non-news sites. We have an internal database of news domains -- nytimes.com, cnn.com, lemonde.fr, etc. -- and whenever an incoming URL doesn't match one of those sites, we discard it immediately. We have no interest in collecting purchase information or email content. We just want to help you better understand how you consume news.

This is entirely true. The moment Newsmeter finishes checking the domain whitelist (the full list of sites will be publicly accessible once Newsmeter is in production), it will immediately discard any page view that doesn't match:

{% highlight ruby %}
render :nothing => true, :status => 204 and return if is_whitelisted == false
{% endhighlight %}

That said, the Chrome extension is sufficiently 'dumb' such that all page views -- not just those on news sites -- are sent to the server, which then checks the URL against the domain whitelist. So if I visit, say, [this article](http://www.nytimes.com/2015/05/12/sports/football/tom-brady-suspended-for-four-games-and-patriots-fined-1-million.html) on American hero Tom Brady's tragic four-game suspension, the server checks the URL, finds that it comes from 'nytimes.com,' and stores the page view's metadata because its domain is on the whitelist. If it weren't, the server would discard all the page view data immediately. In other words, I'm operating on the honor system: theoretically I could be storing users' entire browsing history. I'm just promising that I won't.

Is there a way around this? In theory, yes. A local cache could be regularly synced to the server-hosted domain whitelist so that the extension doesn't even bother sending page view data to the server at all unless the domain matches up.

So why haven't I done this? Well, because...I'm still learning how extensions work. Since beginning to teach myself Ruby on Rails in earnest a little over a year ago, my efforts have been focused almost entirely on Rails and other languages, tools, and platforms that work together with Rails: PostgreSQL, Heroku, etc. I've never created a browser extension before Newsmeter, and my knowledge of the inner workings of local storage is pretty limited at this point. Therefore I've chosen to discard non-whitelisted page views at the server level rather than at the browser level (essentially, since my knowledge of Rails outpaces my familiarity with JavaScript).

There are other considerations as well. Right now, for example, my domain whitelist is limited to around 60 news sites. This is a very manageable number, and those domain names could easily be stored locally. But eventually I expect this list to expand to hundreds or even thousands of domains. Indeed, one of my numerous ideas for expanding Newsmeter involves allowing users to create their own (private) custom whitelists to supplement the default one. All of this would substantially increase the amount of local storage and processing time necessary to ensure that non-whitelisted page views never make it to the Newsmeter server in the first place.

That doesn't mean I'm not going to do it, of course. Despite my sincerity in promising that I won't keep non-whitelisted data, I wouldn't fault users for not trusting me. (Much of the tech industry's recent history -- from lackadaisical security to obscure privacy settings to the Edward Snowden revelations -- has proven to users that they should trust everyone in the software business a bit less generally.) Right now, toiling as I am in the very early stages (and without a user base to worry about), privacy has been put somewhat on the back burner. But even something as technical as when the whitelist is checked is something I'll need to pay closer attention to as I begin inviting users to sign up.