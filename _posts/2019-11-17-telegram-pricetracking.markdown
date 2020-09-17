---
layout: post
title:  "Building a Telegram bot to automate price tracking e-mail notifications"
date:   2019-10-17 12:00:00 +0000
categories: projects
tags: projects python automation
---
[Medium Link](https://medium.com/@gokh/building-a-telegram-bot-to-automate-price-tracking-e-mail-notifications-b0a9fa230615) | 
[Github Repo](https://github.com/gokhj/email-watcher-bot)

This is not going to be a life-changer application; in fact, what I am doing here is just forwarding an e-mail notification to myself or a particular message group. However, this blog post would be a good introduction of Telegram bots and running them on the cloud.

Since Black Friday is on its way, I decided to write a little script to get price tracking e-mail alerts to our message group that I share with friends.

Bots, I love them. They save me a lot of time or some money in this case. Receiving a message from a bot about the weather forecast or a rain alert certainly doesn’t make me feel like Tony Stark, but it comes handy in situations.

Building this bot here doesn’t require any technical knowledge or familiarity with programming. Though, it certainly helps. I will try to explain every step, and at the end, you will have a little, no more than 40 lines of code that just works.

To build a bot, we need to have one. So we go and send a message to BotFather on Telegram, the instructions are below in the image. Copy to API token to manage the bot.

![create-bot](https://miro.medium.com/max/1050/1*5vtLX2pjmxG3Ns1LwVLi6Q.png)

We need to sign up to some services whether to store the bot on the cloud or handle emails. My choice will be Heroku as it gives me the peace of quickly deploying things, which is the idea about this blog post. You can sign up to Heroku from [here](https://www.heroku.com/).

![heroku-signup](https://miro.medium.com/max/1400/1*NxoIlFsibRn7Lty9XCwA0w.png)

We can deploy our application directly from GitHub, which is normally my daily choice. Although, I will use Heroku Git for this time but beware that it requires a separate installation of [heroku client](https://devcenter.heroku.com/articles/heroku-cli) on computer.

![cli](https://miro.medium.com/max/1400/1*WU8e9z6rKg9TuciClO7ZAw.png)

I found CloudMailin as an e-mail handling service; it literally came up first on Google. I recommend to sign up via their website, since it provides 10000 e-mails per month for free. Don’t install this app through Heroku since it only gives 200 e-mails per month. See, I started to save you something already.

To use CloudMailin, we put our Heroku app link as an endpoint; if you’re not sure what’s yours simply click on Open App icon on Heroku dashboard, it will redirect you to the Heroku app page. As for the POST Format, let’s go with JSON — Normalized.

![cloud-mailin](https://miro.medium.com/max/1400/1*FuPzjCvnW0Da-cN4kfj9vg.png)

After finishing the steps you will have a dashboard like this below. Copy receiving email address somewhere, we will use it to sign up for the services at the end.

![email-forward](https://miro.medium.com/max/1400/1*YU01sR6pei_z_haT_rrg_g.png)

Now we can start building our script. Let’s create a new folder with the name of your bot. Head in to the folder and create a file with .py extension, I went with price_watcher.py

I will be building this code with the help of 2 main libraries: Flask and Telebot. Flask will give me the possibility of creating an endpoint on the web so that I can receive requests. It’s one of my favourite libraries in Python, and you can use Flask for even building smart home applications or creating dynamic web pages. Telebot, on the other hand, is only making things faster to deploy, it’s not necessary but of course, makes things very straightforward.

Lets start with importing relevant libraries.

{% highlight python %}
from flask import Flask, request, Response
import telebot, json, os
{% endhighlight %}

Then we store our bot token and initiate the bot object simply doing the two lines next.

{% highlight python %}
api_token = "YOUR TOKEN HERE"
bot = telebot.TeleBot(api_token)
{% endhighlight %}

Now let’s write our first function.

{% highlight python %}
@bot.message_handler(commands=['hello'])
def hello_world(message):
    bot.send_message(message.chat.id, "Hello, World! btw your chat_id is: {}".format(message.chat.id))
{% endhighlight %}

I believe the line above is self-explanatory for a person who is familiar with programming. This is a simple function that replies me back “Hello, World!” and also adds the chat_id to the end of the sentence. Whenever a user sends a message to bot starting /hello command, it will launch the following function, hello_world(message). Message is the object here that we take as a parameter; it contains some useful information. When the function launched, the bot sends a message. This send_message function takes at least two parameters; one is the id of the chat which we can quickly grab from the message object and the message which we added as “hello world”.

Let’s build the flask part of the application now.

{% highlight python %}
app = Flask(__name__)@app.route('/' + api_token, methods=['POST'])
def getMessage():
 bot.process_new_updates(
  [telebot.types.Update.de_json(request.stream.read().decode("utf-8"))])
 return Response("Cheers love.", status=200)@app.route("/", methods=['GET', 'POST'])
def webhook():
    bot.remove_webhook()
    bot.set_webhook(url='https://pricewatcherbot.herokuapp.com/' + api_token) # for heroku
    return "<h1>Welcome to Price Watcher Bot</h1>", 200
{% endhighlight %}

In the first route we start the bot to receive messages, getMessage() function decodes the message for our bot to handle. In this case, we’re only interested in the messages starting with /hello. This route sends a response, but the important part is giving a status of 200, this means we are confirming that we received the message.

The second route is the route we use for the web. In its first line, I delete any existing webhooks to avoid conflicts, then create a new one for the page we are storing the code. It returns a welcome sentence in header format for the HTML. You can return and render HTML pages with this way, but it will not be our concern here. Check Jinja templates for it.

Finally we add the last bit to run our flask app.

{% highlight python %}
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(os.environ.get('PORT', 5000)))
{% endhighlight %}

Before uploading our code to Heroku servers, we need two more files; requirements.txt and Procfile. We will mention the specific libraries we are using in the first one, and configure the second file as a python3 web app.

requirements.txt:


{% highlight python %}
Flask==1.1.1
pyTelegramBotAPI==3.6.6
{% endhighlight %}

Procfile:
{% highlight python %}
web: python3 price_watcher.py
{% endhighlight %}

Now the bot is ready to work on Heroku. There will be additional modifications for the post request for the emails; however, let’s publish it now since we will need a chat id of the conversation, and our hello world function will provide that.

Let’s check if you have git installed on your computer by typing “git--version” or “which git”. If you don’t have git on your computer, install it here. After installing the Heroku CLI and Git, we open the terminal (mac, linux) or command prompt (windows) depending on your operating system.

```
heroku login
cd FOLDERNAME
```

cd means change directory, we change our directory with the folder that we add our files. For command prompt we go with dir instead of cd.

```
git init
heroku git:remote -a pricewatcherbot
git add .
git commit -am "My first commit"
git push heroku master
```

Git init initialises an empty repository for git. Heroku git: remote -a pricewatcherbot creates a remote repository on Heroku’s end named pricewatcherbot. In your case, add your bot’s name on it. After initialising the commit, we add everything within the folder simply by typing “git add .” The dot will add everything within the directory to git.

After then we need to send these files with a helpful message, that’s where commits come in. Now we can push everything to the server. After the push, Heroku does everything, including installing packages and running the Python server.

After succesfully deploying the application you can see your webpage returning the welcome header.

![welcome](https://miro.medium.com/max/1400/1*xdQnliIKaGbGowrabUEWJg.png)

So far it’s good. This screen means the bot is working in the background. Let’s say hello. We need that chat_id after all.

![first-test](https://miro.medium.com/max/1400/1*Ug_E6dlyuMGgM3hcBkAvGA.png)

The chat_id is essential for us to tell the bot where to send the message. It is the id of the conversation currently going on, to get the chat_id of a group chat add the bot to the group and do the /hello command. Let’s head back to price_watcher.py and add a variable named chat_id underneath the api_token. After then we can delete the whole function about hello world.

Lets get back to the second app.route, we need to add an if statement that whenever we receive a post request we programme our script to send that to us.

{% highlight python %}
if request.method == 'POST': # If we receive a file from the page
    post_json = request.get_json() # Create a JSON object of POST
    bot.send_message(chat_id, str(post_json)) # Sends that object to me
    return Response("OK", status=200) # Sends a response saying received
{% endhighlight %}

Now beware that, send_message() function can only send strings (basically text) as data, that’s why I’m converting this JSON object to a string with str() method. There are functions such as send_file() etc. on Telebot, but I prefer this way. Sue me if you want.

Just to avoid the confusion, this is the latest state of our code. I told you it’s less than 40 lines!

![full-code](https://miro.medium.com/max/1400/1*2ikG-MaoSCH4h1beUejqjw.png)

Now let’s commit our changes and push the code to Heroku servers.

```
git commit -am "Now we can receive Posts!"
git push heroku master
```

To try, let’s send a simple message to the email that CloudMailin provided us.

![send-email](https://miro.medium.com/max/1400/1*pDyB780ZKwS44O8_1gHuXg.png)

Good news, I received the e-mail. Bad news, I received it in the worst possible way.

![email-mirror](https://miro.medium.com/max/1400/1*4cXZ4gc7c74z7qB0Vg19nQ.png)

Now if you check this JSON object, there’s actually a field that contains the main part of the e-mail we’d like to get. It’s at the end of the code called ‘plain’. So if I only pick the plain element of this object/array, would I be receiving in the way that I want? Let’s try.

Just a little modification on lines 22 and 23.

```
text = post_json['plain']
bot.send_message(chat_id, text)
```

Let’s commit and push again to see if I receive it correctly.

![email2](https://miro.medium.com/max/1400/1*Fk16Shdo-ZLpOXt9nxH91A.png)
![message2](https://miro.medium.com/max/1400/1*ptAlhpXQiRLzsXcd1W57Uw.png)

Yes it works! Now I can get some spam emails!

This bot currently lives on a group that mainly we speak about “hot deals”. I use websites like camelcamelcamel (the perfect Amazon tracker), hotukdeals and pricespy to configure what type of deal I want and sign up with the email address that CloudMailin provided. This is a good way for us to discuss “how hot the deal is” with other people by automating all the hassle like reading the email, sending the information and deleting it from my inbox. At the end, I like a clean inbox.

![works](https://miro.medium.com/max/1400/1*0q2wbtnluyRbsur7P6UTyA.png)