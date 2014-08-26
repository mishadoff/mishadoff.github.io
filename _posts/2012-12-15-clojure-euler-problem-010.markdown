---
layout: post
title: "Clojure Euler: Problem 010"
date: 2012-12-15 00:23
comments: true
sharing: true
categories: [clojure, programming, project-euler]
published: true
---

> The sum of the primes below 10 is 2 + 3 + 5 + 7 = 17.
>
> Find the sum of all the primes below two million.

Permalink: [http://projecteuler.net/problem=10](http://projecteuler.net/problem=10)

<!-- more -->

We have already worked with prime numbers in
[Problem 003](/blog/clojure-euler-problem-003/) and
[Problem 007](/blog/clojure-euler-problem-007/). We also decided that
the best way to work with prime numbers in clojure to use `primes` lazy-seq
from `clojure.contrib.lazy-seqs`.

Gentle reminder: Just add proper `:use` to your file:

```clojure
(:use [clojure.contrib.lazy-seqs :only (primes)])
```

Then, the simplest problem solution to use `take-while` stream and `reduce` for sum:

```clojure
(reduce + (take-while #(< % 2000000) primes))
```

Again, one-line solution. Let's run it.

Finding result on my nachine takes ~12 seconds. Small enough, but maybe somehow it can be improved?

### Sieve of Eratosthenes

[Sieve of Eratosthenes](http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes) - ancient algorithm
for finding prime numbers. The best explanation how it works with picture from wiki:

![](http://upload.wikimedia.org/wikipedia/commons/b/b9/Sieve_of_Eratosthenes_animation.gif)

Our first naive implementation:

```clojure
(defn sieve-1 []
  (loop [nums (set (cons 2 (range 3 2000000 2))) n 3]
    (if (> n 2000000) (reduce + nums)
        (recur (clojure.set/difference nums (set (range (* n n) 2000000 n))) (inc n)))))
```

*Note:* [clojure.set/difference](http://clojuredocs.org/clojure_core/clojure.set/difference) function
find the difference between two sets.

This recursive implementation is even worse than our oneliner, it calculates the result in ~24 seconds.

Primary optimisation is to use `(* n n)` as recursion base instead of `n`. Because of there are no prime
numbers if we crossed greater than square root of maximum. Problem evolves.

``` clojure
(defn sieve-2 []
  (loop [nums (set (cons 2 (range 3 2000000 2))) n 3]
    (if (> (* n n) 2000000) (reduce + nums)
        (recur (clojure.set/difference nums (set (range (* n n) 2000000 n))) (inc n)))))
```

~21 seconds. Still worse.

Why we increment by 1 on recursion call? If we starting our `n` value from `3` we don't need
to go over even numbers.

```clojure
(defn sieve-3 []
  (loop [nums (set (cons 2 (range 3 2000000 2))) n 3]
    (if (> (* n n) 2000000) (reduce + nums)
        (recur (clojure.set/difference nums (set (range (* n n) 2000000 n))) (+ n 2)))))
```

~13 seconds. Much better... than previous, still worse than first version.

If you looked at `difference` documentation, take a look at its implementation under "Source".
Nothing prevent us to pass sequence as second argument instead of set. Just removing casting to set.

``` clojure
(defn sieve-4 []
  (loop [nums (set (cons 2 (range 3 2000000 2))) n 3]
    (if (> (* n n) 2000000) (reduce + nums)
        (recur (clojure.set/difference nums (range (* n n) 2000000 n)) (+ n 2)))))
```

~6 seconds. Wow! It's better than original solution. That means our efforts were not wasteful.

Unfortuantely, we stopped improve our function. But if you want more, other major improvements
can be done in the following areas:

* Use non-persistent set to save prime numbers. It prevent overhead on new objects.
* Use "wheel trick" that can be found in the sources of `primes` from `clojure.contrib.lazy-seqs`.
* Use dynamic step of iteration. We changed `(inc n)` to `(+ n 2)`, but probably there is smarter solution.
* Do not use `(reduce +)` because it is linear algorithm. We init `nums` sequence with some arithmetic progression.
Its sum can be calculated in O(1) by formula. When we compose another sequence (second argument for `difference`)
its also an arithmetic progression and sum can be calculated in O(1). When doing differencem just subtract second sum
from first sum, and it will be current `nums` sum.

There are much more optimisations can be done to improve prime numbers performace.
If you interested, read nice Christophe Grand's post
[Everybody Loves The Sieve Of Erathosthenes](http://clj-me.cgrand.net/2009/07/30/everybody-loves-the-sieve-of-eratosthenes/)

[(str "Git" "Hub")](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem010.clj)

**P.S** Honestly, I am OK with 12 seconds. But 6 seconds is better. We performed improvements
just to show the point **if you want to improve something, probably, you can do it**.
