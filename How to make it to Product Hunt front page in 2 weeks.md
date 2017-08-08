---
title: How to make it to Product Hunt front page in 2 weeks 
date: 2017-07-16 20:00:00
tags: mvp django side-project
---


After spending more than one year developing features I thought users might need on my last MVP, [Unicooo](https://unicooo.com/). I picked the idea of [YouTube Classified](https://www.channelshunt.com/) from my side project to-do list and planned to finish it in two weeks. 

Update: 

![product](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/product.png)

We are on the Product Hunt front page today! Thanks guys!

### Coming Up With The Idea

Since I live in China, the Great Firewall blocks the easy access to YouTube that the rest of the world takes for granted. When I finally started to use it about a year ago, I found it hard to find the tech and science related channels that I needed. What I did at first was:

- Searching the keyword “Science YouTube Channels Recommendation” on Google.
- Clicking all the websites displayed on the first page.
- Building a channel set on my mind because many of them repeatedly recommend the most popular channels such as [minutephysics](https://www.youtube.com/user/minutephysics) and [SmarterEveryDay](https://www.youtube.com/user/destinws2) which are awesome indeed.

What the heck.

To make things easier, I also did a little research on “YouTube Channels Classified,” and found [socialblade](https://socialblade.com/youtube/top/100/mostsubscribed). SocialBlade classifies some YouTube channels, but instead of targeting toward the regular YouTube users, the application is for data analysis, where important categories like Product Review and Unboxing are nowhere to be seen. I totally understand. When there are simply too many channels on YouTube, the easiest way is to limit to 20-30 categories because it’s impossible to correctly classify all the channels under the subcategories. Therefore, those categories are 100% correct, but unfortunately useless. 

How about YouTube itself? Doesn’t YouTube put channels into categories? When I search [basketball](https://www.youtube.com/channel/UCkcRYjXjAhsBPVH4aeIPxmQ), YouTube does recommend related channels at the bottom. 

![Basketball](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/basketball_channel.png)

However, when I search [game](https://www.youtube.com/channel/UCOpNcN46UbXVtpKMrmU4Abg), YouTube does not recommend related channels at the bottom, which is very confusing.

![Gaming](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/game_channel.png)


Finding the main users is very important, and in this case of [YouTube Classified](https://www.channelshunt.com/), we have a very broad user range. Not only can it help English speaking users, people who want to get to know another culture can also make great use of it. For example, if I go to the United States, I would like to know more about its culture, but all of my currently subscribed channels are Chinese, so YouTube Classified may help me by letting me explore popular American channels I’m interested in. 

For now, my main idea is to classify some of the most popular channels into categories and subcategories, such as Computer under Science, Make-Up under Beauty&Fashion, and Tech Products (Reviews) under Reviews. What about our business model? I think once we grow big, we can advertise some channels because a lot of new channels want to stand out in the massive crowd. Now, I’ll move on to start programming. 

### Business model

I think we should have some features that YouTube doesn’t have, and I thought of adding stories about YouTubers, on how they built and maintain their channels. It’s fun to their followers and useful for new YouTubers. But in the end, I chose to just add recommended websites at the bottom of each subcategory. People interested in math are likely to be also interested in science related websites and blogs. I don’t have time to implement it yet, so I simply let users submit websites they recommend. 
For now, we just recommend the hottest channels with more than 100k subscribers. But I realized that we should also be standing in the YouTubers’ shoes and think of them. If I were a YouTubers, what can this application do for me? There are so many popular channels already, and it’s very hard for a new YouTuber to stand out, therefore my next step goes to adding newer channels.


### Version Control

I usually open source my entire code on Github, just like [Unicooo-django](https://github.com/Windsooon/Unicooo-django). Not only because it’s free, but also because I think the code, in general, should be open source by default.(Now [youtube-category](https://bitbucket.org/WindsonYang/youtube-category) is open source), 
[Bitbucket](https://bitbucket.org/product) is great and offers unlimited private repos. My only problem when using it is that I have to set my ssh key to my personal page instead of the repo. 

You should not set your ssh key here.
![repo](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/bitbucket_ca.png)

Instead, add to your personal page.

![personal](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/bitbucket_personal.png?raw=true)

### Hosting the code

I compared among [PythonAnywhere](https://www.pythonanywhere.com/), [Heroku](https://www.heroku.com/), [AWS](https://aws.amazon.com/), and [DigitalOcean](https://www.digitalocean.com/).

- PythonAnywhere costs less than Heroku. [Difference between PythonAnywhere and Heroku](https://blog.pythonanywhere.com/65/)
- AWS is not easy for the beginners, and it may take quite a long time to set up. If you would like to know more about AWS, this guide may help.[Amazon Web Services — a practical guide](https://github.com/open-guides/og-aws)
- DigitalOcean is great, and it also has some useful [blogs](https://blog.digitalocean.com/)

I choose PythonAnywhere for at least these four reasons.

- Super easy to set up and you can usually deploy it in 10 minutes.
- Offers a [free plan](https://www.pythonanywhere.com/pricing/)
- Customer service is quite helpful(Usually reply under two hours). 
- The author of Test-Driven-Development with Python, Harry Percival, used to work here. I learned a lot from this book. Thanks, Harry Percival.


### Coding

I started coding on June 28th, 2017. Before that, I built a [repo](https://github.com/Windsooon/awesome-youtube-category) in Github and submitted it on HackerNews to attract hackers to help classify together. It didn’t get much attention, but Github and HackerNews are still great places to ask for help and to find resources. There are so many awesome-xxx going on right now. 

As you can see, the application is indeed simple. Use YouTube API to get some data and then display it. At first, I used [django-hackathon](https://github.com/Windsooon/django-hackathon) which is for Hackathon, and I spent some hours on building the backend (a shout-out to the awesome Diango and Diango Rest FrameWork). I tried full page Ajax that gives users a better experience, but I find it hard to run when the application begins to have more logic, so I deleted that part of the code and rewrote it. Remember, delete the useless code as early as possible.

I followed [Web Design in 4 minutes](http://jgthms.com/web-design-in-4-minutes/) for the basic design. Then spend some time on responsive design. I use [pure](https://purecss.io/) as my front end framework, it works well except for the font size. If you're also not familiar with it, check these excellent articles [How to make your typography responsive](http://www.creativebloq.com/css3/handling-typography-responsive-design-4122794) and [Everything I know about Responsive Web Typography](https://zellwk.com/blog/responsive-typography/). Basically, what you have to do is to use vw: 

    @media only screen and (max-width: 767px) {
        .font-class {
            font-size: 6vw;
        }
    }


### Signing certificates

[Let's encrypt](https://letsencrypt.org/) and use [dehydrated](https://github.com/lukas2511/dehydrated) as a great tool to set it up. Be aware that every time after you re-apply the certificate, you have to notify PythonAnywhere people for updating.


### Domain


Since YouTube Classified is based on YouTube and that I want to attract YouTube users as our users, I titled something that starts with YouTube, so that when the users type in YouTube on the browser, it will show the website. 

![browser](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/broswer.png)

After picking Godaddy as the domain hosting, I searched the coupon for buying a “.com” domain. At first, the 90% off coupon looked great, but when I was paying for it, I received a payment error. When I asked people who work at Godaddy for help, they told me that the coupon was no longer valid, so I gave up the idea of coupon and paid about $8 for “youtubeclassified.com” for a year. I can get a better pricing if I pay for two years at a time, but since I’m not sure if this business idea will work, I just paid for one year. 

#### Update 
Don't use a domain name that contain other company's trademark. Or you may be caught in a legal dispute and change domain is a nightmare.

### Professional Email 

There are a lot of business email hosting on the internet, but [Zoho mail](https://www.zoho.com/mail/) is the only one I found with a free plan, so that’s my pick. It worked after some easy setup. 

### Uptime Monitor
I use [UptimeRobot](https://uptimerobot.com/), it gives you 50 monitors and every check for 5 minutes for free.

### Adding Google analytics

For people who don’t know what google analytics is, it’s a tool that shows you which page your users focus on and what regions the users are from.

![page](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/category_view.png)
![location](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/location.png)

Remember to filter out your own IP address in the setting.

### Email Subscription Management

I use [MailChimp](https://mailchimp.com/) to manage subscription from users. It has great API docs, great customer service, as well as a free plan. When a user subscribes under a certain subcategory, I get a page URL to identify with the sub category page that the user is interested in. At first, I got lost at group and segment, but after some research, I found out what I have to do:

- Send email and URL to my backend.
- Use the group API to [Create the group](https://developer.mailchimp.com/documentation/mailchimp/reference/lists/interest-categories/interests/). (If the group already exists, it will return a 400, but you can just ignore it.)
- Loop over the groups by group name inside your interested subcategory to get the group ID.
- [Add the email to this group](https://developer.mailchimp.com/documentation/mailchimp/reference/lists/members/)

![ui](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/how%20I%20build%20the%20MVP/mail.png)

### Suggestion from Users

After finishing my first version, I immediately sent the link to my friends and asked for their suggestion. Specifically, I asked them:

- Any confusion when using it?
- Any business model idea?

None of them are programmers, but both of them gave me excellent advice. Philip suggested giving each subcategory an individual page. Before that idea was given, under a certain category page, it lists all the subcategories on the same page, such as Math, Computer, and Physics under the Science category. I changed it within a day. Julian suggested to let users choose the categories they love so that they will be more likely to come back. It’s a great idea, but I didn’t implement it right away because it’s too complicated and I wasn’t sure if many users would need this feature. I also let my workmate use the website in front of me so that I could know which part confuses him. I paid attention to where he clicked, and that if he knew how to search for subcategories. After that, I changed some button styles and removed some href to beautify the website. I also learned from my co-founder, Jessica. She gave me some good advice on navigation and sorting of the channels. 

To be honest, what they suggested are quite useful and obviously, and I don’t know why I missed these ideas when programming. I focused more on the structure of the code instead of what the users really need. Thus, I’d say listen to your friends before you spend a lot of time on a new feature, because they may not be hackers, but they represent the real users who will be spending time on your MVP.


### Marketing

I made a list to submit and advertise my application:

1. [hackernews](https://news.ycombinator.com/)
2. [producthunts](https://www.producthunt.com/)
3. [Wired](https://www.wired.com/)
4. [Betalist](https://betalist.com/)
5. [TheNextWeb](https://thenextweb.com)
6. [lifehacker](https://lifehacker.com/)
7. [reddit/startup](https://www.reddit.com/r/startups/)
8. [reddit/youtube](https://www.reddit.com/r/youtube/)
9. [reddit/Entrepreneur](https://www.reddit.com/r/Entrepreneur/)
10. [reddit/SideProject](https://www.reddit.com/r/SideProject/)

#### Others

1. [Tech websites](https://medium.com/@PitchMantra/100-best-places-to-promote-your-startup-6ad7a52d79c0)
2. [Tech websites](https://triphappy.com/blog/131-startup-directories-to-promote-your-startup/1)
3. [Tech websites](http://www.inboundio.com/blog/27-websites-to-submit-your-startup/)
4. Quora and other blogs

We answer questions on Quora and post comments on blogs about YouTube channel recommendation, as ways to advertise our application. We carefully choose the really useful channels that other comments didn’t mention. Then, I wrote this article to share my experience on how to build this MVP.


### Hi hackers

Once again, HackerNews guys didn’t pay any attention to my application, but I understand. Firstly, it’s not geeky enough to just use YouTube API and then classify some channels. Secondly, it’s not impressive. Thirdly, as a tool, it’s not that useful. I think so too. Before building the first version, I thought about classifying the channels by their titles and description using Naive Bayes, a tool that I have recently learned about and wanted to give it a try. I also thought of building an iOS application instead of a website for the same reason. 

This reminds me of a comment made by “xando” under the post on [Ask HN: Who is hiring? (May 2017)](https://news.ycombinator.com/item?id=14238005)

>Hey, a friendly reminder. I’m parsing the thread, all job offers added here are also available on the map on
https://whoishiring.io or just HN items https://whoishiring.io/search/36.0440/-90.8984/4?source=hn
>If you post here, please use the below format to help me with parsing. If you won’t, no worries, I will do my best to get all the things right.

      1) {company} | {job title} | {locations} | {attrs: ONSITE, REMOTE, INTERNS, VISA, SALARY, company-url}
      Google | Software Developer | SF | VISA https://google.com
      DuckDuckGo | Software Developer | Paoli PA | REMOTE, VISA, SALARY:100k-120k
      Facebook | Web-developer | Zurich | SALARY:120k CHF 
      Google | Site Reliability Engineer | London | SALARY:120k GBP, VISA, REMOTE
or
  
      2) {company} | {job title} | {location}
      Google | Site Reliability Engineer | Sydney
      Facebook | Web-developer | Zurich
      
>I’m using this regex to test the first line, you can test it here https://regex101.com/r/relwQD/3

      \s*(?P<company>[^|]+?)\s*\|\s*(?P<title>[^|]+?)\s*\|\s*(?P<locations>[^|]+?)\s*(?:\|\s*(?P<attrs>.+))?$
>Check bellow for the SALARY regex.

      SALARY:(?P<salary_min>\d+(?:k|K)?)(?:\s*\-\s*(?P<salary_max>\d+(?:k|K)?)?)?(?:\s?(?P<currency>[A-Z]{3}))?     
>and you can test it as well https://regex101.com/r/SRWkMz/2/
—— xando, AskHN

This guy built https://whoishiring.io and wants more people to submit their jobs there. Instead of using machine learning or algorithm to crawl the data and format it, he just simply asked people format their comments! So I think, my purpose is to build this application for YouTube users to really make use of it, so how can I spend so much time on machine learning risking the outcome that people don’t like the idea at all? So, I just control myself and try to get this shit done. 
At this point, I just simply want to evaluate it base on Harry Percival’s three standards on [Test-Driven-Development with Python](http://www.obeythetestinggoat.com/book/chapter_manual_deployment.html): 

>Is the site usable? Is it better than nothing? Can we make lists on it?

The rule of thumb is, if “yes” is the only answer, then don’t mind the bugs and deploy it now. Fortunately, the idea of YouTube Classified passed. 

As Paul Graham once written in Hacker and Painter (I can’t find the original quote because I read the Chinese version):
“ People think hackers design everything before coding. But they don’t. They are just: coding->thinking->change->coding.”

### What is next

Hackathon in a month: Build four side projects in a month. Happy coding.

