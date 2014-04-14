---
layout: post
title: "implemented list processing (lisp!) in ruby"
date: 2014-01-12 22:41
comments: true
published: false
categories: [ruby, lisp, proc, metaprogramming]
---

```
module Lisp
  def cons(x,y)
    -> (m) { m.call(x, y) }
  end

  def car(z)
    z.call(-> (p, q) { p })
  end

  def cdr(z)
    z.call(-> (p, q) { q })
  end
end
```

```
# Note: copy/paste the above into pry/irb, and then `include Lisp`
```

```
# list of 2 values
car(cons(1, 5)) #=> 1
cdr(cons(1, 5)) #=> 5
```

```
# list of 1 list and 1 value
car(car(cons(cons(5, 2), 1))) #=> 5
cdr(car(cons(cons(5, 2), 1))) #=> 2
cdr(cons(cons(5, 2), 1))      #=> 1
```

```
# list of 2 lists each with values
car(car(cons(cons(5, 2), cons(3, 4)))) #=> 5
cdr(car(cons(cons(5, 2), cons(3, 4)))) #=> 2
car(cdr(cons(cons(5, 2), cons(3, 4)))) #=> 3
cdr(cdr(cons(cons(5, 2), cons(3, 4)))) #=> 4
```
