---
layout: post
title:  "Tube Line Status: Check Transport for London status from Amazon Echo"
date:   2019-10-27 12:00:00 +0100
categories: projects
tags: python lambda aws
---
[Live Project Link](https://alexa.amazon.co.uk/spa/index.html#skills/dp/B081LWRNNN){:target="_blank"} |
[GitHub Repo](https://github.com/gokhj/alexa-tube-line-status){:target="_blank"}

The code might be a bit messy; it's my first try on Alexa development. Haven't read anything on it yet, except watching a 5 mins video tutorial on YouTube.

I added some inputs to intents with some relevant information. The machine learning understands the central line when the user asks "red line" for instance.

Standard SDK has been used and functions built on top of it. Below there are some examples on the current dialogue model.

![general dialogue](https://camo.githubusercontent.com/f2c3db0759a4ee281dbee2a365fd620871db945f/68747470733a2f2f692e706f7374696d672e63632f676a32527758466d2f696d6167652d3237352e706e67)
![general dialogue 2](https://camo.githubusercontent.com/c611094edb581439058d2530c1a3908608d57c23/68747470733a2f2f692e706f7374696d672e63632f627276684b38626d2f696d6167652d3237362e706e67)