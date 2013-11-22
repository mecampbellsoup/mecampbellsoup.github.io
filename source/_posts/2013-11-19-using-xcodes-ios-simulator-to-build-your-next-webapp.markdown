---
layout: post
title: "using xcode's iOS simulator to build your next webapp"
date: 2013-11-19 20:19
comments: true
categories: [rails, xcode, ios]
---

#programmers need to be empathic

When building a web application, developers need to be empathetic - that is, they need to be really good at putting themselves in their users' shoes. And these days, "users" aren't just sitting in front of a desktop anymore; instead, people take their computers everywhere with them in the form of mobile smartphones. 

Are you in a public place? If so, look around. I mean it - really look around. What do you see? Most likely you can see people with heads lowered playing with their mobile phones.

##3 quick facts to convince you mobile is f*$%ing important
- 91% of all people on earth have a mobile phone
- 56% of people own a smart phone
- **50% of mobile phone users, use mobile as their primary Internet source**

At this point you may be thinking: "True. I should really make sure anything I'm building is intended for use on a smartphone." Kudos to you. And luckily, many of the most popular web development frameworks are way ahead of you and me in responding to the tectonic mobile shift.  [Twitter Bootstrap](http://getbootstrap.com/) and [Zurb Foundation](http://foundation.zurb.com/) - a pair of front-end (mainly CSS) frameworks - are represenative of this shift. Both compete for the title of "most responsive, mobile-friendly framework" and are obsessed with offering frameworks that render beautifully regardless of the size of the computer's screen. 

##...but building mobile-first can be annoying.

With this in mind, in order to be an empathetic developer these days, it's not a bad idea to approach UI/UX with a "mobile-first" mentality. But "testing" your app in a mobile context can be pretty annoying - you need to first constantly be on your phone and using it to experiment with your app (which, if you have an iPhone like me, means goodbye battery); you are always switching back and forth between laptop and mobile device; and finally, you have to either deploy your app earlier than desired or do some extra work to set up [localtunnel](https://github.com/progrium/localtunnel) or [ngrok](https://ngrok.com/) in order to make your localhost server accessible to your external mobile device.

##Enter: iOS Simulator, a part of the Xcode development kit.

[Screenshot of how I use it.](http://cl.ly/image/3r2i2d2P012f)

This little guy solves that laundry list of problems I mentioned earlier:

1. You never have to use your actual iPhone (preserve that precious battery)
1. As a result, no more switching back and forth and using your phone for stuff you'd rather be doing in your natural coding environment
1. No need to deploy early or set up a localtunnel just so you can access your app's development environment from an iPhone

And the good news keeps coming: for those of you who use Alfred (which should be anyone who spends lots of time on their computer), there's a workflow to make opening the iOS Simulator extremely fluid, fast and painless:

[Visual of using the workflow - so simple!](http://cl.ly/image/0o2g0R2N201Z)

[Here's a link](http://www.alfredforum.com/topic/2126-launch-ios-simulator/) to where you can find that workflow as well. 

##goodnight, and good luck

This strategy has saved me lots of time, headache, and - especially - iPhone battery life. But more importantly, using the iOS Simulator has resulted in a more beautiful and responsive web application this time around since I was de facto engaged in the mobile experience from Day 1. (I basically never opened localhost:3000 in my web browser.) I hope you agree:

[TextMeLater](http://bit.ly/textmelater)





