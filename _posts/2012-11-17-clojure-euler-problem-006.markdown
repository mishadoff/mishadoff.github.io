---
layout: post
title: "Clojure Euler: Problem 006"
date: 2012-11-17 14:05
comments: true
categories: [clojure, project-euler, programming]
published: true
---

>The sum of the squares of the first ten natural numbers is,

>1^2 + 2^2 + ... + 10^2 = 385

>The square of the sum of the first ten natural numbers is,

>(1 + 2 + ... + 10)^2 = 55^2 = 3025

>Hence the difference between the sum of the squares of the first ten natural numbers and the square of the sum is 3025 − 385 = 2640.

>Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum.

Permalink: [http://projecteuler.net/problem=6](http://projecteuler.net/problem=6)

<!-- more -->

This problem is very simple, even simpler than previous one, that confirms fact that all Project Euler problems
are **NOT** in increasing complexity order. Ok, let's split our problem into two subproblems.

### Sum of the squares

First, we need a square function:

``` clojure
(defn sqr [n] (* n n))
```

Then, sum of squares of the first one hundred natural numbers we calculate with common `(reduce + list)` idiom:

``` clojure
(reduce + (map sqr (range 1 101)))
```

### Square of the sum

Just take a sum, and square it:

``` clojure
(sqr (reduce + (range 1 101)))
```

### Bind all together

We splitted our problem into two smaller, now is time to bind all. We can just subtract results obtained
but you see, that both of smaller solutions uses common piece of code `(range 1 101)`. Move it to `let` form.

``` clojure
(let [rn (range 1 101)]
  (- (sqr (reduce + rn)) (reduce + (map sqr rn)))))
```

That' all. I've got result in less than millisecond.
Maybe there are some pitfalls in this problem (*e.g. integer overflow for larger ranges*)
but this most straightforward solution works fine.

Congratulations!

[GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem006.clj)

**P.S.** First time I solved this problem and was confused: *Where is the trick?* Seems nowhere.
That's why using most straightforward solution is often the best choice (*excluding obvious cases, of course*).
It has fast implementation, and if it fails, it fails fast.

**Fail Fast, Succeed Faster**
