---
layout: post
title: "ruby's string interpolation: syntactic sugar to the max"
date: 2013-10-29 22:58
comments: true
categories: [ruby, syntactic sugar, string interpolation] 
---
##there ain't no such thing as magic

One thing I've come to realize about programming - despite almost every gut reaction to the contrary - is that there is no magic. None. Period. Fullstop. As Avi would remind us, Ruby (ignoring that whole C thing) can be distilled into three things: objects, methods, and keywords. So when you see some magic, investigate further, keeping in mind those 3 fundamental components of the Ruby language. 

<img src='http://s3.amazonaws.com/rapgenius/filepicker%2FeEF6S8NTRLu4B2LbvkwO_magic_.jpg' width=400 >

But as I've gotten more and more exposure to Ruby's hidden secrets, it's become seemingly more difficult to believe there's no magic going on! This is because Ruby makes use of an idea called ["syntactic sugar"](http://rubylearning.com/satishtalim/ruby_syntactic_sugar.html). Syntactic sugar is basically a group of special programmatic syntax rules that let you write your code in a way that doesn't *appear* to jive with the normal rules of the language, but that is easier to remember how to do and looks better.

##syntactic sugar = delicious but disorienting

Syntactic sugar is therefore a vehicle for abstraction. Instead of adhering only to the native syntax rules of the language, Ruby has embraced natural syntactical shortcuts that appear to break the rules, but actually make your code read more legible and human-friendly. 

For the most part, syntactic sugar is a wonderful thing. (With a name like that, how could it **not** be a good thing?!) But it can also be a trifle dangerous without at least a cursory knowledge of what exactly the sugar is coating. Let's look at a specific example: string interpolation in Ruby. 

```ruby
def interpolate_two_variables(num1, num2)
  puts num1.class
  puts num2.class
  "String interpolation allows us to magically include variables, like #{num1} and #{num2}!"
end
interpolate_two_variables(num1, num2)
Fixnum
Fixnum
=> "String interpolation allows us to magically include variables, like 6 and 23!"
``` 
Sweet, thanks Ruby! I use string interpolation just about every day coding in Ruby - it's an incredibly powerful and convenient way to group different objects together in the context of, say, a view page in a web app. But... what's really going on here? I don't see anything like Ruby's normal ```object.method_call``` syntax, and yet it feels like there MUST be a method call being invoked inside the double quotes. What exactly is the interpreter converting the ```#{}``` syntax into?

## #{}: more than just a pretty face

Assuredly, there's nothing magical going on here. Upon being invoked, our friend ```#{}``` sets to work telling the Ruby interpreter to call the ```to_s``` method on the object inside of itself. In the above example, those objects are ```num1``` and ```num2```. Cool!

## customizing our syntactic sugar: DateTime'z

This story has only just begun, y'all. Since Ruby is so awesome, we can of course (re-)define the ```to_s``` method at the Class level of our choosing. Consider instances of the ```DateTime``` class. I would like for string interpolation of DateTime instances to show up nice and legible - you know, like the way we normally communicate dates and times. 

```ruby
"#{DateTime.now}"
=> "2013-10-30T12:39:02-04:00"
```
Hmm... gross. Parsing that mentally has taken me well over the 1 microsecond that I've allotted for that task. In true Ruby fashion, let's dive into ```DateTime```'s ```to_s``` method and re-write it for the task at hand:

```ruby
class DateTime
  def to_s
    self.strftime("The date is %m/%d/%y, and the time is %r")
    puts "Look! \#{} is just calling DateTime#to_s, which we overwrote to make our DateTime objects easier to read!"
  end
end
"#{DateTime.now}"
# Look! #{} is just calling DateTime#to_s, which we overwrote to make our DateTime objects easier to read!
=> "The date is 10/30/13, and the time is 01:31:15 PM"
```
This example demonstrates that the ```#{}``` is merely syntactic sugar that calls the ```to_s``` method as defined in the receiving object's class.

```ruby
apples = 4
puts "I have #{apples} apples"
puts "I have %s apples" % apples
# I have 4 apples
=> nil
```

## main takeaways

Going forward, I intend to dive head-first into every example of syntactic sugar that I can get my eyeballs on, because the sugar always coats a delicious candy core of Ruby awesomeness.