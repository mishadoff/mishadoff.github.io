---
layout: post
title: "Clojure Euler: Problem 020"
date: 2013-07-21 13:39
comments: true
sharing: true
categories: [clojure, programming, project-euler]
---

> Find the sum of the digits in the number 100!

Permalink: [http://projecteuler.net/problem=20](http://projecteuler.net/problem=20)

<!-- more -->

First of all we need to calculate factorial.
Without integer overflows, stack overflows and other caveats.

Previous article [Fast Factorial](/blog/fast-factorial/) has working solution
that we might use.

```
(defn ! [n]
  (reduce *' (range 1 (inc n))))
```

Now, calculate the sum of digits, the same function that we used in
[Clojure Euler: Problem 008](/blog/clojure-euler-problem-008/) and
[Clojure Euler: Problem 016](/blog/clojure-euler-problem-016/)

```
(defn sum-of-digits [n]
  (reduce + (map #(- (int %) 48) (seq (str n)))))
```

And the final line

```
(sum-of-digits (! 100))
```

Simple enough, huh?

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem020.clj)

**P.S.** The functions `sum-of-digits`, `count-digits` and similar used often
and look very ugly, so I decided to create a separate set of functions (*library?*)
which I can refer. It should be a set of common mathematic functions, including
different sequences, sums, converters, interesting numbers. You might use it too.
Welcome to [numberto](https://github.com/mishadoff/numberto/)!
