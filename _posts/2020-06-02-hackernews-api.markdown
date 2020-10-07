---
layout: post
title:  "Automating HackerNews Stories"
date:   2020-06-02 12:00:00 +0100
categories: projects
tags: automation node javascript
---
[Live Project Link](https://hackernews.gokhanarkan.com/){:target="_blank"} |
[GitHub Repo](https://github.com/gokhj/hackernews-automation-api/){:target="_blank"}

Like most of the developers, I like to keep up with tech-related news happening across the world. I believe it is a vital part of my job, and thankfully, I love it. In my case, I prefer to read blog posts, news and articles or listen to podcasts about technology. My go-to website, in this case, is Y Combinator's Hacker News.

I like automating things, it is not because I am lazy; but I find them extraordinarily efficient in the long run. If I built a habit of some sort, I always try to automate it to save up some time. (So I can play Overwatch)

As a solution to the above problem, I thought about creating a little application to skim through the tech stories and send the top ones to my mobile device straight away. So in this blog post, I will be talking about my intentions and technological choice justifications about why I developed this API.

Moreover, this API is entirely public and available at all times for everyone. A friend has already created a little [reading app](https://hackernews-stories.netlify.app/){:target="_blank"} as a practice to learn React.

So let's get back to the post.

I am a Python guy; if I need to develop something, like a quick solution or automation, I almost always go with Python. It's simple and gives me rapid results. So when I started developing this project, I picked Python from my toolbox of programming languages.

Initially, I created this script to ensure that I get the data of news directly from HackerNews' Firebase real-time database.

**Python code:**

![Python code](/assets/hackernews/python-implementation.png)

When I run this code, It took 200 seconds to finish in a relatively new Macbook Pro. I was appalled. Like, was this normal? I executed the code again, and I got a similar result.

![Python performance](/assets/hackernews/python.png)

Therefore, I decided to write the same code in Nodejs. And I did.

**NodeJS code:**

![Node code](/assets/hackernews/node-implementation.png)

The same thing only took 3.1 seconds for Nodejs.

![Node performance](/assets/hackernews/node.png)

Honestly, less than 20 seconds was undoubtedly okay for me anyway, so 3.1 seconds was a miracle. I didn't test other languages at all since the Node result already fulfilled my condition. After I establish a decision on the programming language, I created an Express server to start the development. 

The API is using a Model-View-Controller design pattern, and I will explain the individual components below.

## Model

The ```model``` contains the database architecture. Currently, the API has only one model named *news*. This is to store the relevant information. I used MongoDB along with Mongoose.

![Model code](/assets/hackernews/model.png)

## View
The system has ```/routes/api/stories.js``` as the only route.
![Routes](/assets/hackernews/routes.png)

The subroutes are generating unique data for the request. Here is the code of /top, for instance.
![Top](/assets/hackernews/top.png)

## Controller
This is the place where I implemented the application logic. 

#### connect.js
This component has the functions implemented in the first place for testing request speed. Additionally, there are two further functions added; ```insertDB()``` inserts the individual story data to database, ```deleteDB()```, on the other hand, purges all the data stored to restart adding process.

#### instapaper.js
This component simply has one function for sending stories to my Instapaper account. I added some forethoughts such as to prevent sending stories with no URL. However, Instapaper API has already some of them; for instance, it does not accept duplicate stories, so I did not create anything to reassure that.
![Instapaper controller](/assets/hackernews/instapaper.png)

## App.js
App js is the main file to control over the MVC components as well as some CRON jobs. 

The first automated job ensures to update the database every 3 hours.

![First cron job code](/assets/hackernews/cron1.png)

The second cron job sends the top 10 stories to my Instapaper account at 23.55 every day.

![Second cron job code](/assets/hackernews/cron2.png)



## Results

Here is the proof; all stories are downloaded to my phone and available for offline reading. I think I invented the newspaper.

<div style="text-align:center;">
<img src="/assets/hackernews/iphone1.png" alt="Screenshot from iPhone"
    title="Screenshot from iPhone" width="300"/>
<img src="/assets/hackernews/iphone2.png" alt="Screenshot from iPhone"
    title="Screenshot from iPhone" width="300"/>
</div>

## Further Developments

Since it is a working product at the moment, the API opens so many possibilities to work on. I can create a mail list and send the top stories to people who have registered; I can generate keyword notifications so people also can receive stories on tailored keywords. These are the ideas I thought about whilst I was writing this post, and I am sure many more features can be implemented. However, this is the end for me. Overall, I think it was a useful project, and I will be using it daily.

Thanks!
