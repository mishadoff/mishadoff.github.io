---
layout: post
title: "Clojure Euler: Problem 016"
date: 2013-03-05 01:31
comments: true
categories: [clojure, programming, project-euler]
published: true
---

> 2^15 = 32768 and the sum of its digits is 3 + 2 + 7 + 6 + 8 = 26.
>
> What is the sum of the digits of the number 2^1000 ?

Permalink: [http://projecteuler.net/problem=16](http://projecteuler.net/problem=16)

<!-- more -->

Are you kidding me?

* Find 1000th *power of two*
* Sum its digits

In [Clojure Euler: Problem 008](/blog/clojure-euler-problem-008/) we
learned how to sum digits in the number. Just gentle reminder:

``` clojure
(defn sum-of-digits [n]
  (reduce + (map #(- (int %) 48) (seq (str n)))))
```

Now, let's create sequence of powers of two:

``` clojure
(defn powers-of-2 []
  (iterate (partial * 2) 1))
```

Unfortunately, this sequence throws `integer overflow` on the `64th` element.
We can fix that using *long arithmetics*, which known as `BigInteger` in Java.
Change `1` to `1N`.

``` clojure
(iterate (partial * 2) 1N)
```

Another way is to use **automatic promotion** operator (`+'`, `*'`).
If result of some operation is not suitable for some type,
instead of invalid computation and runtime exception, clojure automatically promotes
the type to suitable one (*for example* `Long.MAX_VALUE +' 1` *works fine and produces
 correct result with type of* `BigInteger`):

``` clojure
(iterate (partial *' 2) 1)
```

Choose `powers-of-2` that you prefer and final result will look like this:

``` clojure
(sum-of-digits (last (take 1001 (powers-of-2))))
```

[github](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem016.clj)

**P.S.** Automatic promotion is a beatiful thing. But be aware about losing in
speed of calculations. Also, no way back. If promotion happened, *depromotion* won't.
