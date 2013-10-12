---
layout: post
title: "what exactly is between those pipes?"
date: 2013-10-11 22:43
comments: true
categories: [ruby, flatiron school, local variables, blocks]
---
So here's a question - how would you define the thing between the "pipes" (these guys ```||``` ) in the following code examples:	

{% codeblock [lang:ruby] [simple blocks in ruby] %}
wrecking_ball = Song.new
wrecking_ball.tap { |song| song.name = "Wrecking Ball" }
{% endcodeblock %}

In other words, what's the ```|song|```? No, seriously - what is it?! Multiple choice:
<ul>
<li> (a) method</li>
<li> (b) keyword</li>
<li> (c) variable</li> 
</ul>

If you guessed C then you were correct. But that's kind of weird… typically we come across variables during assignment. Something like this is way more familiar when we hear "variable":

 ```string = "Hi! I'm a string. What a cool class…"```

To be more specific, that thing between the pipes that we see in every single block (for the most part) is actually a <em>local</em> variable. For the next step, let's see exactly why they come in handy.