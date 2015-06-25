---
layout: post
title: "Bitcoin Ticker: Building a Status Bar Widget in Mac OS"
date: 2015-06-08 17:52:55 -0400
published: false
draft: true
comments: true
categories: objective-c "mac os"
---
## (tl;dr)<a name="tldr"></a>
The source code for my bitcoin price tracker can be found [here]().

## intro
I like to pay fairly close attention to the price of [Bitcoins](http://www.coindesk.com/information/what-is-bitcoin/) on a daily basis. I'm of the firm opinion that Bitcoin has kicked off a cryptocurrency revolution that will shake the foundations of the digital world *writ large*. There are some pretty provocative write-ups, particularly [this one](http://startupboy.com/2014/04/01/the-fifth-protocol/), on the implications of Bitcoin as a trustless, distributed, and decentralized network that facilitates instantaneous transfer of value. Importantly, Bitcoin faclilitates this network in a way that is [Byzantine fault-tolerant](https://bitcointalk.org/oldSiteFiles/byzantine.html), which allows the network to propagate through time without the need of some central authority giving a stamp of approval for any & all transactions among the network's peers.

For some reason, since Mac OS Yosemite came out earlier this year, my usual Bitcoin ticker has been having an identity crisis:

![](http://cl.ly/image/0J2X1d3x2A0E/Image%202015-06-08%20at%206.29.07%20PM.png)

(Notice "down" where the exchange rate for USD/BTC should be. Sad.)

I saw this as a great opportunity to build my own Mac OS price ticker, since along the way I'd learn more about Bitcoin and Objective-C (which has intrigued me since learning that some of its central concepts, i.e. sending messages, were [inspired by Alan Kay via Smalltalk](http://mythz.servicestack.net/blog/2013/02/27/the-deep-insights-of-alan-kay/)).

## Getting Started

Here's what I've built so far:

![](http://cl.ly/image/2V2x3q1H043t/Image%202015-06-16%20at%207.31.44%20PM.png)

Cool, right?! It refreshes the price feed every 120 seconds, but that's certainly customizable. Later on I'd like to add a menu item to allow the user to set the refresh interval explicitly... that will have to be blog post round 2 though.

I'll break down the iterations of the code base to generate this status bar widget, explaining each step of the way what was added and how it works.

### Step 1: Create the Xcode project

This part is pretty straightforward. I'm assuming you already have [Xcode](https://developer.apple.com/xcode/downloads/) installed. Open the application, and select "Create a new Xcode project":

![](http://cl.ly/image/3Y0N0O3U3N3T/Image%202015-06-25%20at%2011.46.10%20AM.png)

Next, select "Cocoa Application" from within the list of OS X project types:

![](http://cl.ly/image/0p2B080d3g32/Image%202015-06-25%20at%2011.49.30%20AM.png)

On the next page you are presented with some configuration options. These aren't salient so I'll move on.

After selecting a location on disk to store your new Xcode project, you can start writing code - we'll put everything in the `AppDelegate.h` and `AppDelegate.m` files since the codebase will be puny.

### Step 2: Initialize the `NSStatusItem`


### Step 3: Write a function that makes an HTTP request to fetch the BTC price
### Step 4: Inject the requested price data into the status bar item
### Step 5: Figure out how to loop steps `3` and `4` above

## Wrapping up (a.k.a. one interesting thing I learned about Objective-C)
