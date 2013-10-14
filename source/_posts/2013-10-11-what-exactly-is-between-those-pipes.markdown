---
layout: post
title: "what exactly is between those pipes?"
date: 2013-10-11 22:43
comments: true
categories: [ruby, flatiron school, local variables, blocks]
---
### <strong>"I've seen these pipe things before… a bunch of times." (The thought you just had.)</strong>

They look super familiar. Why does Ruby make use of them? What's inside the pipes?!

Well in short, Ruby reads the thing between the pipes as a local variable to be used in the subsequent block. Let's look at an example of an iterator (followed by a block) from the Ruby docs. 

```ruby
@names.each do |name|
  puts "Hello #{name}!"	
end
```
To paraphrase the docs: ```each``` is a method that accepts a block of code then runs that block of code for every element in a list, and the bit between ```do``` and ```end``` is just such a block. <em>The thing between pipe characters is the parameter for this block.</em>

What happens here is that for every entry in a list (i.e. ```@names``` in this example), ```name``` is bound to that list element, and then the expression ```puts "Hello #{name}!"``` is run with that ```name```.

(Thought tangent: So this example actually points to another cool feature of Ruby - "duck typing". When we see ```@names.each```, we naturally expect ```@names``` to be an Array (or Hash) and we'll be iterating over each element. But not so fast! What happens if ```@names``` is actually something totally different - like, say, an object from a custom-built class called Names? Warning: this is a highly contrived exmaple…)

```ruby
class Name
  attr_accessor :string
  def initialize(string)
    @string = string
  end
  def each
    self.string
  end
end

@names = Name.new("woe, this is weird - I'm a psuedo string attr within a Name object which responds to #each?!")
@names.each do |name|
  puts "Hello #{name}"
end
=> "woe, this is weird - I'm a psuedo string within a Name object that responds to #each?!"
```
(Um, pretty awesome, right? Ruby doesn't care about what you expect it to do; it only cares it the object at hand responds to the method being called. Talk about an egalitarian language!)

Now that we've had an intro-<em>duck</em>-tion to blocks and pipes, here's a question - how would you <em>define</em> the thing between the "pipes" (these guys: ```||``` ) in the following code example:	

```ruby
wrecking_ball = Song.new
wrecking_ball.tap { |song| song.name = "Wrecking Ball" }
```
(Another mental tangent: I think pretty much every object responds to ```#tap``` - that will have to be for another blog post though.)

To put the question differently, what's the ```|song|```? No, seriously - what is it?! Multiple choice:
<ul>
<li> (a) method</li>
<li> (b) keyword</li>
<li> (c) variable</li> 
</ul>

If you guessed C then you were spot on - pat yourself on the back! To be more specific, that thing between the pipes that we see in almost every block is actually a <em>local</em> variable. This is Ruby's approach to getting the object receiving the method call (e.g. the ```wrecking_ball``` Song object receiving the ```tap``` method) inside the method as an argument. The convo with Ruby (since we all have dialogues with our computers in the language of Ruby these days) goes down something like this:
<blockquote>
"Ok lord programmer, I realize you're calling a method on this object - and you're hoping to ```yield``` the object receiving the method call to the method's block (the code following the ```.each do ``` bit). Tell ya' what: in order to make sure said object gets from the left side of this expression into the yielded block, I'm going to temporarily make a local variable - you can even name it WHATEVER YOU WANT - so that you have full access to the object within the block. Sound good? You bet your ass it does. I'm Ruby, we're gonna' get along famously."
</blockquote>


### Putting down the pipe (only temorarily, calm down)

Awesome. Now we know exactly what's going on between those pipes. Ruby is creating a local variable that points to the object receiving the method call, all so that we can fully access our object inside the method's block. Thanks Ruby.

To drive this point home, let's remove these pipe thingies and see if we can still code like we normally do. 

```ruby
some_numbers = [5,6,7,8]
some_numbers.collect do
  puts "This is our block!"
end

"This is our block!"
"This is our block!"
"This is our block!"
"This is our block!"
=> [nil,nil,nil,nil]
```

OK, so that's pretty interesting. It looks like our block is definitely being evaluated, and our collect iterator is yielding to that block precisely ```self.size``` times (or 4 in this case). Since we have a ```puts``` method, whose return value is always ```nil```, we end up with the returned array of ```nil```'s above. But I want access to the ```[5,6,7,8]``` on which we're calling ```.collect```! Let's try another strategy:

```ruby
some_numbers.collect do
  p some_numbers
end
[5, 6, 7, 8]
[5, 6, 7, 8]
[5, 6, 7, 8]
[5, 6, 7, 8]
 => [[5, 6, 7, 8], [5, 6, 7, 8], [5, 6, 7, 8], [5, 6, 7, 8]]
```
Alright, getting closer. Looks like we still have access to the entire object receiving the ```.collect``` method call. How can we get to one element of ```some_numbers``` at a time though? In other words, how can we get specific, granular access to each component of the object at hand, the one receiving the method call? How can we have our block be evaluated on each element of ```some_numbers```, but only one element at a time? The answer lies in Ruby's implementation of Array#each (and really all the iterators) - here are implementations we've written <a href="https://twitter.com/FlatironSchool"/>@flatironschool</a>:

```ruby
class Array
  def each
    i = 0
    while i < self.length
      yield(self[i])
      i += 1
    end
    self
  end
  def my_each_with_index
    i = 0
    while i < self.length
      yield(self[i], i)
      i += 1
    end
    self
  end
end
some_numbers = [5,6,7,8]
some_numbers.my_each_with_index do |x, i|
	puts "At index #{i} the value is #{x}"
end
At index 0 the value is 5
At index 1 the value is 6
At index 2 the value is 7
At index 3 the value is 8
=> [5, 6, 7, 8]
```
Lines 5 and 13, there's our old pal ```yield```. How cool it is to finally see the distinction between ```.each``` and ```.each_with_index``` - it's merely the # of local variables from ```self``` (the object, in our case the ```Array [5,6,7,8]``` receiving the method call) that we "yield" or pass along to the block! So now we having a working definition of 'what's betweeen the pipes': they are simply <em><strong>local variables which represent each element of ```self``` that get passed to the block each time our method call ```yield```s to the blockf</strong></em>! It's a bit of a mouthful, but now that we understand it makese sense to us. 

In my next post, I want to talk about Ruby's bindings - a poorly understood (and rarely needed) aspect of Ruby that I hope to shed some light on. 

Until then, work hard/be nice! 