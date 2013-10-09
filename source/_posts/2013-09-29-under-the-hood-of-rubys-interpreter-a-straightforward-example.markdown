---
layout: post
title: "under the hood of ruby's interpreter: a straightforward example"
date: 2013-09-29 16:49
comments: true
categories: [flatiron school, ruby, interpreter]
---
Flatiron, Day 4:

First, some things Avi said today:
<blockquote>

Iteration is about going over a collection of objects. Enumeration is the same thing but you’re given the objects.<br/>

object.method… the dot is called dot notation. It’s how we send messages to objects.<br/>

.. (read: ‘dot dot’) is a literal constructor for Range.<br/>

You need to keep “rf -rf /” handy. You know, in case of those Terminator situations.<br/>

Comptuer languages fall into 2 categories: those designed for human problems, and those designed for computer problems. JavaScript was designed to be read quickly by web browsers (i.e. a computer problem).<br/>

Computers will get faster, but people won’t get any smarter.
</blockquote>

Now, on to the post subject... let's walk stepwise through a (very) simple program in Ruby:

Step 0:

	x = 1
	if x == 1
    	puts "x is equal to 1!"
	end
Step 1:

	if 1 == 1
    	puts "x is equal to 1!"
	end
Step 2:

	if true
    	puts "x is equal to 1!"
	end
Step 3:

	puts "x is equal to 1!"  #=> "x is equal to 1!"
	
FizzBuzz ultimate solution:

	def fizzbuzz (i)
    	[("fizz" if i % 3 == 0), ("buzz" if i % 5 == 0)].join
	end
case vs. if

	if x == 1
    	puts "x wins"
	elsif o == 1
    	puts "o wins"
	end

case object  # only can check if condition applies to one object

when condition # condition cannot be a logical operand; must use if/end if you want a unique logic applied for each "case"