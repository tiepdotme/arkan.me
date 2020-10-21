---
layout: post
title: "Tiny Express Server for People Who Want to Contact Me in a Non-conventional Way"
date: 2020-10-05 12:00:00 +0100
categories: projects
tags: javascript node
---

[Live Project Link](https://post.arkan.me/){:target="\_blank"} |
[GitHub Repo](https://github.com/gokhj/post.arkan.me/){:target="\_blank"}

There is this company called Plaid I have recently heard because it was all over the news as [Visa acquired it for \$5.3 billion](https://techcrunch.com/2020/01/13/visa-is-acquiring-plaid-for-5-3-billion-2x-its-final-private-valuation/){:target="\_blank"}. I have a fairly eccentric habit that I tend to click on the careers page on every company website I visit. In fact, I browse the software engineering roles and compare the requirements with my skills to catch up with what they want from a candidate these days. It is indeed an excellent way to catch up with the industry, new technologies and such.

So, I visited Plaid’s careers page just like every other company website and what I came across in this one made me fall in love instantly. I adore these kinds of gimmicks, as I am an easily impressed person myself. After all, this was a company focused on APIs; they offer candidates to apply by API as well. With their own words: “At Plaid, our API is core to everything we do. In the spirit of our culture, we love receiving job applications as calls to our API.”

![Apply by API](/assets/postarkanme/plaid.png)

This gave me an idea to build a little API as a toy project; so I can express that I love building APIs just like Plaid did. I was planning to display it on my [personal page](https://gokhanarkan.com){:target="\_blank"}; therefore, I would open up a possibility for Visa to acquire me for \$5.3 billion too. Likewise, it would be such enjoyment for fellow developers to contact me, or maybe I am entirely wrong.

- [ ] **Beginning of a cheap joke, I advise not to read**

> Since I want to EXPRESS my enthusiasm on building APIs, I established to assemble this project with EXPRESS.js.
>
> - Me

- [x] **End of the cheap joke**

The whole application, which its only function is sending an email to my personal inbox, took me 1 hour to develop. I spent the majority of my time on the security aspect of the server, which checks for rate limits, cross-site scripting, HTTP parameter pollution or cors etc. I executed this project on 18th of August; tested, completed and left it in my commit history.

![Commit History](/assets/postarkanme/commit_history.png)

Today, I woke up really early. By early, I mean a lot, like 4 am early. It was freezing. I brewed some coffee and got back to the bed quickly. I turned on my laptop, considering I can use the heat of my computer when I am under it. So I decided to finish/deploy one of the small projects that I didn’t fully finish in the past. Hence I chose this particular one as it felt like an effortless task.

I prepared the README.md file and decorated it with some scripts along with a route to display this information on the main page. The primary reason I added these example scripts so people see that I can code stuff.

# Update as Oct 16, 2020

As of the 16th of October, I realised that this project has some future. Well, maybe not for everyone but at least for me. I implemented two more things as I felt the need for them while I use it. Here is the visualisation of my complex development cycle:

![Cycle](/assets/postarkanme/cycle.png){:style="width: 300px; display: block;"}

I think this is the key essence of building stuff; you build, you use (or find people to use) and find the missing features; then repeat the cycle—screw agile scrum methodologies.

### Subscriber

I created a brand new object called Subscriber along with a model and controller, where I register email addresses for newsletter and other purposes. I did this by storing the origin of the request with the email in the database. So when the time comes, I can quickly get all the subscribers of that domain by a single query like this:

```javascript
// Mongoose query
Object.find({ url });
```

After a bit of more use, I also realised there were duplicate entries, so I made sure to check for duplicates as well.

### Recipient

Secondly, I created another model called Recipient; in this way, this server can be more autonomous, so I can generate domain origins and send the relevant emails to the relevant email addresses. For instance, if I receive the request from `https://post.arkan.me`, the function checks with the database and looks if there is a recipient email that already configured. Hence, I can pick the email address and rather than using one global email where I fuck my inbox; I can distribute the emails to different inboxes.

```javascript
// Get preconfigured recipient data if in the database

const recipient = await Recipient.findOne({ origin: req.headers.origin });
const toEmail = recipient ? recipient.email : process.env.TO_EMAIL;
```

This might be relevant for the cybersecurity aspect too, so maybe in the future, I can further make an implementation only to send emails if the server knows the origin already. However, I need to look up and research if this can be tricked or not. All in all, I believe configuring this on the CORS level would be more efficient and less complex.

# Boring stuff

&rarr; Check the code examples [here](https://post.arkan.me/){:target="\_blank"}.

&rarr; Oh, I also created a little demo form because the project was looking dull; it’s [here](https://post.arkan.me/example){:target="\_blank"}. It is hard to believe that I made this page within a single HTML file in 10 minutes, thanks to Vue and TailwindCSS!

![Form](/assets/postarkanme/form.png)

# Real Life Use

I deployed this project to Heroku, though I was lazy and I had some credits about to expire. Later on, I am planning to deploy to a t2.micro instance along with some other little projects within pm2 clusters.

In the end, it'd be good to have an endpoint for this purpose so that I can connect other static page forms in one place. All around, it was rather a productive project.

Take care, and have a lovely day/afternoon/evening!
