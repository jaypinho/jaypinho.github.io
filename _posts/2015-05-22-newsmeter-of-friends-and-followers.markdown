---
title:  "Newsmeter: of friends and followers"
excerpt: "Integrating social networks into an app is more complicated than you'd think."
date:   2015-05-22 01:22:00
tags: newsmeter friends social networks followers
---
I've been tinkering with Newsmeter again in my spare time.

On the UI front, I spruced up the login page slightly. Instead of a boring text link, the user is now presented with rounded buttons.

The second front-end change you may have noticed is that I added an option to log in via Twitter as well: previously the only option was via Facebook.

![Newsmeter login screen]({{ site.url }}/assets/newsmeterlogin.png)

Coupled with the above changes on the UI side was a more significant one under the hood. I'm just now beginning to incorporate the concept of friends -- or followers, or connections: I haven't settled on the terminology just yet -- into Newsmeter.

This has turned out to be a much more complex process than I'd imagined. Here are a few of the reasons why:

#### Directionality of friendships

One of the first questions you have to resolve while building a site that allows users to make 'friends' is whether the relationships are one-way or two-way.

Twitter's relationships are one-way: that is, you can follow someone without them following you back. Furthermore, unless their account is protected, you don't even need to ask their permission: you just hit the Follow button and you've established a connection. But unless they decide to follow you back, it's a one-way street: you'll see their tweets in your timeline, but they won't see yours in theirs. (Tumblr blogs represent another example of a network that uses one-way relationships.)

Facebook, on the other hand, is based primarily on two-way relationships: to add someone as a friend, they have to approve you. And once they've done that, you can both see each other's profiles. There's no such thing as friending someone without them adding you as a friend simultaneously. In other words, it's a two-way relationship.* (LinkedIn operates similarly with its 'connections.')

For Newsmeter, I decided fairly early on that I wanted to take the Twitter approach: every friendship would be one-way. This makes access privileges much more individualized as well: if I want to share only some of my data with Person X but Person X wants to share all of her data with me, this is easy to implement using one-way relationships. That is, my relationship to her would have one setting (a more restricted one), and hers towards me would have a different (more open) one.

#### The Facebook and Twitter APIs return different data

Newsmeter uses [OmniAuth](https://github.com/intridea/omniauth), an open-source and surprisingly simple authentication package that makes it easy for coders to build apps in which users can log in using popular existing platforms -- like Facebook, Twitter, and LinkedIn, for example.

When a login using OmniAuth is successful, data is returned to the app. Here, for example, is [a sample response](https://github.com/mkdynamic/omniauth-facebook) from an OmniAuth Facebook login:

    {
      :provider => 'facebook',
      :uid => '1234567',
      :info => {
        :nickname => 'jbloggs',
        :email => 'joe@bloggs.com',
        :name => 'Joe Bloggs',
        :first_name => 'Joe',
        :last_name => 'Bloggs',
        :image => 'http://graph.facebook.com/1234567/picture?type=square',
        :urls => { :Facebook => 'http://www.facebook.com/jbloggs' },
        :location => 'Palo Alto, California',
        :verified => true
      },
      :credentials => {
        :token => 'ABCDEF...', # OAuth 2.0 access_token, which you may wish to store
        :expires_at => 1321747205, # when the access token expires (it always will)
        :expires => true # this will always be true
      },
      :extra => {
        :raw_info => {
          :id => '1234567',
          :name => 'Joe Bloggs',
          :first_name => 'Joe',
          :last_name => 'Bloggs',
          :link => 'http://www.facebook.com/jbloggs',
          :username => 'jbloggs',
          :location => { :id => '123456789', :name => 'Palo Alto, California' },
          :gender => 'male',
          :email => 'joe@bloggs.com',
          :timezone => -8,
          :locale => 'en_US',
          :verified => true,
          :updated_time => '2011-11-11T06:21:03+0000'
        }
      }
    }

For whatever reason it's not shown in the supplied example, but one of the data points in the JSON response includes the logged-in user's list of Facebook friends.

#### Slight tangent alert

**Importantly, this API response only includes friends who have also logged in to the same app at some point and approved whatever permissions it asked for.**  This is [a fairly big change](http://techcrunch.com/2015/04/28/facebook-api-shut-down/) that Facebook implemented just this month -- big enough that it made the news in several publications, especially as it related to [political targeting strategies](http://adage.com/article/campaign-trail/election-facebook-tool/295706/) online.

In other words, if I log into Newsmeter using Facebook, Newsmeter will only receive the user IDs for my Facebook friends that have also logged in to Newsmeter -- which is guaranteed to be a tiny fraction of my total friends list. (It's crazy this was ever *not* the case, really.)

This is somewhat tangential, but, alongside this change, Facebook introduced the idea of ['app-scoped' IDs](https://developers.facebook.com/docs/apps/upgrading#upgrading_v2_0_user_ids) with v2.0 of its Graph API:

> Facebook will begin to issue app-scoped user IDs when people first log into an instance of your app coded against v2.0 of the API. With app-scoped IDs, the ID for the same user will be different between apps.

This is part of the ongoing war of attrition in the online ID space, which is very hotly contested right now. Every major player in online ad tech wants to own the 'ID graph,' a definitive mapping of who's who across multiple devices. Part of building out such an extensive graph is preventing competitors from stealing this data via your API: thus, while I may be Facebook User 123 on Newsmeter, I may appear as Facebook User 456 to, say, FarmVille or whatever other app connects to Facebook's API. Facebook knows that User 123 is the same person as User 456, but no one else does, and thus FarmVille and Newsmeter can't pool IDs based off of Facebook's network.

Twitter operates differently: every user has a public ID, and it's the same no matter which app is accessing it. Privacy is less of an issue since Twitter timelines are assumed to be public anyway (again, except for the protected ones). That said, Twitter doesn't return any follower data (neither whom you follow, nor who follows you) when you authenticate on Twitter using OmniAuth. So the process for obtaining a list of followers is more complicated for Twitter than it is for Facebook.

#### What does it mean to be my friend?

This brings up my next point, which is that there are a lot of different ways to build out a 'friendship' architecture for a new app or site. I've already settled the question of one-way vs. two-way, but many other questions remain.

Perhaps most importantly, where should 'friendships' be stored? Within a Newsmeter database or on the social networks? If, say, I'm friends with Person X on Facebook, which then makes us friends on Newsmeter as well, what happens to our friendship on Newsmeter if he defriends me on Facebook? Should Newsmeter automatically defriend us? Should it ask us whether we'd like to defriend? Should it let us stay Newsmeter friends even though we're no longer friends on Facebook?

This question gets even more complicated with the introduction of multiple login choices. I can now connect both my Facebook and Twitter accounts to a single Newsmeter account. (This is done by joining a User table with an Authorization table: one user, Jay Pinho, can have several authorizations -- say, one for logging in via Facebook and one for logging in via Twitter.) If I'm friends with someone on Facebook and they follow me on Twitter, and then I join Newsmeter, after which they unfollow me on Twitter but remain my Facebook friend, how should Newsmeter interpret that, if at all?

Even more fundamentally, how should friendships even be established in the first place? Should there be a 'Find Your Friends' button (like the ones that appear on many social apps these days) where users can manually select which Facebook and Twitter contacts to 'friend' on Newsmeter?

All of these questions will have to be answered over time, and they're very much subject to change as I test out Newsmeter and hear from other early users. But here's how I'm leaning right now:

- Friendships will be stored entirely on Newsmeter's servers, not based on social networks. In other words, you can friend people on Newsmeter based on your preexisting social network friendships -- but once you've become Newsmeter friends, no activity on external social networks will have any impact on your Newsmeter friendship. You'll have to manually defriend someone in the Newsmeter UI to get rid of them.
- 'Friending' someone on Newsmeter, while similar to Twitter in that it's one-way, will be more like Facebook in terms of the effect: on Twitter, following someone means you get to read their tweets. On Newsmeter, as on Facebook, 'friending' someone will mean that they'll get (some level of) access to *your* data. (Otherwise you could just follow anyone you're curious about snooping on.)
- Newsmeter will provide a list of people you could 'friend' based on who uses the app. For users who've logged in via Facebook, this will be straightforward: if your Facebook friends have ever logged in to Newsmeter, you'll have the option of friending them on Newsmeter. For Twitter, my inclination is to allow users to friend both their followers and their followees (people they follow), again only pertaining to the subset that use Newsmeter.

#### Access levels

What will it even mean to be friends with someone on Newsmeter? How many different levels of access will there be? Will there be a setting allowing friends of mine to view every single new story I've browsed since I started using the site? Just generalized stats? Something in between?

#### Conclusion

For most of these questions, there is no right answer. In general, on questions relating to sharing, a good general principle to go by is to err towards allowing users to opt in to sharing more, rather than sneakily publicizing their data by default. By the way, when I write 'a good general principle,' I mean from an ethics perspective: from an advertising point of view, opt-in is utterly disastrous. (No one ever does it.) But there are caveats even for something as seemingly straightforward as protecting user privacy. Most obviously, if the app forces users to put in additional effort just to share data with friends -- an activity I hope to make a central part of the Newsmeter experience -- they're far less likely to ever realize they can use the app that way and, therefore, will probably find it less useful or interesting overall.

Sometimes gentle nudging can go a long way. But it's a very gray area.

\* *Interestingly, over time these two models have converged somewhat, mostly by Facebook moving towards Twitter's model: Facebook has introduced the concept of 'unfollowing' friends (which keeps them as friends but hides their posts from your timeline), differentiated between posts published to the public versus those made only visible to friends, and so on. Meanwhile, Twitter has stayed mostly consistent with its approach to one-way relationships, even solidifying the concept by adding 'muting' and removing the requirement that direct messages can only ever be sent to users who already follow you.*
