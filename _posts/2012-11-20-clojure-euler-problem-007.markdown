---
layout: post
title: "Clojure Euler: Problem 007"
date: 2012-11-20 15:37
comments: true
categories: [clojure, programming, project-euler]
published: true
---

> By listing the first six prime numbers: 2, 3, 5, 7, 11, and 13, we can see that the 6th prime is 13.

> What is the 10001st prime number?

Permalink: [http://projecteuler.net/problem=7](http://projecteuler.net/problem=7)

<!-- more -->

It's also pretty clear problem to make you more familiar with prime numbers.
As I said in [Clojure Euler: Problem 003](/blog/clojure-euler-problem-003),
big amount of Project Euler problems devoted to prime numbers so it is **MUST HAVE** (*I don't like this phrase either*)

You need to implement by yourself effective prime number algorithm. And only after that use one from library.
As we do.

``` clojure
(:use [clojure.contrib.lazy-seqs :only (primes)])
```

This line import just one symbol `primes` to your namespace. It's a prime numbers lazy sequence. Lazy sequences briefly covered
in [Clojure Euler: Problem 002](/blog/clojure-euler-problem-002) or [Wikipedia article](http://en.wikipedia.org/wiki/Lazy_evaluation),
that's enough for now.

Return to question.

> What is the 10001st prime number?

No problem.

``` clojure
(last (take 10001 primes))
```

Two lines of code: one for import, one for solution. What can be simpler?

Note, that [last](http://clojuredocs.org/clojure_core/clojure.core/last) return last element of the sequence and works in linear time.

Another one problem solved in few <strike>seconds</strike> minutes.

[GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem007.clj)

**P.S.** Actually, I lied. In linked solution I use 6 lines of code: namespace declaration, use, *empty line*,
comment with executing time, function declaration and, finally, solution.
