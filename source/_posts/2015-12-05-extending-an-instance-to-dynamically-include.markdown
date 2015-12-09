---
layout: post
title: "Extend an Instance to Dynamically Include"
date: 2015-12-05 13:52:30 -0500
comments: true
categories: ruby eigenclass extend include module
---

Here's a question for you lovely developers (and non-developers, too!) out there: how can we **dynamically** include behavior from a specific module at runtime? Put differently, is it possible to include one specific module's behavior **after** a `class` definition has already been read in? The typical Ruby include-behavior-from-a-module pattern looks something like this:

```ruby
class Employee
  attr_accessor :can_be_promoted_to_manager
  
  include Trainable
  
  ...
  
  end
end
```

... where `Trainable` might extend the behavior of `Employee` instances something like this:

```ruby
module Trainable
  def train!
    ## some training-related code here...
    self.can_be_promoted_to_manager = true
  end
end
```

This is a contrived example, clearly, but the pattern should look familiar: in Ruby, we love to [encapsulate behavior][1] into modules and `include` them - the name for this practice is [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns). However, since we write the `include` directive within a class-level [scope](https://newcircle.com/bookshelf/ruby_tutorial/scope), we cannot for example use an instance of `Employee` itself to determine which module's behavior we'd like to include. Let me explain with a quick example...

### Different Modules for Different Training

Assume that instead of one `Trainable` module, we really have multiple modules that encapsulate distinct forms of employee training like follows:

```ruby
module ManagerTrainable ; end
module TechnicianTrainable ; end
module ExecutiveTrainable ; end
module SalesPersonTrainable ; end
```

(NB: As you can see, I've omitted any actual methods from these modules above, but use your imagination! e.g. `ExecutiveTrainable#soul_suck!`, or maybe `SalesPersonTrainable#learn_to_peddle!`)

These modules may have also been written to reflect the various real-world types of `Employee` that are modeled in our Ruby application:

```ruby
manager    = Employee.new(type: :manager)
technician = Employee.new(type: :employee)
```

Each of these `employee`s also need to be able to go through some form of "training". So, in order to ensure that all of `Employee` types can be `train!`'ed in the appropriate manner, we can simply `include` all modules at the class level:

```ruby
class Employee
  include ManagerTrainable
  include TechnicianTrainable
  include ExecutiveTrainable
  include SalesPersonTrainable
  
  ...
  
  end
end
```

Unforutnately, this has a nubmer of problems. First, it doesn't communicate the [developer's intent](https://signalvnoise.com/posts/3531-intention-revealing-methods) very clearly. Someone coming along and reading this code in some months' time might ask, "What's going on - why all these variations of `Trainable`?" Second, we risk [overwriting methods](http://ruby-journal.com/how-to-open-slash-override-slash-monkey-patch-a-class-in-ruby/) if the modules share the same/similar interfaces (for example, if each module implements `#train!` independently, you would have to change the method names, or apply logic to the ordering of our 4 `include` directives, or... well, you'd have a real problem on your hands to solve at that point). 

## `extend` at instance-level scope to simulate `include` instead.

Ruby thankfully offers a very elegant, dynamic solution to this problem. Since the logic of which `Trainable` module should be included is a function of the instance's `employee.type`, we can use a lifecycle hook in combination with `extend` like so:

```ruby
class Employee
  attr_accessor :type
  
  def initialize(opts = {})
    # first set the object's attributes as usual
    super
    # extend the appropriate module's behavior to the instance's Eigenclass (more on this shortly)
    extend(Kernel.const_get("#{self.type}Trainable"))
  end
end
```

In [Rails](), you could refactor this by using the provided [object lifecycle hooks](), e.g. [`after_initialize`]() like so:

```ruby
class Employee
  after_initialize :extend_trainable_behavior
  
  ...
  
  private
  
  def extend_trainable_behavior
    # So, for an `employee` whose type is 'manager', we are effectively including `ManagerTrainable`'s behavior only!
    extend(Kernel.const_get("#{self.type}Trainable"))
  end
end
```

## Code Example: add `Human#moniker` behavior with both class-level `include` and instance-level `extend`

In the following code example (where all humans are named `"Matt!"`, ha), you can see how either `include` or `extend` can be used (at different scopes, of course) to tell all `Human`s how to report their `name`. Note that console/IO output is indicated by commented out lines with a single `#` - you'd get the same output by copy-pasting
this code into a live REPL.

```ruby
## Here's our nameable concern. We use both of Ruby's provided hooks - included and extended - to report
## when either event takes place at runtime. 

module Nameable
  def self.included(base)
    puts "#{self} included in #{base}."
  end

  def self.extended(base)
    puts "#{self} extended in #{base}."
  end

  def moniker
    "Matt!"
  end
end

class HumanWithNormalInclude
  include Nameable
end
# Nameable included in HumanWithNormalInclude

class HumanWithInstanceExtend
  def initialize
    super
    extend_behavior
  end

  def extend_behavior
    extend Nameable
  end
end

included_human = HumanWithNormalInclude.new
included_human.moniker
# "Matt!"

extended_human = HumanWithInstanceExtend.new
# Nameable extended in #<HumanWithInstanceExtend:0x007fa939a0da40>.
# <HumanWithInstanceExtend:0x007fa939a0da40>
extended_human.moniker
# "Matt!"
```

## Black magic?! How does it work?!!

To understand why `extend`ing at an instance scope works the way it does, it will help to understand the concept of ["eigenclasses"](http://madebydna.com/all/code/2011/06/24/eigenclasses-demystified.html) in Ruby. First, let's define eigenclass and then write a sort of helper method that tells us any object's eigenclass on demand.

> **Eigenclass**: a dynamically created anonymous class that Ruby inserts into the method lookup path *any time at least one singleton method is added to an object*. (I added the last bit in italics... I think it's right, ha.)

```ruby
class Object 
  def eigenclass 
    class << self  # this opens us to the scope of the receiver's eigenclass
      self         # return the eigenclass itself
    end 
  end 
end
```

To tie the eigenclass concept back into our previous code example, let's try to answer the question: **Where does `extended_human.moniker` actually "live"?**

If we query both of our instances' classes, it's not immediately clear where our `extended_human` instance's `#moniker` method lives...

```ruby
[26] pry(main)> included_human.class.ancestors
[
  [0] HumanWithNormalInclude < Object,
  [1] Nameable,
  [2] Object < BasicObject,
  [3] PP::ObjectMixin,
  [4] Kernel,
  [5] BasicObject
]
[27] pry(main)> extended_human.class.ancestors
[
  [0] HumanWithInstanceExtend < Object,
  [1] Object < BasicObject,
  [2] PP::ObjectMixin,
  [3] Kernel,
  [4] BasicObject
]
```

I typically query an object's class's ancestors when I want to see its inheritance hierarchy (where 'inheritance' is a combination of mixins and classical inheritance, i.e. `class Foo < Bar`) as part of finding where a particular method might have come from. This exercise reminds us that in Ruby, the `Kernel` module (responsible for `eval`, `gets`, `puts`, etc.) is quite high up in the object hierarchy, and as such its methods/behaviors are made available practically everywhere in a Ruby program. 

> **Notably absent from `extended_human.class.ancestors`, however, is any reference to our `Nameable` mixin.**

So - where is `extended_human.moniker` coming from then? Let's instead look through the instances' eigenclasses' ancestors:

## Ruby's method lookup path flows through eigenclasses first!

```ruby
[29] pry(main)> included_human.eigenclass.ancestors
[
  [0] HumanWithNormalInclude < Object,
  [1] Nameable,
  [2] Object < BasicObject,
  [3] PP::ObjectMixin,
  [4] Kernel,
  [5] BasicObject
]
[30] pry(main)> extended_human.eigenclass.ancestors
[
  [0] Nameable,  # Here it is!
  [1] HumanWithInstanceExtend < Object,
  [2] Object < BasicObject,
  [3] PP::ObjectMixin,
  [4] Kernel,
  [5] BasicObject
]
```
Aha - found it! Note that for our `extended_human`, `Nameable` is the very first ancestor in its [method-lookup hierarchy](https://blog.jcoglan.com/2013/05/08/how-ruby-method-dispatch-works/). This is because we `extend`'ed `Nameable` directly into `extended_human`'s eigenclass, as opposed to `include`'ing it in its containing class definition. And once again, to display this point differently:

```ruby
[31] pry(main)> extended_human.eigenclass.ancestors - extended_human.class.ancestors
[
  [0] Nameable
]
```
By making use of an anonymous class under the hood at runtime, Ruby gives us the ability to **dynamically mixin behaviors at all levels of our programs - namely in our example, at both the class and instance level!** Wo-man, I <3 Ruby :)

[1]: https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)