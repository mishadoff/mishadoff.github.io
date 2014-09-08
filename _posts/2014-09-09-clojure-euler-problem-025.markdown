---
layout: post
title: "Clojure Euler: Problem 025"
date: 2014-09-09 00:51
comments: true
categories: [programming, clojure, project-euler]
sharing: true
published: true
---

> What is the first term in the Fibonacci sequence to contain 1000 digits?

Permalink: [https://projecteuler.net/problem=25](https://projecteuler.net/problem=25)

<!-- more -->

We've introduced Fibonacci sequence at [Clojure Euler: Problem 002](/blog/clojure-euler-problem-002/)

Lazy sequence generates fibonacci numbers is dead simple:

```clojure
(defn fibonacci []
  (->> [0 1]
       (iterate (fn [[a b]] [b (+' a b)]))
       (map first)))
```

We also can count the length of number in digits. No decimal arithmetic. Cast number to string, and take length of a string

```clojure
(defn num-of-digits [n]
  (count (str n)))
```

To solve the problem use the most straightforward approach and you'll find the answer in less than 1 second.

```clojure
(->> (fibonacci)
     (map-indexed (fn [i n] [i (num-of-digits n)]))
     (drop-while #(< (second %) 1000))
     (first)
     (first))
```

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem025.clj)

**P.S.** If you followed all posts upto this one, then you have 25 problems solved, level 1 and nice badge

![](https://projecteuler.net/images/levels/level_1.png)
