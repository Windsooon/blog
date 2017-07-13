##How I build my MVP in two weeks

Since I built my last MVP [Unicooo](https://unicooo.com/) using more than a year. Spending lots of time to add features I **Guess** users will need.So when I come up with the idea [YouTueb classified](http://www.youtubeclassified.com/) and looking at my todo side-project list which contain more than 20 ideas.

I just wanna deploy it base on the standerd by Harry Percival

>>Is our site usable? Is it better than nothing? Can we make lists on it? Yes, yes, yes.

[Testing Deployment Using a Staging Site](http://www.obeythetestinggoat.com/book/chapter_manual_deployment.html)

So if your answer is yes either, deploy it now. Forget about the bugs.


###Come up with the idea

I live in China, so it's not easy to use YouTube since the [Great Firewall](https://en.wikipedia.org/wiki/Great_Firewall). And when I finally start using it a year ago, I found it hard to find channels I need. I'm interested in Tech and Science. What I can do at first is 

- search the keyword **science youtube channels recommendation** on google
- click all the websites show in first page. 
- Build a **channels set** in my mind because many of them keep mentioning about some hosest channels like [minutephysics](https://www.youtube.com/user/minutephysics) and [SmarterEveryDay](https://www.youtube.com/user/destinws2) which are awesome of course.

So I do a little research about youtube channel classified, I found
which also classfied the youtube channels. I think their application is for data analyse instead of normal youtube users, and they also miss some important categories like Product Review, Unboxings. I totally understand why, when you wanna classfied lots channels, you will just want to limit to 20-30 category because it's impossible to correctly classfied all the channels in subcategory.So the categories will be 100% correct but useless.

How about YouTube itself? When I search [basketball](https://www.youtube.com/channel/UCkcRYjXjAhsBPVH4aeIPxmQ), YouTube will recommend relative channels in the bottom.


[Basketball]()



But when I search [game](https://www.youtube.com/channel/UCOpNcN46UbXVtpKMrmU4Abg)

[Gaming]()

YouTube will not recommend relate channels. 

Another point is if I going to work at USA, I wanna know more about its culture, but all my subscribed channels are chinese.So [YouTueb classified](http://www.youtubeclassified.com/) may help. Find your main users is very important.

By now, my base idea is classified some hotest channels for the categories that  users want. like Computer under Sciencs, Make up under Beauty&Fashion, Tech Product under Reviews. Business model? I guess once we get big, we can recommend some channel for ad, Because there are so many new channels wanna standout. Now I start programing.

###Host your code
Usually I open source all my code in Github. Like [Unicooo](https://unicooo.com/) on [Github](https://github.com/Windsooon/Unicooo-django). Not just because it's free, **I also think Code should be open soure by default**. But this time I choose to use a private one just for pratice, and I'm really to open source. [Bitbucket](https://bitbucket.org/product) is great and offer Unlimited private repos. I only got one problem when I using it. I misset my ssh key on the repo instead of my personal page

[repo]

[personal]

###Run your code
I choose [pythonanywhere](http://pythonanywhere.com/), you can use AWS or Digitalocean or whatever you like. But I find pythonanywhere is super easy to set up and their customer service is quite and helpful. Most importantis, you don't have to worry about the nginx settings or other stuff.

###Signing certificates 
[Let's encrypt](https://letsencrypt.org/) with no doubt, and [dehydrated](https://github.com/lukas2511/dehydrated) is great tool to set it up.

###Choose your Domain
Since I focus on youtube users, I use something start of youtube. So when you type youbute on the broswer, the broswer will recommend the website.

[broswer]()

I choose Godaddy for domain hosting. I searched the coupon for buying a .com domain, At first, it work well, the price get 90% off. But when I pay for it, I get payment error. I ask the godaddy guys for help, They said the coupon is invalid. So I gave up and pay about $8 for youtubeclassified for a year.(I can get better price when I pay two years together, but I'm not sure this business idea work, so I choose just pay for one year.)

###Domain Email hosting
There are a lot of Email hosting on web, I only found [Zoho Mail](https://www.zoho.com/mail/) have a free plan, So I choose it. After some easy setup it works.

###Add Google analytics
For people who haven't use google analytics before. It will let you know which page you user focus and where are they come form like this
[page]
[location]

Remember to filter your own IP in settings.

###Manager Email subscribe
I use [mailchimp](https://mailchimp.com/) to manager subscribe users, They have great api docs, great customer service as well as a free plan.My use case is When user subscribe, we also get the page url to identify which category he is interesd. At first I get lost at group and segment, after some research I found out what I have to do is.  

- Send email and url to my backend.
- Create group use the group api [Create the group](https://developer.mailchimp.com/documentation/mailchimp/reference/lists/interest-categories/interests/), (If the group alreally exist, it will return a 400, but just ignore it)
- Loop over the groups by group name inside your interest-category to get the group id. 
- [Add the email to this group](https://developer.mailchimp.com/documentation/mailchimp/reference/lists/members/).


###Coding
I started coding at 2017-06-28, Before that, I build a [repos](https://github.com/Windsooon/awesome-youtube-category) in Github then submit it on hackernews to attract hackers help classfied together. It didn't get a lot attention, But you can use Github and hackernews for help, there is so many awesome-xxx right now.

As you can see, the application is dead simple, just use youtube api to get some data then display. as I said, I choose to make it simple.At first, I used [django-hackathon](https://github.com/Windsooon/django-hackathon) which for hackathon, I just use some hours for the backend, (Thanks for awesome django and django restframework).Of course I know I have to spend a lot of time on frontend to make it look better. I tried full page ajax which gives users better experience. But I found it hard to maintain when the application have more and more logic. So I delete that part of code then rewriteit.


###Business model
After I finish my first version, I immediately send to my friends and ask for suggestion. I asked them 

- Anything confuce when you're using it.
- Any business model advice.

Both of them are not programmer,and both of them give me excellent advise. Philip said I should add a subcategory. Before it, When you click Science, it will list all subjects like Math, Computer, Physics. I fixed it within a day. Julian said why not let users choose categories they love. These will let user come back. I didn't choose to implement it right away because is too complicated and I not sure other users would need this. 

I also think what we can have but YouTube don't, I have thought add story about youtuber. How they build their channel, how to maintain. It's fun to their followers and useful to new youtuber. But finally I choose to add recommend website at the bottom of subcategory. People interested in Math will also interest in Science American website and other science blogs. I don't have time to complete it yet.So I let user submit website they recommend.I also ask my workmate to use the application in front of me. So I could know which part will confuced him. Where he will try to click. Does he know how to search the subcategory. After that, I change some button styles and some remove some href make the application better. I also learn from my cofounder Jessica, She also gave me some good advise about how to navigate, how to sort the channels. Actully, what they asked is very reasonable, and I don't know why I missed when I programing. I just focus the structure of code instead of what user really need. So, listen to your friends before you spend a lot of time in a new feature. They may not hackers, but they are real users and they will spend their time on your MVP.

Now we just recommend the hotest channels more than 100k subscribes. But I realized we also stand in the youtubers's shoes. What if I'm a youtuber, What can the application can do for me. There're so many channels already, a new youtuber can't find a way to standout. So my next step is add new channels in it.

###Marketing

I find a list to submit my applicaation

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

####Others

1. [Tech website](https://medium.com/@PitchMantra/100-best-places-to-promote-your-startup-6ad7a52d79c0)
2. [Tech website2](https://triphappy.com/blog/131-startup-directories-to-promote-your-startup/1)
3. [Tech website3](http://www.inboundio.com/blog/27-websites-to-submit-your-startup/)

####Quora and blog
We answer question on Quora and post comments on blogs about youtube channel. We carefully choose the channels that really useful to the users and other comments didn't mention.


###Feed back
Once again, hackernews guys don't pay any attentaion on my application, Actually, I know why. At first, it's not geek enough, just use YouTube api then classfied some channels. Second, it's not fun and not impressed. Third, As a tool, I don't need it. You know what, I think so, Before I build the first version, I had thought to classfied the channels by the its title and description use machine learning. Because I just learn something about machine learning and I wanna have a try. And I also thought of build a iOS application instead of a website for the same reason. 

But this remind me of a post on [Ask HN: Who is hiring? (May 2017)](https://news.ycombinator.com/item?id=14238005) the comment from xando

>Hey, a friendly reminder. I’m parsing the thread, all job offers added here are also available on the map on
https://whoishiring.io or just HN items https://whoishiring.io/search/36.0440/-90.8984/4?source=hn
If you post here, please use the below format to help me with parsing. If you won’t, no worries, I will do my best to get all the things right.

This guy build https://whoishiring.io and want more people submit their jobs there. Instead using machine learning or geek algorithm to scrapy the data and classified it, he just asked people for help. So I think, Is my purpose to build this application is to show others I'm geek or for users to really use it. And what if I spend a lot of time in machine learning then people don't like this idea at all.So I just control myself and get the shit done.

As Paul Graham write in hacker and painter ( I can't find the the original because I read the chinese version.)

People think hacker design everything before coding. But they are not, they just coding->thinking->change->coding.

###What is next
Build four side projects in a month. Happy coding.
