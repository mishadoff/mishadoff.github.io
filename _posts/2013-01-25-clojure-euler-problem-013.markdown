---
layout: post
title: "Clojure Euler: Problem 013"
date: 2013-01-25 16:33
comments: true
categories: [clojure, programming, project-euler]
published: true
---

> Work out the first ten digits of the sum of the following one-hundred 50-digit numbers.

Permalink: [http://projecteuler.net/problem=13](http://projecteuler.net/problem=13)

<!-- more -->

*Assume, here is a huge amount of digits. In any case, you can always find them in permalink.*

What is the problem to sum one-hundred numbers?

``` clojure
(reduce + numbers)
```

Done.

Are you kidding me? Not so fast. The question is *what type of number you must choose*
to sum all these numbers. `int`? `long`? `looooong`? `super long long`?

Technique to sum very long numbers called
[Bignum arithmetic](http://en.wikipedia.org/wiki/Arbitrary-precision_arithmetic).
To solve that problem you just need to implement this technique... or use already implemented.

Java (*yeah we talking about it*) support long arithmetics with
[BigInteger](http://docs.oracle.com/javase/7/docs/api/java/math/BigInteger.html).
And clojure does.

``` clojure
(reduce + (map bigint numbers))
```

With that function you'll get the big number.
Just truncate first 10 digits and you are good.

``` clojure
(apply str (take 10 (str (reduce + (map bigint numbers)))))
```

(for [i "[GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem013.clj)"] i)


**P.S.** Ideally, last line should be wrapped
with `(read-string ...)` to produce number type. But we using
manual submission, so it is fair.
