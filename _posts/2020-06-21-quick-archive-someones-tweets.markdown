---
layout: post
title:  "Quickly Archive Public Twitter Accounts"
date:   2020-06-21 12:00:00 +0100
categories: projects
tags: python telegram twitter
---

I recently discovered that if a tweet link is shared on Telegram, it will stay there forever (possibly) even though the account's long gone or the tweet is deleted.

Since Twitter API allows to access a user's recent 3000 tweets, this gave me an idea to create a Telegram bot to archive other people's tweets.

I used Tweepy's Cursor method to access someone else's timeline, particularly their own tweets by implementing this bit:

```python
username = "ekrem_imamoglu"
# This will get the tweets of https://twitter.com/ekrem_imamoglu
user_tweets = tweepy.Cursor(api.user_timeline, id=username).items()
for tweet in user_tweets:
    print(tweet.text)
```

This snippet can eventually be used for storing and analysing information of accounts on Twitter. In this script, I am passing each tweet to the conversation so the Telegram hosts will open up a connection and index the data to their database.

I am putting the full code below, I believe the lines are pretty much self-explanatory, but I added comments where necessary.

![Script](/assets/archive_tweets/image_796.png)

The repository can be forked [here](https://github.com/gokhj/telegram-tweet-archive), and the following steps need to be done to create your bot.

1. Create a telegram bot by messaging ```/newbot``` to [BotFather](https://t.me/botfather)
2. Register to [Twitter Developer Portal](https://developer.twitter.com/en/apps) and create an app
3. Install libraries ```python install -r requirements.txt```
4. Change the environment variables with your ones
5. Run the bot ```python archivebot.py```
6. Send the command to the bot, for example: ```/user ekremimamoglu``` (this command will download the latest 3000 tweets of the Mayor of Istanbul)
7. Profit.

Here is an example of what will happen when you finish and execute the script.

<div style="text-align:center;">
<img src="/assets/archive_tweets/telegram_screenshot.png" alt="Screenshot from iPhone"
    title="Screenshot from iPhone" width="300"/>
</div>

Stay safe and have a nice day!