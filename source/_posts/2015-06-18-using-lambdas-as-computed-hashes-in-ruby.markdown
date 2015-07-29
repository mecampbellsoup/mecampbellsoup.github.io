---
layout: post
title: "Using Lambdas as Computed Hashes in Ruby"
date: 2015-06-18 11:22:00 -0400
comments: true
categories: ruby lambda hash
---
I recently read a [blurgh post](http://blog.honeybadger.io/using-lambdas-in-ruby) about the interesting, quirky aspects of lambdas in Ruby.

One feature that stood out to me was lambdas' ability to stand in where hashes would normally be used. 

This functionality is made possible because, in Ruby, lambdas can be called in any of the following ways:

```ruby
l = lambda { |x| puts x }

l.call("Foo") => "Foo"
l.("Foo")     => "Foo" (admittedly this syntax is bizarre to me...)
l["Foo"]      => "Foo" (looks like hash access using the typical Hash#[] method...)
```

The third way is the bridge between lambdas and the concept of "computed hashes". I searched for a definition of computed hash, but didn't find much consensus. The working definition for this post would be something like:

> A hash object whose values can be initialized (read: computed) at runtime based on logic declared elsewhere in the program.


## Putting It Together: An Example
When might the use of computed hashes, i.e. lambdas, be a favorable replacement to a normal hash?

Let's say you're writing tests for your program and you want to add a degree of ["fuzz testing"](https://en.wikipedia.org/wiki/Fuzz_testing). As an example, perhaps one of your classes is initialized with `first_name` and `last_name` attributes (note the `initialize` method expects to receive a `Hash`-like argument as input in sticking with Rails convention), and then generates a `slug` to be used for query string parameters elsewhere in your application:

```ruby
class Person
  attr_reader :first_name, :last_name

  def initialize(hash_like_object = {})
    @first_name = hash_like_object[:first_name]
    @last_name  = hash_like_object[:last_name]
  end

  def slug
    @slug ||= "#{first_name.downcase[0, 3]}-#{last_name.downcase[0, 3]}"
  end
end
```
Now let's generate an instance of the `Person` class to make sure everything looks OK:

```bash
ruby-2.2.2-p95 (main):0 > matt = Person.new(first_name: "Matt", last_name: "Campbell")
#<Person:0x007fca00179bd0 @first_name="Matt", @last_name="Campbell">
ruby-2.2.2-p95 (main):0 > matt.slug
"mat-cam"
```
This checks out. Our `slug` method is pretty dumb, but let's say it becomes more complex: we amend `slug` to handle duplicates. As it stands, "Arthur MacCormack" and "Art MacNulty" will have the same slug and so are not uniquely identifiable by their slug. 

The point of interest here is NOT the logic you end up implementing to make `slug` more unique. What's of interest is how you can fuzz test whatever logic you end up implementing throughout your test suite.

### Faker + Computed Hash = Fuzz Testing

[Faker](https://github.com/stympy/faker) is a great library for generating random data, which I most typically use in conjunction with [FactoryGirl](https://github.com/thoughtbot/factory_girl) to generate instances of my models (that is, the Ruby classes that represent the domain I'm modelling in the application).

Let's see how we can utilize a computed hash to improve the degree of fuzz testing in my unit tests:

```ruby
require 'faker'

# Here is the Person class definition again for reference.

class Person
  attr_reader :first_name, :last_name

  def initialize(hash_like_object = {})
    # The next two lines work because our hash-like-object, in some cases a lambda,
    # can be called using the same [] syntax as Hash#[]
    @first_name = hash_like_object[:first_name]
    @last_name  = hash_like_object[:last_name]
  end

  def slug
    @slug ||= "#{first_name.downcase[0, 3]}-#{last_name.downcase[0, 3]}"
  end
end
```

```ruby
# Construct our computed hash lambda...

randomizer = lambda { |k| Faker::Name.send(k) }
```
And, voilà, we can initialize `Person` instances using our `randomizer` (which is in fact a lambda, and not a hash):

```ruby
ruby-2.2.2-p95 (main):0 > person = Person.new(randomizer)
#<Person:0x007f81f0dfc8b0 @first_name="Nedra", @last_name="Pouros">
ruby-2.2.2-p95 (main):0 > person.first_name
"Nedra"
ruby-2.2.2-p95 (main):0 > person.last_name
"Pouros"
ruby-2.2.2-p95 (main):0 > person.slug
"ned-pou"
```
## tl;dr
Need to generate pseudo-random instances of your classes in order to utilize fuzz testing across your test suite? Try initializing your instances using a computed hash, which in Ruby can be implemented using a lambda and `call`-ing it using the hash accessor `Hash#[]` that you're used to seeing.
