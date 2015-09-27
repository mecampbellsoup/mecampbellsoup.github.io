---
layout: post
title: "How Fixed Gear Bicycling Taught Me About Dependency Injection"
date: 2015-09-27 12:17:14 -0400
comments: true
categories: oop, ruby, dependency injection
published: true
---

I became inspired to write this blog post after watching a great [@sandimetz](https://www.twitter.com/sandimetz) [talk which she gave at RailsConf 2015](https://www.youtube.com/watch?v=OMPfEXIlTVE). Her subject was primarily the [Null Object Pattern](https://en.wikipedia.org/wiki/Null_Object_pattern), but she extends the fundamental principle - that objects, not conditionals nor excruciatingly tailored classes, should be used to encapsulate your system's behaviors - to the practical aspects of injecting dependencies required by your domain's fundamental objects. Let's take a look at what I mean by using a real-world example: my recently acquired (and already banged up - I've had two flat tires already!) [fixed gear (a.k.a "fixie") bike](https://en.wikipedia.org/wiki/Fixed-gear_bicycle). I want you to start asking yourself: if you were given the task of modeling in Ruby a fixed-gear bicycle alongside a "normal" or freewheel bicycle, what tools would you reach for - inheritance, composition, or otherwise?

## Modeling our `Bicycle`

Let's throw down a little code to start bringing our bicycle domain to life:

```ruby
class Bicycle
  attr_reader :frame_size, :color

  def initialize(frame_size, color)
    @frame_size, @color = frame_size, color
  end

  def number_of_speeds
    freewheel ? gear_count : 1
  end

  def gear_count
    1
  end

  def freewheel
    true
  end
end

```
Cool - now we can instantiate a new `Bicycle` with our frame size and color preferences. Moreover, we have a sensible default (at least for urban NYC bicycling) for our drivetrain set to single-speed and [freewheel](https://en.wikipedia.org/wiki/Freewheel). (Note that 'freewheel' is distinct from fixed-gear. On a fixie, for example, it is not possible for the chain to disengage from the crank arms, i.e. the motion of the rider's pedals. On a freewheel bicycle, you can stop the motion of the pedals beneath you and the drivetrain will continue turning, allowing for what we call 'coasting'.)

This is all well and good - until you find yourself moving to Brooklyn. In Brooklyn, fixed gear bikes are more popular, and it's probably got something to do with a hipster resurgence. 

So now let's use our knowledge of OO and Ruby to model our fixed gear bicycle.

## Reach for inheritance first...?
Here is a perfectly viable implementation of our fixie bicycle variant:

```ruby
class Fixie < Bicycle
  def freewheel
    false
  end
end
```
Easy! All we did was inherit from `Bicycle`, and modify the `#freewheel` method to instantiate a non-freewheel single-speed bicycle. 

Now let's say, however, that your roommate would prefer a multi-speed bicycle. Once again using inheritance we may write some code like so:

```ruby
class MultiGear < Bicycle
  def gear_count
    @gear_count ||= 18
  end
end
```
And once again, this has solved our problem. But, while effective, this solution raises a number of concerns. Mainly, we already have two distinct classes in order to account for two slight variations in `Bicycle` types. What do you imagine will happen to our system as the number of different variations grows? Put differently:

* Do we really need distinct, unique classes to model a single variation in the characteristics of our bicycle? 
* Does the organization of our code and the patterns therein easily communicate the distinctions we're attempting to convey?
* Are we satisfied with the idea that, should our `Bicycle`s change in other aspects in the future, we'll continue to open new classes?

## Where inheritance breaks down (pun intended)

To focus on the last concern that we just raised, let's say you've accepted a job as a delivery boy for a local Chinese restaurant. Most of those guys, since they're riding for hours a day, enhance their drivetrain with an electric motor which looks something like this:

![](https://www.electricbike.com/wp-content/uploads/2012/06/ego-kits.jpg)

You've been asked to allow your system to model this new `Bicycle` variant. Perhaps you could reach for inheritance once again and end up with something like this:

```ruby
class ElectricMotorAugmented < Bicycle
  def drivetrain
    :electric_motor
  end
end
```
This may meet the needs of a relatively basic system, but let's say your boss asks you to run a report...

> Boss: "Hey you, new gal! Get me a breakdown of all the bicycles in New York City with an approximation of their top speeds. We are examining the relationship between bicycle accidents and their drivetrain mechanism - we've been hearing a lot lately about these electric-motor-augmented bicycles getting into accidents at faster speeds than non-electric bicycles."

Since your boss seems to be asking for data concerning the relationship between bicycles' drivetrain mechanism and their approximate 'top speed', you set out to run the report with some code like this:

```ruby
Bicycle.all.map { |bike| [ bike.class.name, bike.top_speed ] }
```
Now, you need to re-open all of your classes and implement `#top_speed`:

```ruby
class Bicycle
  def top_speed
    # in units of MPH
    20
  end
end

class MultiGear < Bicycle
  def top_speed
    25
  end
end

class ElectricMotorAugmented < Bicycle
  def top_speed
    30
  end
end
```
Gheesh, that was kind of a lot of work - we had to open 3 different classes to find a home for our top speed approximations. You can see that our pattern - which is built on top of inheritance - could certainly become unwieldy and difficult to maintain as our system grows.

## A better pattern: inject your dependencies!

I think this pattern really speaks for itself, so I'll let the code do most of the talking here. Instead of inheritance, if we reached for dependency injection our system may have turned our more like this:

```ruby
class Drivetrain
  attr_reader :freewheel, :gear_count, :electric_motor_augmented

  # NOTE: we're using Ruby 2.1+ required keyword argument syntax here
  # https://robots.thoughtbot.com/ruby-2-keyword-arguments
  def initialize(gear_count:, freewheel:, electric_motor_augmented: false)
    @gear_count, @freewheel, @electric_motor_augmented =
      gear_count, freewheel, electric_motor_augmented
  end

  def top_speed
    speed =  (gear_count > 1) ? 25 : 20
    speed += electric_motor_augmented ? 10 : 0
  end
end

class Bicycle
  attr_reader :frame_size, :color, :drivetrain

  def initialize(frame_size, color, drivetrain = Drivetrain.new)
    @frame_size, @color, @drivetrain =
      frame_size, color, drivetrain
  end

  def gear_count
    drivetrain.gear_count
  end

  def top_speed
    drivetrain.top_speed
  end

  def number_of_speeds
    drivetrain.freewheel ? drivetrain.gear_count : 1
  end
end
```

Now we can easily instantiate our various `Bicycle` types and get `#top_speed` data from them:

```ruby
fixie        = Bicycle.new("57 cm", "black", Drivetrain.new(gear_count: 1,  freewheel: false))
single_speed = Bicycle.new("57 cm", "black", Drivetrain.new(gear_count: 1,  freewheel: true))
multi_speed  = Bicycle.new("57 cm", "black", Drivetrain.new(gear_count: 18, freewheel: true))
motorized    = Bicycle.new("57 cm", "black", Drivetrain.new(gear_count: 18, freewheel: true, electric_motor_augmented: true))

fixie.top_speed        #=> 20
single_speed.top_speed #=> 20
multi_speed.top_speed  #=> 25
motorized.top_speed    #=> 35
```

## "Isolate what is different"

By isolating in our mind what was different among our bicycle variations, we were able to extract it out into its own `Drivetrain` dependency. The real benefit of doing this is that we can *inject* this dependency into our `Bicycle` instances as we need! **No more sublcassing `Bicycle` endlessly as variation after variation of bike requires modeling in our system.** You can envision this pattern of dependency injection coming in handy as your system grows and different attributes of `Bicycle` start to vary. Have you seen the foldable, transportable frame style of bikes?

![](http://blog.tradetang.com/wp-content/uploads/2009/11/folding-bike.jpg)

Using dependency injection, we can account for this variable attribute pretty succinctly:

```ruby
class Frame
  attr_reader :color, :size, :foldable

  def initialize(color:, size:, foldable: false)
    @color, @size, @foldable = color, size, foldable
  end
end

class Bicycle
  attr_reader :frame, :drivetrain

  def initialize(frame = Frame.new, drivetrain = Drivetrain.new)
    @frame, @drivetrain = frame, drivetrain
  end

  # rest of code omitted for brevity...
end
```

And here is our foldable `Bicycle`:

```ruby
foldable = Bicycle.new(
             Frame.new("black", "57 cm", true),
             Drivetrain.new(gear_count: 1,  freewheel: false)
)
```

## Conclusion

Watching Sandi's talk (and writing up this post) have certainly changed my opinion on using inheritance versus injecting dependencies into my domain model. I was inspired to write this post by Sandi and the joy I've been getting from riding fixie around Brooklyn for the past couple of months.

I hope you've found this blog post interesting and educational. Please let me know in the comments below!