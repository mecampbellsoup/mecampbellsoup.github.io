---
layout: post
title: "a short &amp; sweet intro to objects in ruby"
date: 2013-10-08 08:25
comments: true
categories: [ruby, POOD, sandi metz, flatiron school, nyc]
---
Recently here at Flatiron we've begun to write our code with a focus on object orientation. To a beginner, this sentence has absolutely zero meaning whatsoever - so let's take a step back and define some terms.

First of all, there are a couple truisms to keep in mind when it comes to code:
<ul>
<li>First, in case you haven't heard yet, things are going to change. Your application will grow in users; new technologies will emerge; your investors will want you to add new features (talk about a first world problem!). Your code base needs to be flexible when it comes to making changes for any of these reasons - flexible like a reed in the wind, as opposed to brittle like, say, graphite. As Sandi Metz puts it: 
<blockquote><em>
Changing requirements are the programming equivalent of friction and gravity.
</em></blockquote>
</li></ul>
<br>
Ok, great! So we know change is a-comin'. How do we structure our code to be prepared like the Boy Scouts?
<ul>
<li>The less the different segments of your code base know about one another, the better! Admittedly, this one is a bit counterintuitive. In life, we're typically used to setting all of our ducks in a row before taking any major steps or jumping off any cliffs (figuratively speaking, for the most part). We're naturally pretty risk averse, and we routinely succumb to the delusion that we can mitigate risk and uncertainty a bit by lining up said ducks. It's as if we're trying to tear the mask of the unknown off so that we can make decisions today with perfect information. Programmers have adopted a different approach: instead of trying to know and discount the future, they simply design programs that will adapt easily, fullstop. Note I've made no assumption about what changes will emerge to throw our programs for a loop; the emphasis is on a coding paradigm in which your different segments can easily change how they communicate with one another. This is what people mean when they talk about the principle of object-oriented design. Once again, let's get Sandi's take:
<blockquote><em>
In the absence of design, unmanaged dependencies wreak havoc because objects know too muchabout one another. Changing one object forces change upon its collaborators, whichin turn, forces change upon its collaborators, ad infinitum.
</em></blockquote>
</li></ul>

Now let's take a look at some code. Let's try to build a bicycle in Ruby - first we'll eschew the object-oriented princples just discussed, and then we'll embrace them. Hopefully the proof is in the pudding!

First, the painful, hard-to-read & -follow version…
<script src="https://gist.github.com/mecampbellsoup/6900628.js"></script>

Whew, that was pretty exhausting and hard to follow. Let's see what happens if we segregate along natural, real-world dividing lines…
<script src="https://gist.github.com/mecampbellsoup/6900643.js"></script>