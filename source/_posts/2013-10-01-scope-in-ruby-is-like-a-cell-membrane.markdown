---
layout: post
title: "scope in ruby is like a cell membrane"
date: 2013-10-01 00:03
comments: true
categories: [scope, ruby, flatiron school]
---
###How is program scope like a cell membrane?

Scope is the context within which something is defined. Let's focus on methods, for starters. If you write a page of code with a method in it, alongside a bunch of other stuff (variable declarations, enumerators/iterators not contained in that method, etc.), don't expect your method to have any clue what's going on above or below it in that page. Sorry, that explanation sucked - let me show an example:

	some_numbers = [1,2,3,4,5]
	another_variable = {:cell_membrane => "cytosol", :receptor => "ligand"}
	more_variables = some_numbers.collect do |num|
		num * 2
	end
	
	def method_in_same_code_file(args)
		more_variables + some_numbers ##=> This method doesn't know what these are!! 
	end
	
The way that we get around this issue of restrictive method scope in Ruby's design is actually something we all are quite familiar with: <em>passing arguments to our methods.</em> Perhaps you can imagine now the similarity to a cell membrane - take a look at the image below. I would sa the receptor-ligand binding is a lot like doing something like this:

	<page of code omitted… let's just call the method>
	method_in_same_code_file(some_numbers, more_variables)
	
In this method call, our arguments works just like our receptor-ligand binding! Put differently: the arguments function as the cell membrane's receptors, in that only by going through them can stuff on the outside be itelligible/readable to the method's insides. 

Method scope visualization!

<img src="http://fwcdscience.wikispaces.com/file/view/transduction.gif/43381569/transduction.gif">

###cool things <a href="http://en.wikipedia.org/wiki/Kent_Beck">Kent Beck</a> has said (about programming):

One line methods are there to communicate.

You don't spend three or four lines expressing iteration, you spend one word.

YAGNI: You aren't going to need it. 

Optimism is an occupational hazard of programming; feedback is the treatment.

(Speaking about his Chrysler experience…) By far the dominant reason for not releasing sooner was a reluctance to trade the dream of success for the reality of feedback.

<strong>Make it work, make it right, make it fast.</strong>

###how to write a basic test/spec:

	def assert_equal(actual, expected)
		unless actual == expected
			puts "fail: expected #{expected} but got #{actual}"
		end
	end
	
Not so bad, right?