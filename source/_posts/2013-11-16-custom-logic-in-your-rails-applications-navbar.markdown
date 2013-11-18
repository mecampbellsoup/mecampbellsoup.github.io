---
layout: post
title: "custom logic in your rails application's navbar"
date: 2013-11-16 18:01
comments: true
categories: [rails, controllers, views]
---
#the dilemma

Real talk: navbars are fantastic. They're really good at their job, which is providing a constant set of routes to your users to find their way around. No one ever gets lost when there's a good navbar around to help out. 

New Rails projects ship with an application layout (that is, ```application.html.erb``` file) that provides an ideal home for our navbar since we want the bar to always be visible at the top of our app, and the application layour is rendered by all views by default. 

The problem with navbars is that they typically aren't very intelligent. For example, when you're currently viewing a webpage within an application, most of the time the navigation bar does not update to hide that option dynamically. Here's an example of what I mean:


