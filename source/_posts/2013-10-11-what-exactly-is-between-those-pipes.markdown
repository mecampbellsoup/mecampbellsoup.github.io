---
layout: post
title: "what exactly is between those pipes?"
date: 2013-10-11 22:43
comments: true
categories: [ruby, flatiron school, local variables, blocks]
---
### <strong>"I've seen these pipe things before… a bunch of times." (The thought you just had.)</strong>

They look super familiar. What do they do? What's going on between them?

Let's look at an example of an iterator (followed by a block) from the Ruby docs. 

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

If you guessed C then you were spot on - pat yourself on the back! To be more specific, that thing between the pipes that we see in almost every block is actually a <em>local</em> variable. This is Ruby's approach to getting the object receiving the method call (e.g. the ```wrecking_ball``` Song object receiving the ```tap``` method) inside the method as an argument. The convo with Ruby goes down something like this:
<blockquote>
"Ok lord programmer, I realize you're calling a method on this object - and you're hoping to ```yield``` the object receiving the method call to the method's block (the code following the ```.each do ``` bit). Tell ya' what: in order to make sure said object gets from the left side of this expression into the yielded block, I'm going to temporarily make a local variable - you can call it WHATEVER YOU WANT - so that you have full access to the object within the block. Sound good? You bet your ass it does. I'm Ruby, we're gonna' get along famously."
</blockquote>


## Putting down the pipe (only temorarily, calm down)

Awesome. Now we know exactly what's going on between those pipes. Ruby is creating a local variable that points to the object receiving the method call, all so that we can access our object with impunity inside the method's block. Thanks Ruby.

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
Alright, getting closer. Looks like we still have access to the entire object receiving the ```.collect``` method call. How can we get to one element of ```some_numbers``` at a time though? How can we have our block be evaluated on each element of ```some_numbers```, but only one element at a time? The answer lies in Ruby's implementation of Array#each (and really all the iterators):

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
end
```

```ruby
def this_method_takes_a_block
  yield(5)
end

this_method_takes_a_block do |num|
  puts num
end

5
 => nil
```
The main takeaway: our pipe friends allow us to pass a local variable as an argument to a given block. 