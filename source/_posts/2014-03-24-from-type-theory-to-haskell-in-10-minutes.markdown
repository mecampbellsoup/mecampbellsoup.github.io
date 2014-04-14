---
layout: post
title: "From Type Theory to Haskell in 10 Minutes!"
date: 2014-03-24 14:32
comments: true
published: false
categories: [type theory, lambda calculus, haskell]
---
You've probably heard of "types", and maybe even "type systems" in your learning so far as a [#c0d3r](http://en.wikipedia.org/wiki/Leet). For example, you've probably heard that [Ruby is strongly typed](http://www.rubyfleebie.com/ruby-is-dynamically-and-strongly-typed/), while [C is weakly typed](http://coding.smashingmagazine.com/2013/04/18/introduction-to-programming-type-systems/). Type systems are pretty clutch - to varying degrees, they ensure the integrity and expected behavior of our programs. But more generally, types provide us a formal system for posing quesitons of logic & determining whether said question is, in fact, answerable.

But where did these super-powered "types" come from? As it turns out, the type systems that we use every day in our programming languages are descendants of a field of logic known as [type theory](http://plato.stanford.edu/entries/type-theory/). In the next 10 minutes (I promise!), we'll travel from type theory to functional programming languages and connect the dots along the way.

### Leibniz Sets The Stage

[Leibniz](http://en.wikipedia.org/wiki/Gottfried_Wilhelm_Leibniz) - who demonstrated the superiority of the binary over the decimal representation for mechanical computers back in, oh, **1680** (!) or so - was such a proponent of algorithmic solutions to *all* problems that he would recommend to disputants in *any* field to sit down at a table, take out their pens, and say:

> "Calculemus!" (Or: "Let us calculate!")

Leibniz therefore had as an ideal the following:

1. Create a "universal language" in which all possible problems can be stated.
1. Find a decision method to solve all the problems stated in the universal
language.

Henceforth I will refer to Leibniz's "ideal" as the challenge to define "effective calculability". Many would rise to Leibniz's challenge - to implement such a system to formally pose & solve *all* problems - including one Bertrand Russell.

### Russell Rises to Leibniz's Challenge

One of the better-known mathematician-logicians to grapple with Leibniz's effectively-calculable challenge was [Bertrand Russell](http://en.wikipedia.org/wiki/Bertrand_Russell). His opus [*Principia Mathematica*](http://en.wikipedia.org/wiki/Principia_Mathematica) represented an early attempt to create a logical basis for all of mathematics - that is, an attempt to define effective calculability for all mathematical problems. Russell's theory of types was introduced as a response to a logical contradiction in set theory that resulted from [George Cantor's theorem](http://en.wikipedia.org/wiki/Cantor's_theorem) that no mapping of a function `F` across a set `X`

```
F : X → Pow(X)
```

can be surjective (i.e. `F` cannot be such that _**for every *`b`* in `Pow(X)`**_, there is a value *`a`* in `X` such that `F(a)` is equal to `b`). This can be phrased “intuitively” as the fact that **there are more _subsets_ of `X` than _elements_ of `X`.** (Or perhaps even more comprehensively: there cannot be a 1:1 mapping between any set `X` and its "powerset" `Pow(X)`, because there exist more subsets of `X` than distinct elements of `X`.)

#### A Quick Example of Cantor's Theorem

Let us take, for example, a set of primitives `X -> [1, 2, 3]`. The cardinality (i.e. the number of elements contained in the set) of `X` is 3. What is the cardinality of the "powerset" `P` of `X`? Well, with the powerset defined as "the set of all subsets", let's enumerate `P(X)`:

```
P(X) -> [
			[ ∅, ∅, ∅], [ 1, 2, 3],
			[ 1, ∅, ∅], [ ∅, 2, 3],
			[ ∅, 2, ∅], [ 1, ∅, 3],
			[ ∅, ∅, 3], [ 1, 2, ∅]

		]
```

As you can see, the cardinality of `P(X)` is 8 since there are 8 total subsets. More generally, the relationship between `X` and `P(X)` is represented by the function `f(x) = 2^x`. In other words, a set `X` with cardinatliy `n` will have a "powerset" `P(X)` whose cardinality will be equal to `2^n`. Cantor's theorem extended this logic to [infinitely-large sets as well](http://en.wikipedia.org/wiki/Controversy_over_Cantor's_theory).

Here's a visualization of the concept of powersets, over an abstract set `X -> {x, y, z}`:

![](http://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/Hasse_diagram_of_powerset_of_3.svg/250px-Hasse_diagram_of_powerset_of_3.svg.png)

### Demystifying Russell's Paradox

For Russell, Cantor's Theorem was problematic towards his efforts to define effective calculability. After all, how can one produce a decision method to solve a problem concerning an infintely-large powerset that is even more infinitely-large than, say, the infinitely-large set of finite real numbers? (Whew!)

As Russell grappled with Cantor's Theorem, he observed a rather confounding feature of naïve sets. In attempting to map the set of all sets "which are not members of themselves", a paradox emerged.

To explain the paradox, let's start with another simple example. Using [set-builder notation](http://www.mathsisfun.com/sets/set-builder-notation.html), we can express the set of integers greater than 3 and less than 7 as follows:

```
let x = { n: n is an integer and 3 < n < 7}
```

Seemingly, we could provide any "predicate" (i.e. the logic that determines the set's members, or `is an integer and 3 < n < 7` above) or [proposition](http://en.wikipedia.org/wiki/Proposition#Treatment_in_logic) to describe the `x` that will comprise our set. One specific predicate leads us succinctly to Russell's paradox:

```
let y = {x: x is not in x}
```

Translated as: "Let `y` be equal to the collection of sets `x`, where each `x` is not a member of itself." Another translation: "Any set - for example `b` - that is not in `y` must contain itself, since `y` is the set of all sets that do not contain themselves."

Is the set we've just defined `y` itself a member of, or "in", `y`? Either answer leads to a contradiction:

* If we concede that `y` is in fact a member of `y`, then we have violated the predicate's logic that `y` **only** contains sets that do not contain themselves.
* If we then declare that `y` is NOT a member of (is not "in") `y`, then we are saying that `y` is a set that contains itself. (Prepare for brain-melting.) Basically, since `y` does not contain itself, it _must_ be a member of `y` as we have satisfied the predicate - but this contradicts our declaration upfront that `y` is NOT in `y`.

Therein lies [Russell's Paradox](http://en.wikipedia.org/wiki/Russell's_paradox). (You can read [here](http://plato.stanford.edu/entries/russell-paradox/) for further discussion if you're still a bit confused - which is to be expected, frankly.)

### The Issue: Unrestricted Comprehension.

In the language of naïve set theory, Russell's Paradox is a result of the [axiom schema of comprehension (unrestricted)](http://en.wikipedia.org/wiki/Unrestricted_comprehension#Unrestricted_comprehension), which states:

> There exists a set B whose members are precisely those objects that satisfy the predicate φ.

As we've just seen, the above axiom leads directly to Russell's paradox by taking `φ(x)` to be `¬(x∈x)` (i.e., the property that set `x` is not a member of itself).

Like many paradoxes, it certainly feels like Russell's Paradox stems from a certain ambiguity of terms. Does it make sense to talk about a set that contains itself? Can the set of integers `[1,2,3]` really contain itself - that is, contain the set `[1,2,3]` - as one of its **sub**-sets? The problem in the paradox is that we are confusing, in the example of the set `[1,2,3]`, a description of sets of integers with a description of _sets of sets_ of integers. We are [allowing a predicate to act on itself](http://homepages.inf.ed.ac.uk/wadler/papers/propositions-as-types/propositions-as-types.pdf) in asking whether set `X` contains `X`.

(NB: As we will see with lambda calculus, this is akin to a proposition of the form `P(P)`, which is structurally impossible in that system as well and hence we avoid the paradox.)

### Type Theory to the (Paradoxical) Rescue!

Type theory was born from the realization that sets constructed hierarchically could not suffer from the Paradox. In type theory, Russell introduced a hierarchy of objects: numbers (type 0), sets of numbers (type 1), sets of sets of numbers (type 2), and so on ad infinitum (type `n`).

The set of numbers `[1,2,3]` can now be expressed as having type 1 - it contains number objects of type 0. We now restrict our set `[1,2,3]` from including itself as a member since it can only include objects whose type is less than 1. As succinctly as the Paradox was expressed above, we have now banished it from existence by assigning a type to every possible set.

## The Untyped Lambda Calculus

Alonzo Church first introduced the λ-calculus as “A set of postulates for the foundation of logic” in two papers of that title published in 1932 and 1933. Like Russell, Church had set out to formalize the concept of "effective computability" (which eventually developed into the concept of a [computable function](http://en.wikipedia.org/wiki/Computable_function), or algorithm). The name derives from the Greek letter lambda (`λ`) used to denote binding a variable in a function.

### Grammar Rules

Intuitively, the expression (later called a "term") `λx.x2` corresponds to an anonymous definition of the mathematical function `f(x) = x2`.

The terms (formulas, expressions; these three are used interchangeably) of the calculus are defined inductively as follows:

* `x` is a term for any variable.
* If `M`, `N` are terms, then `(MN)` is a term. *(This bullet corresponds to function composition.)*
* If `x` is a variable and `M` a term, then `(λx.M)` is a term. *(This bullet is called "abstraction" and corresponds to function definition.)*

Intuitively, the notation `λx.M` corresponds to the mathematical notation of an anonymous function `x ↦ M` (read: "variable `x` mapped to the formula `M`").

### Booleans

Because **everything** is a function in lambda calculus, you may be wondering how basic logic is implemented at all. Let's take a quick look at booleans:

* `true	= λxy.x`
* `false	= λxy.y`

In other words, true is a function of two arguments which returns its first argument and false is one which returns the second argument. ([Further reading.](http://www.iep.utm.edu/lambda-calculi/))

### Integers

Great, we have defined Boolean objects as functions, so we can check that box. But what about numbers? Those seem pretty important if we're going to implement a programming language in lambda calculus. As it turns out, Church developed a *purely functional* encoding for integers as well!

The numerals are defined in the lambda calculus as follows:

```
1 = λab.ab
2 = λab.a(ab)
3 = λab.a(a(ab))
⁝
```

Simply put, an integer `n` can be represented as recursively calling the anonymous function above `n` times! How cool is that?!

We're almost there - we can finally see the building blocks for a programming language implemented with lambda calclulus, but we're missing a crucial component: the relationship between mathematical proofs and computer programs carrying out instructions...

## "Proofs as Programs": The Curry-Howard Correspondence

In the interest of time (read: I promised you ten minutes only!), I'll be succinct here.

We can state the Curry–Howard correspondence in general terms as a correspondence between proof systems and type theories. One way of stating this is that types correspond to propositions, and values correspond to proofs.

When interpreting something as a logical proposition, you're only interested in whether the type is inhabited (has any values) or not. Therefore, the following program in Haskell is, in fact, a proof:

```haskell
theAnswer :: Integer
theAnswer = 42
```

In this not-so-exciting example, our proposition reads something like, `"There exists something called an Integer"`, and our proof of this proposition is the value `42`. Because the type `Integer` is *inhabited* (has any values), we are guaranteed to have an algorithm

Let's take a look at a more interesting proposition-proof combination, a `concat'` function modeled after the Haskell Prelude library function of the same name:

```haskell
concat' :: [[a]] -> [a]
```
Here we are given the type, which can be translated as follows:

> Given a list of lists containing values of type `a`, we will always be returned a list of values of type `a`.

(NB: In Haskell, a `String` is equivalent to a list of `Char`s, which means a `String` type-checks with `[a]` in the type signature above.)

If we can demonstrate that a **value** exists for this proposition, then we have effectively proved the program. Let's take a look at another example:

```haskell
λ > concat' ["hello ", "world!"]
"hello world!"
```

By giving our computer the instructions to evalute `concat' ["hello ", "world!"]` we have simultaneously *proved* the proposition that for some `[[a]]` we will be returned an `[a]`. We've bridged the gap between mathematical proofs and computer programs!

But there's something even cooler - since Haskell (and most functional programming languages at that) correspond to [intuitionstic (or constructive) logic](http://en.wikipedia.org/wiki/Intuitionistic_logic), we're also guaranteed to have an algorithm to arrive at our `[a]` return value from an `[[a]]`. And sure enough, here is that algorithm:

```haskell
concat' [] = []
concat' (x:xs) = x ++ concat' xs
```

Because of Curry-Howard Correspondence, our programs now represent mathematically-sound proofs (and vice versa). In other words, our programs (ahem) PROVE THEMSELVES at compile time.

## Time's Up!

From Leibniz to Russell; from type theory to the lambda calculus; and from the simply-typed lambda calculus to Curry-Howard Correspondence, we've traversed the evolution of the idea of "effecfively calculable" from the nonexistent ideal of philosophers & logicians all the way to self-proving functional programs (like the ones we wrote in Haskell). I hope you're inspired to dig a little deeper, and please let me know what you think in the comments below!
