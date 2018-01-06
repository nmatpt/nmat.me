---
layout: post
title: Twitter4j extended mode
---

Recently Twitter increased the character limit from 140 to 280 characters. To get the full text of tweets via their API you need to [explicitly set extended mode](https://developer.twitter.com/en/docs/tweets/tweet-updates) as a request parameter.

To enable this in the latest `twitter4j` version (4.0.6) you should use the `setTweetModeExtended` method in the `ConfigurationBuilder`:

```java
twitter4j.conf.ConfigurationBuilder()
      .setOAuthConsumerKey(""))
      .setOAuthConsumerSecret("")
      .setOAuthAccessToken("")
      .setOAuthAccessTokenSecret("")
      .setTweetModeExtended(true)
```

If you are running an older twitter4j version there is a workaround [here](https://gist.github.com/nishemon/8618f0eb8cb78e6dcfe6217a7e7cb24b).

Note that for retweets you have to use `getRetweetedStatus().getText()` instead of just `.getText()`.
