---
layout: post
title: "Clojure Euler: Problem 021"
date: 2013-10-05 15:24
comments: true
sharing: true
categories: [programming, clojure, project-euler]
published: true
---

> Evaluate the sum of all the amicable numbers under 10000.

Permalink: [http://projecteuler.net/problem=21](http://projecteuler.net/problem=21)

<!-- more -->

> Let d(n) be defined as the sum of proper divisors of n
> (numbers less than n which divide evenly into n).
> 
> If d(a) = b and d(b) = a, where a != b, then a and b are an
> amicable pair and each of a and b are called amicable numbers.
>
> For example, the proper divisors of 220 are
> 1, 2, 4, 5, 10, 11, 20, 22, 44, 55 and 110; therefore d(220) = 284.
> The proper divisors of 284 are 1, 2, 4, 71 and 142; so d(284) = 220.

We can do it *bruteforcely*, but we won't.

Let start with the function `d`. We call it `sum-of-proper-divisors`

``` clojure
(defn sum-of-proper-divisors [n]
  (let [base (filter #(zero? (mod n %)) (range 2 (Math/sqrt n)))]
    (reduce + 1 (concat (map #(/ n %) base) base))))
```

Bad approach to find all divisors: to iterate on all numbers from 1 to `n`
and check if it is divisor or not.
Instead, we iterate from 1 to `sqrt(n)`, find one divisor and
calculate another symmetric divisor.

For example, take number `10`.
We iterating from `2` to `(sqrt 10)`. Number `2` is divisor, so we divide `(/ 10 2)`, get `5` which is also divisor. And so on.

This is predicate checks where two numbers amicable or not:

``` clojure
(defn amicable? [a b]
  (and (not (= a b))
       (= a (sum-of-proper-divisors b))
       (= b (sum-of-proper-divisors a))))
```

Obvious enough, just consider that we skip case where `(= a b)`.

The last part is to use our functions to obtain result.

``` clojure
(reduce +
  (let [sums (vec (map sum-of-proper-divisors (range 1 10000)))]
    (for [i (range 1 10000)]
      (if (amicable? i (nth sums (dec i))) i 0))))
```

Just generate all sums, and check if another number `i` produce the amicable pair. Done!

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem021.clj)

**P.S.** Take a look at the last snippet. `vec` is really important there.

