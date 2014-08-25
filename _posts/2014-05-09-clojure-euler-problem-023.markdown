---
layout: post
title: "Clojure Euler: Problem 023"
date: 2014-05-10 18:11
comments: true
categories: [programming, clojure, project-euler]
published: true
---

> Find the sum of all the positive integers
> which cannot be written as the sum of two abundant numbers.

Permalink: [http://projecteuler.net/problem=23](http://projecteuler.net/problem=23)

<!-- more -->

To define abundant number let's revisit `sum-of-proper-divisors` from the [Clojure Euler: Problem 021](/blog/clojure-euler-problem-021)

``` clojure
(defn sum-of-proper-divisors [n]
  (let [base (filter #(zero? (mod n %)) (range 2 (Math/sqrt n)))]
    (reduce + 1 (concat (map #(/ n %) base) base))))
```

Using that function, `abundant?` predicate just follows definition.

``` clojure
(defn abundant? [n]
  (> (sum-of-proper-divisors n) n))
```

If you look carefully at `sum-of-proper-divisors` it is buggy.

``` clojure
(sum-of-proper-divisors 25) => 1 ;; instead of 6
```

Square numbers are not handled correctly.
To fix that we add one more list to `concat`.

``` clojure
(defn sum-of-proper-divisors [n]
  (let [divs (filter #(zero? (mod n %)) (range 2 (Math/sqrt n)))]
    (reduce + 1 (set (concat
                      (let [isq (int (Math/sqrt n))]      ;; added
                        (if (= n (* isq isq)) [isq] []))  ;; added
                      divs
                      (map #(/ n %) divs))))))
```

Double check

``` clojure
(sum-of-proper-divisors 25) => 6
```

Great! Next step is to find all integer numbers which can not be written as the sum of two abundant numbers.

Precalculate all abundant numbers:

``` clojure
(def abundant (filter abundant? (range 12 28124)))
```

Now we can build all numbers which can be written as
two abundant numbers and find their sum.

``` clojure
(->> (for [i abundant j abundant
           :let [num (+ i j)]
           :when (< num 28124)]
       num)
     (distinct)
     (reduce +)
     (- (reduce + (range 1 28124))))
```

This solution works ~44 sec, too much.

More clever approach is to iterate from all integers upto 28124 and check if some number can be written as a sum of two abundant numbers.

The trick is simple: we building sorted set of abundant numbers

``` clojure
(def abundant (into (sorted-set) (filter abundant? (range 12 28124))))
```

For every integer number `n` we subtract one abundant number and check if `abundant` set contains result.
There is a good function [some](http://clojuredocs.org/clojure_core/clojure.core/some) that makes our life easier.

``` clojure
(defn abundant-sum? [n abundant]
  (some #(abundant (- n %)) abundant))
```

Finding the sum


``` clojure
(->> (range 1 28124)
     (remove #(abundant-sum? % abundant))
     (reduce +))
```

It works ~26 sec.

Another optimization is to limit number of abundants in
`abundant-sum` function.

``` clojure
(defn abundant-sum? [n abundant]
  (some #(abundant (- n %))
         (take-while #(< % n) abundant)))
```

Time reduced to ~14 sec. Pretty good.

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem023.clj)

**P.S.** Buggy version of `sum-of-proper-divisor` solved the previous problem correctly, but it was just a luck. Make sure you test your functions.
