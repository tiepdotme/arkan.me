---
layout: post
title: "Tiny Express Server for People Who Want to Contact Me in a Non-conventional Way"
date: 2020-10-05 12:00:00 +0100
categories: projects
tags: javascript node
---

[Live Project Link](https://post.arkan.me/) |
[GitHub Repo](https://github.com/gokhj/post.arkan.me/)

There is this company called Plaid I have recently heard because it was all over the news as [Visa acquired it for \$5.3 billion](https://techcrunch.com/2020/01/13/visa-is-acquiring-plaid-for-5-3-billion-2x-its-final-private-valuation/). I have an eccentric habit that I tend to click on the careers page on every company website I visit. In fact, I browse the software engineering roles and compare the requirements with me to catch up with what they want from a candidate these days. It is indeed an excellent way to catch up with the industry, new technologies and such.

So, I visited Plaid’s careers page and found something unusual here; besides, I immediately loved it. I adore these kinds of gimmicks, and I am an easily impressed person myself. After all, this was a company focused on APIs; they offer candidates to apply by API as well. With their own words: “At Plaid, our API is core to everything we do. In the spirit of our culture, we love receiving job applications as calls to our API.”

![Apply by API](/assets/postarkanme/plaid.png)

This gave me an idea to build a little API as a toy project; so I can express that I love building APIs just like Plaid did. I was planning to display it on my [personal page](https://gokhanarkan.com); therefore, I would open up a possibility for Visa to acquire me for \$5.3 billion too. Likewise, it would be such enjoyment for fellow developers to contact me, or maybe I am entirely wrong.

- [ ] **Beginning of a cheap joke, I advise not to read**

> Since I want to EXPRESS my enthusiasm on building APIs, I established to assemble this server with EXPRESS.js.
>
> - Me

- [x] **End of the cheap joke**

The whole application, which its only function is sending an email to my personal inbox, took me 1 hour to develop. I spent the majority of my time on the security aspect of the server, which checks for rate limits, cross-site scripting, HTTP parameter pollution or cors etc. I executed this project on 18th of August; tested, completed and left it in my commit history.

![Commit History](/assets/postarkanme/commit_history.png)

Today, I woke up really early. By early, I mean a lot, like 4 am early. It was freezing. I brewed some coffee and got back to the bed quickly. I turned on my laptop, considering I can use the heat of my computer when I am under it. So I decided to finish/deploy one of the small projects that I didn’t fully finish in the past. Hence I chose this particular one as it felt like an effortless task.

I prepared the README.md file and decorated it with some scripts along with a route to display this information on the main page. The primary reason I added these example scripts so people see that I can code stuff.

# Boring stuff

&rarr; Check the code examples [here](https://post.arkan.me/).

&rarr; Oh, I also created a little demo form because the project was looking dull; it’s [here](https://post.arkan.me/example). It is hard to believe that I made this page within a single HTML file in 10 minutes, thanks to Vue and TailwindCSS!

![Form](/assets/postarkanme/form.png)

# Real Life Use

I deployed this project to Heroku, though I was lazy and I had some credits about to expire. Later on, I am planning to deploy this to a t2.micro instance along with some other little projects within pm2 clusters.

In the end, it'd be good to have an endpoint for this purpose so that I can connect other static page forms in one place. All in all, it was rather a productive project.

Take care, and have a lovely day/afternoon/evening!
