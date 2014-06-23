---
layout: post
title: "to throw-catch, or to begin-rescue... that is the question"
date: 2014-06-23 00:11:31 -0400
comments: true
published: false
categories: 
---
```ruby
def thrown_here
  if true
    throw :foo, "Hello"
  end
end

def caught_here
  caught = catch(:foo) {
    thrown_here
  }

  p "#{caught}, world!"
end
```
