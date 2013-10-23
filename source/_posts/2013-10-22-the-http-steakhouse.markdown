---
layout: post
title: "the HTTPrime steakhouse: breaking down the request-response cycle using lots of metaphors"
date: 2013-10-22 23:34
comments: true
categories: ["ruby", "http", "web requests", "mvc", "web app"]
---
###why must an http request be so confusing?

Look, we've all been there. When first setting out to learn about the Great Internets, one of the first themes you'll come across is that of the HTTP request. You'll quickly learn that HTTP stands for "Hypertext Transfer Protocol". And after mulling that over a bit, your next thought might be: "Cool, that wasn't so hard!" But soon, someone starts throwing new ideas and concepts your way - using words like "server", "web application", "controllers", "get" and "post". And before you know it, you're swimming in a sea of disconnected semantic meaning. Bummer.

As it turns out, the Internet is a giant, teeming and conscious (no?) mass of computers which pass long strings of text back and forth, all the time. Now, for the next part of this blog post, I could show you a picture like the following and begin to dissect it, piece by piece:

![Alt text](http://www.jeevanchaaya.com/techvibe/wp-content/uploads/2008/10/http-request-response-model.png)

But I'm not going to subject you to that, dear reader. Today, during an impromptu mid-day comedic improvisation seminar at school (yes, Flatiron School is awesome, we know), we heard about the importance of explaining big, complicated ideas by making comparisons to things that are already understood. (That is to say, we should use more metaphors and analogies.) So instead of telling you everything there is to know about an HTTP request/response cycle, I'm going to offer an intentionally over-simplified story for you wrapped in a familiar metaphorical context: that of a dining experience at a steakhouse. 

Note: I'll be taking a tad of creative license in the coming paragraphs; turn back now if this frightens you or if you can't bear the idea of a steakhouse experience being in any way misrepresented.

###HTTPrime Steakhouse

####customers are *clients*, and the maître d' is the *server*

First, let's set the stage. At our restaurant - the HTTPrime Steakhouse - each hopeful customer represents what's called a *client*. The client carries with it a very valuable thing: knowledge of what it wants! Much in the way that you enter a restaurant with knowledge of what you expect to order, an HTTP client brings that same information to a web application's server. In the HTTP world, all clients' requests arrive at something called a *server*, which is akin to the gatekeeping maître d' at our steakhouse. Our maître d' is responsible for intercepting potential diners, having a quick, formal chat with them to understand what they desire, and handing their request off in the appropriate manner (typically to the restaurant itself at that point, the part where customers sit and dine). I say "formal" chat because, in fact, the server is only able to respond with a particular type of response - integer values communicating whether the client's request was valid, erroneous, or misguided. Think of how a real-life maître d' feels a bit stiff when you're talking with him...

It turns out, this is very similar to the role a web server plays - the server stands at the entrance to the web application (that is, the restaurant itself), has a quick chat with incoming customers (that is, incoming HTTP requests made by clients), and passes the customers with their requests along to the waitstaff and the restaurant itself (that is, sends the HTTP request and its information along to the web application itself). 

####GET vs. POST

"But what's all this jazz I keep hearing about GET and POST requests, huh?" Great, great question. You, the client (or customer at HTTPrime, for our purposes) can actually have two types of conversations with our maître d':

1. Client: "Hello sir! Could we please be seated, take a look at the menu, and **GET** some of your delicious filet mignon?"
2. Client: "Hello sir! I have a delicious filet mignon recipe to give to HTTPrime. Here you go. Maybe you could **POST** it up in your kitchen somewhere… that way your restaurant will have it available for all future customers."

A GET request is akin to walking up to the maître d' and telling him you would like a filet mignon. A POST request is akin to walk up to the maître d' and telling him you have an amazing recipe for filet mignon, and you'd like to give it to the restaurant. The maître d' then simply responds to your request by telling you whether the restaurant is prepared to handle your request. 

###mvc... wtf?

![Alt text](http://www.digital-web.com/extras/restful_css/mvc-by-a-web-app-designer.png)

####waiters are your application's controllers...

You just wrapped up your chat with our faithful maître d'. You've explained you are here to GET (should I stop capitalizing, have I driven that point home yet?) one of HTTPrime's famous filet mignons. What happens next: magically, out of nowhere seemingly, he summons HTTPrime's filet-mignon-dedicated who ushers you to your table. Let's call this waiter boy Rodrigo, because I think that's a cool name. In web applications, controllers take on the function of a restaurant's wait staff: they don't do any of the cooking, nor the cleaning up. All they do is take orders from customers, relay those orders to the kitchen (where the real magic happens), and return to the customer's side to fulfill their original request. I don't mean to trivialize the role of a good waiter - after all, bad service is almost as bad as Armageddon itself - so you can see why lightning fast, responsive controllers are a must. 

####and the kitchen represents your application's models

Loyal, kind, loving Rodrigo - what a big heart he has - has just confirmed your order to GET (wink, wink) a 3-pound, "rare plus" filet mignon. Where's he off to next? He's going to *control* that order straight over to the kitchen, of course! And as alluded to, this is where the real magic happens. In the kitchen, raw ingredients, spices, and some black magic come together to create HTTPrime's signature dishes - the ones that people keep comin' back for. In a web application, the models gather raw data from the database (or other data repository), sprinkle in their own sugar/spice/black magic combination (we call this "logic", generally speaking), and - voilà! - pass off a perfectly cooked, delicious filet mignon for Rodrigo to satisfy you, the client's, picky demands. 

####but wait, it goes deeper - down to the database

But where do these raw materials come from, exactly? Well, in HTTPrime, the raw ingredients used by the kitchen would need to come from at least a couple places. (Good news: more metaphors coming!)

Every time the chef walks down to the basement where all those massive refrigerators chock full of dead animal are kept, he's essentially accessing our web application's database. Now, some fancy stuff happens in the database. And frankly, our kitchen chefs don't understand a lick about butchering cows, packaging the meats, and selling the raw steaks to artery-clogging steakhouse chains like ours (i.e. they don't speak SQL). But that's okay, because that isn't their job.

There's another way to get foodstuffs into our restaurant. If our steak database is missing some important ingredient - say, cream for the creamed spinach (my mouth is watering) - our team of chefs can go out into the world and grab the missing item from, well, anywhere. We call this "scraping", and it's an incredibly handy tool that web developers use for getting data wherever it may be presented on the Internets. For our purposes, just keep in mind that scraping is just another way to get raw ingredients and cooking supplies into our kitchen. 

####...and the filet's presentation (you know, garnish and stuff) is like the application's views

Faithful Rodrigo has just received your freshly prepared filet from HTTPrime's kitchen, and he's heading back to where you're seated in the restaurant. But there's one problem: you, the client, are _extraordinarily_ picky. In fact, you won't even look at a filet mignon that you've ordered unless it's plated, garnished, and has a cherry on top. So what's Rodrigo to do? Well, he makes a quick pitstop at one of those watier stations they have at fancy restaurants like HTTPrime and dresses up your filet. Literally. He puts the food-equivalent of fancy clothing on your cow. And this is essentially the exact same function carried out by the views in our web application. Their job is to receive, via our controllers, an object (the important stuff) created by our models and make it extremely presentable. Because, after all, presentation is key - here's proof:

![Alt text](http://4.bp.blogspot.com/-JVqw7uNjBIA/UT8iKeNvjQI/AAAAAAAAAGw/XLEljNq22Xo/s1600/best-food-presentation8.jpg)

###wrapping it up











