---
layout: post
title: "Clojure Euler: Problem 017"
date: 2013-04-09 21:13
comments: true
sharing: true
categories: [clojure, programming, project-euler]
published: true
---

> If the numbers 1 to 5 are written out in words: one, two, three, four, five,
> then there are 3 + 3 + 5 + 4 + 4 = 19 letters used in total.
>
> If all the numbers from 1 to 1000 (one thousand) inclusive were written out in words, how many letters would be used?

Permalink: [http://projecteuler.net/problem=17](http://projecteuler.net/problem=17)

<!-- more -->

To solve this problem we need some sort of mapping that defines association
between number and its string representation, e.g `123 = one hundred and twenty three`.

This can be done with maps in clojure in form `{:1 "one" :2 "two"}`.
But if pay more atention to this map, we see that key is a numeric value.
Exactly what plain arrays do.

Then we just define all mappings, obtain all arrays string elements, find their lengths and sum them.

One problem that providing mapping for each number between 1 and 1000 is a "bit" routine task, so we
need some formula to calculate representation instead of hardcoding it. We come to defining groups.

First group is just *first 20* numbers:

``` clojure
(def first20 ["one" "two" "three" "four" "five"
              "six" "seven" "eight" "nine" "ten"
              "eleven" "twelve" "thirteen" "fourteen" "fifteen"
              "sixteen" "seventeen" "eighteen" "nineteen"])
```

Note: there is no "zero" as we don't use it in spoken language.

Second group is numbers that divides 10:

``` clojure
(def decas ["" "ten" "twenty" "thirty" "forty" "fifty" "sixty" "seventy" "eighty" "ninety"])
```

Note: empty first element here is used to reduce additional check. Its length is 0, that has no effect at all.

And the last ones are three used values:

``` clojure
(def h "hundred")
(def t "thousand")
(def a "and")
```

The basic function will calculate the length string representation of some number.
Let's call it `word-length`.

``` clojure
(defn word-length [n]
  (cond (= n 1000) (+ (count t) (count (nth first20 1)))
        (< n 100)
        (let [q (quot n 10) m (mod n 10)
              d (count (nth decas q))]
          (if (zero? m) d
              (if (< q 2) (count (nth first20 (dec (+ m (* 10 q)))))
                  (+ d (count (nth first20 (dec m)))))))
        (< n 1000)
        (let [q (quot n 100) m (mod n 100)]
          (if (zero? m) (+ (word-length q) (count h))
              (+ (count a) (count h) (word-length q) (word-length m))))))
```

Little explanation:

* If number is `1000` then the length of string "one" + "thousand", 11.
* If number is lower than `100` then extract its decade part. If there is no
reminder for division by 10, the length is length of that decade part.
* Otherwise, if number in `first20`, length taken from array `first20`.
* Otherwise the length is decade part plus remainder.
* If number is lower than `1000` then extract hundred part. If there is no
reminder for division by 100, the length is "hundred" plus number of hundreds.
* Otherwise, the length is "hundred" plus number of hundreds, plus the word "and" and
recursively calculate the length of remainder, which is lower than `100`.

Awful word-presentation of algorithm, because of... Reading code is much simpler, I know.

And the last part just sum up all word-lengths:

``` clojure
(reduce + (map word-length (range 1 1001)))
```

Check the code [here](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem017.clj).

P.S. In fact algorithm is very simple, but needs some time to understand few branches.
By the way, the problem is little boring.
