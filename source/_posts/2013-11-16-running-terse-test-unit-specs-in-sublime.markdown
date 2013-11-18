---
layout: post
title: "running simple Test::Unit specs in sublime"
date: 2013-11-16 11:08
comments: true
categories: [tdd, ruby, codewars, testunit]
---
#Setting the Stage: Codewars

[Codewars](www.codewars.com/r/LqvZzw) is this awesome site where you can learn Ruby or JS by training on "katas" and climbing the ranks of code warriors on the site. The more elegant and efficient your solution to increasingly difficult challenges, the more honor you incur. 

I've been working through a few on the Ruby side of things, and interestingly enough the Codewars team has built in to the platform [their own testing DSL](http://www.codewars.com/docs/kata-test-framework). It's sort of a mut: they've borrowed all the best elements of Ruby's most popular frameworks, including RSpec, T::U and MiniTest. The result: for every langauga kata set on Codewars, there is a Test class/prototype (I say prototype because of JavaScript's unique twist on object-orientation and inheritance). Users can then easily design their own tests as instances of this parent class by invoking very natural-sounding methods like 'expect' (from RSpec), 'assert_equal' (from T::U), and so on. 

##the path to enlightenment was winding

Now while that sounds straightforward enough, I must confess it took me a solid couple of hours to realize that Codewars was not using a native Ruby test framework, but rather was utilizing their own testing API (and not just using some built-in T::U syntactic sugar or something):

```ruby
# Create your own tests here. These are some of the methods available:
#  Test.expect(boolean, [optional] message) 
#  Test.assert_equals(actual, expected, [optional] message)
#  Test.assert_not_equals(actual, expected, [optional] message) 
```
[Here's the docs for the Ruby Test methods](http://www.codewars.com/docs/ruby-test-reference) that are provided by Codewars' API. Pretty fancy, huh?

##simpler route: use RSpec, even for miniscule test suites

While I appreciate the work the Codewars guys have done on this Test module, I don't think learning yet another testing DSL (even though it's only slightly different from RSpec) makes any sense. I'm actually a little confused as to why they would implement their own test framework in lieu of one of the already-accepted and ubiquitous frameworks like RSpec. 

I prefer to do as close to 100% of my code-writing in Sublime as possible (practice makes perfect, ya' know?)



