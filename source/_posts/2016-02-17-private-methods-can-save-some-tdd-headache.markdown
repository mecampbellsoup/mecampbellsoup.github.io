---
layout: post
title: "Private Methods Can Save Some TDD Headache"
date: 2016-02-17 16:18:58 -0500
comments: true
categories: ruby tdd private
---
Let's face it: TDD is hard. I would argue that there's value in that difficulty - TDD forces you to address domain modeling decisions early on; to declare your API before implementing it. I have spent more time than I care to admit staring at some `*_spec.rb` file, unsure about how to translate the loosely-defined conception of behavior in my brain into test examples.

However, there's good news: relying on private methods can some (some) of this pain.

## A Rails Example

Let's say your co-worker has asked you to write a webhooks microservice. This standalone API will be responsible for:

  1. Creating new callback subscriptions for users that want to subscribe
  1. Sending the callback payloads to said user's callback(s) URL(s)

> 'A crucial part of any callbacks API is delivering, over the web via HTTP POST, the actual callback payload,' you reason aloud.

To that end, you conjure up a vision of some `DeliverCallbackJob` class that needs only a `User` instance and the callback payload (probably a long string of JSON, like a serialized web response). Following a common pattern, you decide the interface for this class will be limited to one method:

```ruby
DeliverCallbackJob#perform!
```

In adherence to TDD, we may start with an example (RSpec DSL here) that looks like this:

```ruby
RSpec.describe DeliverCallbackJob do
  # NOTE: some/much setup code omitted...

  let(:deliverer) { DeliverCallbackJob.new(user, payload) }

  describe "#perform!" do
    subject { deliverer.perform! }

    it "sends a callback to all of the user's associated Callback#callback_url endpoints" do
      expect(Net::HTTP).to receive(:post).exactly(user.callbacks.size).times
      subject
    end
  end
end
```
Now, `User#callbacks` is a standard `ActiveRecord::Associations::ClassMethods#has_many` association. So we may be tempted to now drop into our class definition and start implementing:

```ruby
#NOTE: This is pseudo code! Read between the lines somewhat please :)

class DeliverCallbackJob
  def perform!
    user.callbacks.each do |callback|
      Net::HTTP.post(callback.callback_url)
    end
  end
end
```
## The Problem

This works, yes - but we've unwittingly coupled logic unrelated to delivering a callback to this class. Specifically (and this can be hard to see in Rails a lot of the times), the usage of `user.callbacks` is the point of coupling. `DeliverCallbackJob` does not and should not care how we get a (possibly empty) list of callbacks from our `user` record.

## Re-writing our Test

A better test example - one that would have exposed this tendency to couple to the ActiveRecord API - might read like this:

```ruby
describe "#perform!" do
  subject { deliverer.perform! }

  let(:size)      { 4 }
  let(:callback)  { double(callback_url: "foo.bar.com/callback") }
  let(:callbacks) { Array.new(size, callback) }

  before { expect(deliverer).to receive(:callbacks).and_return(callbacks) }

  it "sends a callback to all of the user's associated Callback#callback_url endpoints" do
    expect(Net::HTTP).to receive(:post).with("foo.bar.com/callback").exactly(size).times
    subject
  end
end
```
The key difference here is the assertion we've added to the `before` hook:

```ruby
before { expect(deliverer).to receive(:callbacks).and_return(callbacks) }
```

We've implicitly augmented the interface of `DeliverCallbackJob` by a method `#callbacks`.

This is a really good thing! Now, we have a place to put the logic of "gather all the `Callback` records against which I need to deliver the callback payload at hand". And as such, we have a new point at which we can introduce stubs or mocks in our tests.

In order to get this spec passing, we might refactor our class like so:

```ruby
class DeliverCallbackJob
  def perform!
    callbacks.each do |callback|
      Net::HTTP.post(callback.callback_url)
    end
  end

  private

  def callbacks
    # This is now an implementation detail, as opposed to a closely coupled
    # and hidden dependency stowed away within the `perform!` method.

    # For now, we're using ActiveRecord so implementation may be unchaged
    # relative to our first version of this class... but if we drop Rails/AR
    # in the future, we have a clearly documented spot to change this logic.

    user.callbacks
  end
end
```

## Recap

So what did we learn today? When you're struggling to get through your TDD, it tends to indicate hidden coupling and dependencies. When you feel this happening, take a closer look at your implementation and see what can be moved into private methods. Then, in your test examples, make those dependencies explicit so that you can mock, stub, or whatever suits you.
