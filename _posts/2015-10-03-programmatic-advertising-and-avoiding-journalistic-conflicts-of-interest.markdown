---
title:  Programmatic advertising, and avoiding journalistic conflicts of interest
excerpt: How some controversial ad tech can prevent controversy from getting controversial on the editorial side.
date:   2015-10-03 00:37:00
tags: online advertising ad tech programmatic journalism newspapers news conflict of interest
---
Programmatic advertising is under the gun lately. As *The Wall Street Journal* [reported](http://www.wsj.com/articles/publishers-that-say-no-to-automated-ad-sales-1443650792) on Wednesday:

> Almost every publisher on the Web now teams up with “ad tech” firms, specialists in software that automates ad sales. Most media industry executives consider it a must to employ such brokers, the “networks” and “exchanges” that serve as middlemen in the roughly $60 billion online advertising marketplace.
>
> But Refinery29 and a host of other new-media companies—among them, Vice Media, Vox Media, BuzzFeed and Mic—are bucking that approach, arguing that automated ad technologies are to blame for overrunning the Internet with too many ads and obnoxious tracking mechanisms.

So what exactly is programmatic advertising? There are about as many definitions as there are programmatic ad tech firms, but [here's a generally good one](http://digiday.com/platforms/what-is-programmatic-advertising/):

> “Programmatic” ad buying typically refers to the use of software to purchase digital advertising, as opposed to the traditional process that involves RFPs, human negotiations and manual insertion orders. It’s using machines to buy ads, basically.

So why is the programmatic ad space ([and ad tech in general](http://www.wsj.com/articles/ad-tech-stocks-keep-falling-1443735887)) facing increased scrutiny now? A big part of it is a little snippet from that first WSJ piece I mentioned:

> Some 55% of such “programmatic” ad spending in the U.S. in 2014 went to middlemen, not publishers, according to the Interactive Advertising Bureau.

If that sounds like a lot, it's because it is. (The full study, which was published in July, can be read [here](http://www.iab.net/media/file/PwC_IAB_Programmatic_Study.pdf).) This means that for every dollar an advertiser like Target or Bank of America spends on reaching users across the web, only $0.45 is making it to the sites that are actually hosting the ads themselves. The rest of it -- more than half -- is gobbled up by "middlemen," including everything from DSPs to SSPs, ad exchanges, ad networks, and so on.

Worse still, the more middlemen that exist, the longer it takes these publishers' pages to load, creating a bad experience for their site visitors. ([I covered this]({% post_url 2015-09-25-the-ad-blocking-meltdown %}) in a post last week.) This is why publishers like Vox, Refinery29, etc. are saying no to ad tech. (That said, this counter-trend is itself overstated. On a visit to the Vox.com home page today, my AdBlock Plus extension blocked 13 separate ad tech calls.)

So there are definite downsides to programmatic advertising. At this point I should probably make a distinction, by the way: people often use 'real-time bidding' (RTB) and 'programmatic' interchangeably, but technically the former is a subset of the latter. What I'm about to posit in this post relates more specifically to RTB than to programmatic broadly, so here's the distinction (from that same explainer earlier):

> Real-time bidding is a type of programmatic ad buying, but it isn’t the only one. RTB refers to the purchase of ads through real-time auctions, but programmatic software also allows advertisers to buy guaranteed ad impressions in advance from specific publisher sites.

OK, so here's what I'm thinking: **if you're a newspaper journalist or publisher, one of the unsung benefits of RTB is that it obscures the advertisers from the editorial side of the business.** Indeed, it largely obscures the advertisers from the business side as well.

In other words, RTB holds the potential to reduce the friction between the editorial and business side of any news organization. Historically, there's always been that "Hey, are we allowed to write about net neutrality when Comcast is selling ads in our newspapers?" tension. This was because, back in the days of print, ad spots were purchased beforehand in packages: there was a price associated with specific slots in the daily paper, and this was all worked out in advance of the actual printing. This created a lot of potential for conflicts of interest, a problem that has persisted into the digital age. ([Ever heard of Buzzfeed?](http://tktk.gawker.com/buzzfeed-deletes-post-critical-of-dove-a-buzzfeed-adve-1696852834))

But with real-time bidding, neither the journalists nor their editors (nor even the publisher himself) knows in advance what ads are going to run on their site until they actually run. And even then, ads are displayed in a manner specific to each individual user: no two users see all the same ads on all the same pages. So even *after* the fact, once that theoretical pro-net neutrality piece goes up, the actual distribution of ad dollars and advertisers in an RTB scenario is still somewhat obscured. Chances are, no one in the newsroom would know which advertisers spent the most money placing creatives on the latest article. So this could drastically reduce the potential for a journalistic conflict of interest: if you don't know in advance who's going to advertise on your site, how can you be compromised?

The counter-argument, of course, is that this is wildly simplistic. First, almost all premium inventory -- e.g. home page banners, takeovers, etc. -- will still be sold direct (whether programmatically or not) and will also command a disproportionate share of the publishers' ad revenues, meaning significant chunks of inventory will still be known beforehand.

Secondly, for sites like Buzzfeed whose ads are seamlessly integrated into the content via (presumably) direct buys, the church-state wall is about as porous as the Greek national border. Trying to use programmatic advertising to reduce editorial conflicts would be like handing me a pencil so I can put up a better fight against Floyd Mayweather, Jr. It's not going to make a discernible difference.

Nevertheless, I'd love to hear from people working in online ad tech, journalism, and media generally: is the above theory completely wacky, or is there a kernel of truth in there somewhere?
