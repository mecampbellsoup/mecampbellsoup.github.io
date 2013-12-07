---
layout: post
title: "how to: use binding.pry more effectively within enumerables"
date: 2013-12-07 16:02
comments: true
categories: [ruby, debugging, pry]
---
# what is pry?

When I first discovered the [Pry gem](https://github.com/pry/pry), I felt like I'd won the lottery. At long last I had found a gem library that enabled me to simply insert a line of code - ```binding.pry``` - anywhere into my codebase that would drop me into an IRB session at that exact point at runtime. It sort of feels like air dropping a team of Ruby SEALs into enemy lines in order to eliminate those dreaded bugs. 

## using pry: best practice

Let's talk about a quick example of using Pry in a Ruby file. 

Let's say you have an array of 10^6 elements, and you'd like to iterate through each one and call a fancy method on it:

BAD USAGE OF PRY:

```ruby
(1..10**6).each do |i|
  collatz = Collatz.new(i)
  collatz_length = collatz.length
  biggest = collatz if collatz_length > biggest_length
  binding.pry
end
```
In this case, I was trying to solve [Project Euler problem #14](http://projecteuler.net/problem=14), the Collatz sequence. At some point, I wanted to refactor my ```Collatz#length``` method since it was taking **FOREVER** to get through all one-million items in the array to find the largest sequence.

Man do I love Ruby. By adding just a few words of code, my bad (read: ineffective) use of Pry becomes extremely effective:

GOOD USAGE OF PRY:

```ruby
(1..10**6).each do |i|
  collatz = Collatz.new(i)
  collatz_length = collatz.length
  biggest = collatz if collatz_length > biggest_length
  binding.pry if i == 100 || i == 1000
end
```
By adding in this conditional Pry debugger, I'm able to ensure that - as I'm iterating through a gigantic array of a million items - I'm getting the expected ```Collatz``` class behavior along the way at or around the one-hundredth & one-thousandth elements, respectively.

## conclusion

While this "pro tip" may have been fairly obvious to many of you Rubyists from the get-go, it didn't occur to me that I could treat ```binding.pry``` just like any other method call in Ruby... allowing me to wrap that method call into some conditional logic at runtime.

Thanks for reading!