---
layout: post
title: "guiding your users: custom logic in your rails application's navbar"
date: 2013-11-16 18:01
comments: true
categories: [ruby, rails, controllers, views, navbar]
---
#navbars are important

Real talk: navbars are fantastic. They're really good at their job, which is providing a constant set of routes to your users to find their way around. No user ever gets lost when there's a good navbar around to help out. 

New Rails projects ship with an application layout (that is, ```application.html.erb``` file) that provides an ideal home for our navbar since we want the bar to always be visible at the top of our app, and the application layour is rendered by all views by default. 

The problem with navbars is that they typically aren't very intelligent. For example, when you're currently viewing a webpage within an application, most of the time the navigation bar does not update to hide that option dynamically. Why, oh why, would you want to click the same link twice? If you feel me on this one, keep reading...

##how to make your navigation bars dynamic

I set out to make my navigation bar dynamic (meaning, again, that it would update its contents/links based on which view page the user was currently looking at), and found it was actually pretty straightforward thanks to inheritance in Ruby. 

###the problem, in words

Abstractly, the goal is to create some object that's available throughout my application, in all my controllers and views, that will contain the logic and knowledge of the last URL that the user requested. This way, if a user has just clicked, say, "About" on your navigation bar, the next view should not continue to show that link since it's become redundant.

###the solution, in words

I know that every request hitting my application generates a new instance of the controller behind that particular endpoint. I also know that all of my controllers inherit from a master controller of sorts, which is typically ```ApplicationController``` in Rails. Therefore, since my other controllers - the ones actually doing the work of passing data between my models and views - inherit directly from ```ApplicationController```, I figured I could write a method there that did some introspection on the most recent ```ActionDispatch::Request``` object in order to keep track of the last request's URL. In code, this strategy looked something like this:

```ruby
class ApplicationController < ActionController::Base
  private

  def set_current_url
    @current_path = request.path
  end

  # rest of class omitted for brevity
end
```

So easy! Through this code, I now had access to the ```@current_path``` variable that would be available at each point in time within my views. Here is how I made use of that object (which is available via the ```set_current_path``` method) in my views in my application layout file:

```ruby
<nav class="navbar navbar-inverse" role="navigation">
  <!-- Brand and toggle get grouped for better mobile display -->
  <div class="navbar-header">

    <% unless @current_path.match(players_path) %>
      <button type="button" class="btn btn-default navbar-btn" data-toggle="collapse" align="center">
         <%= link_to "Ladder Rankings", players_path %>
      </button>
    <% end %>

    <% unless @current_path.match(timer_path) %>
      <button type="button" class="btn btn-default navbar-btn" data-toggle="collapse" align="center">
         <%= link_to "Game Timer", timer_path %>
      </button>
    <% end %>

    <% unless @current_path == challenges_path %>
      <button type="button" class="btn btn-default navbar-btn" data-toggle="collapse" align="center">
         <%= link_to "Battle History", challenges_path %>
      </button>
    <% end %>

    <% unless @current_path == new_challenge_path %>
      <button type="button" class="btn btn-default navbar-btn" data-toggle="collapse" align="center">
         <%= link_to "Record an Outcome", new_challenge_path %>
      </button>
    <% end %>

  </div>
</nav>
```

Now, my navigation bar updates dynamically, presenting the user only with logical options for their next to-be-viewed page or resource in my application. Fantastic!



