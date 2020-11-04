---
layout: post
title: "Building My Own Backward Compatible Game Library for Xbox Series S|X"
date: 2020-10-31 12:00:00 +0100t
categories: projects
tags: ruby rails node javascript scraping
---

[Live Project Link](https://backwardible.com/){:target="\_blank"} |
[GitHub Repo](https://github.com/gokhj/backwardible/){:target="\_blank"}

### Introduction

I am currently typing this on a flight from Istanbul to London whilst the guy next to me checking on my laptop's screen. In his mind, he is probably thinking I am somewhat a crazy gamer maniac dude that was continually checking Xbox games in a questionable website in the last 2 hours. News flash, I am way worse. I built this!

I have pre-ordered an Xbox Series S in this generation. All my life I have been a PlayStation guy but this time I wanted to have a change. I also missed the PS5 launch, and when I tried to order it was out of stock everywhere already. I am glad that I picked a cheaper and smaller option since I wanted to play older games that I didn't find any time to play in the past.

**Besides, some things taste better when you age: coffee, for instance, or green tea or beer. I am not the same person I was ten years ago or even a couple of days ago. Some books I hate are now my favourites. After rewatching some movies I like in the past, now I love them even more or maybe less. I used to think Don't Speak by No Doubt was overrated, nowadays listening to it destroys me inside. You need some experience and time to digest some things. This is my ultimate lesson from life; don't judge others because you might become them.**

Additionally, nowadays, a big chunk of the games is bloated with DLCs, loot boxes and other purchasable in-game items. Companies are focusing on many things but missing out on a lot of essential factors such as character development, dialogues or music.

I am not going to lie; I played one of the best games of my life in this generation, some of them are:

- God of War
- Breath of the Wild
- Horizon Zero Dawn
- Persona 5
- Super Mario Odyssey
- The Witcher 3
- Outer Wilds (not worlds!)
- Red Dead Redemption 2

And many more.

However, along the way, I struggle to find such gems, and sometimes either I play dull and tasteless games where I repeat the same thing (like Watch Dogs 2) over and over or I face big disappointments such as Fallout 76.

### Ideation

Since I purchased my new console, I wanted to check which games I could play from prior generation; so I googled "Xbox Backward Compatible Games" and [got this page](https://www.xbox.com/en-US/xbox-one/backward-compatibility){:target="\_blank"}.

The Microsoft page was handy. Upon checking which games I could play, I found many titles that I missed in the past. Even though this was a brief and simple page that displays all the games; it was a big hassle to check game scores or what is it about since the page was not giving these details to the users.

I wish there were a better, user-friendly and less-corporate looking page that I can browse, search or sort games and find hidden gems.

Sadly, there was not.

As an automation addict, I wanted to write a quick script (a lot of great products start with this headspace) to scrape all this information and match them with third parties; so I could have a convenient catalogue of games.

I am not a selfish scumbag, so once I acquired this data, I decided to make it open-source with a user-friendly interface. That is how I got the idea of this project.

### Data Collection

I tried to collect the names of the games from the official Xbox page with a Python library called BeautifulSoup; though after spending about two hours, it got overly complicated for me. This task should not have taken more than 20 minutes. So instead of automating the title collection process, I copied the content straight from my web browser. I would have finished the automated script in two more hours, but compared to copying the content within seconds; I found it a waste of time.

This remembered me below tweet.

<div style="display: flex; justify-content: center;">
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Nothing says &quot;I&#39;m a developer&quot; like spending the day automating something that could&#39;ve been done in an hour.</p>&mdash; Saron (@saronyitbarek) <a href="https://twitter.com/saronyitbarek/status/1315123636785893377?ref_src=twsrc%5Etfw">October 11, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
</div>

After I collect the titles, I looked for APIs that I can match the names with services like Metacritic or HowLongToBeat. Sadly, there was no official way of getting this data, but I found some libraries that scrape this information.

I integrated another method to collect this information; sadly, this step required another method to get global names of the titles. For instance, Xbox listed King of Fighters as KOF, so when I search KOF on Metacritic, I did not get the relevant and correct information. [Here is my passive-aggressive commit of maintaining names](https://github.com/gokhj/backwardible/commit/d0c9deed0236be2b2e72ec8cd8d2f3e3271b6638){:target="\_blank"}.

### Tech Choices

I have used Node and Sequelize for getting and storing the information in the first place. There was no specific reason for using these technologies. I like the automation flow of Node.js; that's all.

The inital prototype can be found in [`~/boxlist`](https://github.com/gokhj/backwardible/tree/main/boxlist){:target="\_blank"} folder if you are interested.

After completing and storing all the games in the database, there was only one thing left — a user interface.

I could have easily used React or Vue; querying the data from a Lambda function. However, I realised that I needed a fully-featured backend so data handling can be dynamic and fast. I could use some efficiency such as searching, pagination and search autocompletes. Yes, I agree; the things I mentioned can be quickly done with a single page application frameworks too; but still, I don't need states, complex DOM manipulations, or compositions.

There are so many full-stack frameworks that I could use for this project, which is overwhelming. I could use Flask, Django, Laravel, Nest, Express, Revel, or I could have used React/Vue/Svelte to build and render from the server; or I could use SSR frameworks such as Next.js or Nuxt.js and could go completely insane. But I picked Ruby on Rails.

Why?

##### Turbolinks

Turbolinks, with their terms "makes navigating your web application faster."

This is how it works:

When you follow a link, Turbolinks automatically fetches the page, swaps in its \<body>, and merges its \<head>, all without incurring the cost of a full-page load.

This is definitely the most compelling reason I went with Rails. Turbolinks works considerably responsive, feels like the page remains the same, and only content is changing. But in reality it is a different page!

![Turbolinks](/assets/backwardible/backwardible.gif){:style="display: flex; justify-content: center;"}

##### Searchkick and Elasticsearch

I needed to index the titles for autocompletes on the client-side and for giving quicker search results. In this regard, searchkick makes perfect sense to me. It learns what the users are looking for, so it gets smarter each time when someone searches something. Apart from that, it handles a bunch of things that would be quite useful in the long term.

- stemming - `tomatoes` matches `tomato`
- special characters - `jalapeno` matches `jalapeño`
- extra whitespace - `dishwasher` matches dish `washer`
- misspellings - `zucchini` matches `zucchini`
- custom synonyms - `pop` matches `soda`

Searchkick was battle-tested with Instacart, which gives me an instatrust (you see what I did there?).

The search looks for the keys in the title, description and the developer of the game. However, I wanted to boost some fields, such as titles as they are more relevant to the search. This is working like magic with Searchkick.

```ruby
@games = Game.search(query, fields: %w[title^10 description developer])
```

One line and it is good to go!

##### Kaminari

Kaminari is everything I looked for and more for a pagination module. It works both on the backend and frontend. I can serve paginated data with one line gracefully.

```ruby
@games = Game.page(page).per(14)
```

However, it is not over.

When I come back to the views, I can use the instance variable to navigate and display information without writing any more code.

```ruby
<%= page_entries_info @games %>
```

is turning into this:

![Kaminari](/assets/backwardible/pagination.png){:style="max-width: 300px;"}

##### Mature & Reliable Gems

Like the examples above, there are so many libraries in Rails that became mature & battle-tested. This gives me an ease of mind, and I focus on the development of my prototype. Of course, there are, let's say Django counterparts of what I mentioned above. But with Rails, it is so easy to compile webpack, integrate the Tailwind & PostCSS or purge the unused CSS.

##### Seeding and Database Automation

As far as I know, on the command line level, seeding the database is not that simple compared to Rails. I can just run this command `rails db:seed` and it will run the `seeds.rb` without any more configuration since the application knows what environment and what database it is using by default.

Here is the snippet of what I am doing.

```ruby
require 'csv'
require 'json'

csv_text = File.read('./db/prod_data.csv')
csv = CSV.parse(csv_text)
csv.each do |row|
  genre = JSON.parse(row[4])
  publisher = JSON.parse(row[8])
  also_available = JSON.parse(row[10])
  Game.create(title: row[1], release_date: row[2],
              description: row[3], genre: genre,
              image: row[5], score: row[6].to_i, developer: row[7],
              publisher: publisher, rating: row[9], also_available: also_available)
end
```

In the future, I can manage it as an automated task to scrape, patch and update the database, which is impressive.

##### Easy to protoype

Writing a controller is so elegant; instance, variables and return layouts make everything awkwardly easy. Here is the index of the home controller, for example.

```ruby
  def index
    page = params[:page].presence || '1'
    @games = Game.page(page).per(14)
    render_404 if @games.current_page > @games.total_pages
    @nav = 'home'
    @page_range = helpers.get_page_range(@games)
  end
```

##### Lack of Ruby in the blog

I like the Ruby syntax and the community. I think it is the best out there. Also, I haven't published anything with Ruby / Rails before in the blog, so a change would be much appreciated. A change just like Xbox for me.

### Aftermath & What's Next?

It is incredible what you can build within hours with the help of open-source. Some modules and libraries require years of work, and as a developer, I can use that knowledge with a single click. Thanks to all open-source contributors out there, even a tiny bit makes a huge difference.

The initial product, backwardible, is currently serving backward compatible games as it can be seen from its self-explanatory name. I would love to improve this and make game pass/ea play additions one day. Honestly, it really depends on if people would use it or not!

Happy hacking!
