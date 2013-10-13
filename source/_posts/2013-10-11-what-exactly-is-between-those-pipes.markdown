---
layout: post
title: "what exactly is between those pipes?"
date: 2013-10-11 22:43
comments: true
categories: [ruby, flatiron school, local variables, blocks]
---
## I've seen thse pipe things before… a bunch of times.

They look super familiar. So here's a question - how would you define the thing between the "pipes" (these guys: ```||``` ) in the following code examples:	

```ruby
wrecking_ball = Song.new
wrecking_ball.tap { |song| song.name = "Wrecking Ball" }
```


In other words, what's the ```|song|```? No, seriously - what is it?! Multiple choice:
<ul>
<li> (a) method</li>
<li> (b) keyword</li>
<li> (c) variable</li> 
</ul>

If you guessed C then you were spot on. To be more specific, that thing between the pipes that we see in every single block (for the most part) is actually a <em>local</em> variable. But that's kind of weird… typically we come across variables during assignment, and this doesn't feel at all like assignment. Something like the following is way more familiar when we hear "variable":

 ```string = "Hi! I'm a string. What a cool class…"```

That's assignment as I think of it. I'm creating a variable called ```string``` to point to the words, ```"Hi! I'm… etc."``` 

## Life without the pipe(s)

For the next step, let's remove these pipe thingies and see if we can still code like we normally do. 

```ruby
some_numbers = [5,6,7,8]
some_numbers.collect do
  puts "This is our block!"
end

# "This is our block!"
# "This is our block!"
# "This is our block!"
# "This is our block!"
#=> [nil,nil,nil,nil]
```
OK, so that's pretty interesting. It looks like our block is definitely being evaluated, and our collect iterator is yielding to that block precisely ```self.size``` times (or 4 in this case). Since we have a ```puts``` method, whose return value is always ```nil```, we end up with the returned array of ```nil```'s above. But I have a suspicion that these pipes are related to Ruby's ```yield``` method somehow. To jog our memories on exactly where ```yield``` comes into play, let's look at a Ruby implementation of Array#each:

```ruby
class Array

  def my_each
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
```
The main takeaway: our pipe friends allow us to pass a local variable as an argument to a given block. 