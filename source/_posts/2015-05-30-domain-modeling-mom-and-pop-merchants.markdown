---
layout: post
title: "Domain Modeling Mom 'n Pop Merchants in a Mobile Finance Platform"
date: 2015-05-30 11:04:19 -0400
comments: true
categories: oop ruby rails inheritance
---
In my primary work project currently, I'm working with a small startup to build a mobile finance platform for the developing world.

The reason: people in the developing world are largely unbanked. Estimates vary, but around [59% of adults in developing economies don't have an account at a financial institution.](http://siteresources.worldbank.org/EXTGLOBALFIN/Resources/8519638-1332259343991/world_bank3_Poster.pdf) That said, using modern technologies (read: *really* cheap mobile phones, Bitcoin, and the web) it should be possible to bring banking-like services to the 2.5 billion people that use cash day-to-day almost exclusively.

## Where the idea of Merchants comes in to play

In order to get our targeted users off cash, and onto a mobile finance platform, we need merchants in the developing world to be ready to accept payment via this mobile finance platform (which I'll henceforth refer to as "MobiCommerce" for short).

In many ways, 'merchants' in our domain will be similar to everyday 'users', in that they'll be sending and receiving funds virtually via MobiCommerce.

Here is the `User` resource's schema for staters:

```ruby
create_table "users", force: :cascade do |t|
  t.string   "phone_number",                        null: false
  t.datetime "created_at",                          null: false
  t.datetime "updated_at",                          null: false
  t.integer  "status",                default: 0,   null: false
  t.string   "name"
  t.string   "pin"
  t.decimal  "balance",               default: 0.0, null: false
  t.string   "device_id"
  t.integer  "balance_inquiry_count"
  t.string   "locale",                              null: false
  t.integer  "referrer_id"
end
```

The main attributes of interest for a particular `User` are `phone_number`, `pin`, and `balance` (at least insofar as executing a transaction on the platform is concerned).

### What's different about a merchant?

Originally, my answer to this question was something along the lines of: "Not that much is different. I'll basically just need to slightly different messages in the transaction process." For example, we're planning to charge a small fee to merchants in order to accept MobiCommerce as a payment option at their shops. So we'd need the system to identiy that a merchant is on the receiving end of a transfer/transaction, and alert them via SMS accordingly - including notifying the merchant of the fee that will be taken out. 

To domain model this, I first reached for [inheritance](http://makandracards.com/makandra/16077-inherit-without-single-table-inheritance-sti-in-activerecord):

```ruby
# app/models/user/merchant.rb
class User::Merchant < User
  after_create :onboard_merchant!
	
  private
	
  def onboard_merchant!
    TELEPHONY_CLIENT.send_sms(to: self.phone, message: "Reply with your business name", from: self.device_id)
  end
end
```

However, this just felt wrong to me. Typically, subclassing an `ActiveRecord`-backed model in Rails is best for organizing a limited & specific set of domain-specific behavior. The classic example is something like:

```ruby
class SignUp < User ; end
```

Now, that `SignUp` class is a great place to put things like `validates :password_confirmation, presence: true` kinda' business logic. The term for this in Ruby is ["form models"](http://railscasts.com/episodes/416-form-objects). Any domain and/or business logic that pertains uniquely to signing up a user now has a home. This class gives you a perfect place to [encapsulate](http://samurails.com/interview/ruby-inheritance-encapsulation-polymorphism/) that behavior.


## The Break Point

I quickly hit a point with my `Merchant` resource where I realized it had outgrown its inheritance from the `User` class. Instead of simply adding merchant-specific business logic & behaviors into this class, I found myself overwriting many of the methods inherited from `User` in order to tweak the desired behavior when a merchant was involved in a transaction.

### A merchant is really just a user with an associated `Business`...

Thanks to my good friend [Chris Lee](https://twitter.com/creeefs), I arrived at a much better solution to this "where to house my `Merchant` business logic" predicament.

Remember, I originally inherited from `User` because I still needed all the logic that connected two people (merchants or non-merchants alike) doing a financial transaction - either a payment to a store, or a Venmo-style peer-to-peer transfer. 

Chris pointed out that I could instead organize my merchant-related logic into its own model, called `Business`. Now, a "merchant" in my system is simply:

```ruby
class User < ActiveRecord::Base
  # most code omitted...
  has_one :business
  
  def merchant?
    business.present?
  end
end
```

That is, it's just a user instance with an associated business. Much cleaner, much more elegant, and much more expressive. Let's look at some examples.

First, this is how I can assess the merchant-specific transaction fee within my `Transaction` class:

```ruby
class Transaction < ActiveRecord::Base
  # most code omitted...
  belongs_to :sender, class_name: User
  belongs_to :receiver, class_name: User
  
  before_create :assess_merchant_fee!, if: :receiver_is_merchant?
  
  private
  
  def receiver_is_merchant?
	  receiver.merchant?
  end
  
  def assess_merchant_fee!
    # deduct the fee from the amount received by the merchant and notify them...
  end
end
```

I was so relieved that I had a home for all of my `Business`-related logic, that I felt compelled to write this blog post. I hope you've enjoyed reading it :smile:

P.S. From what I understand, Facebook uses this same approach to managing their Business Pages.
