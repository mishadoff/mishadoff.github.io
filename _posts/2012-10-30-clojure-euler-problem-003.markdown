---
layout: post
title: "Clojure Euler: Problem 003"
date: 2012-10-30 19:12
comments: true
sharing: true
categories: [clojure, programming, project-euler]
---

> The prime factors of 13195 are 5, 7, 13 and 29.

> What is the largest prime factor of the number 600851475143?

Permalink: [http://projecteuler.net/problem=3](http://projecteuler.net/problem=3)

<!-- more -->

If you familiar with [prime numbers](http://en.wikipedia.org/wiki/Prime_number), you apparently know basic algorithms to find them,
if not, I advice to get familiar with it. There are lot of problems devoted to prime numbers on Project Euler. So, read 10 minutes about prime numbers, and go on.

In few words, prime number is a number starting from 2 that has no divisors except *1* and *itself*.
Exposing this logic on all numbers, we can simply find first prime numbers: `2, 3, 5, 7, 11, ...`

To implement function that search for prime numbers we can implement predicate, that test whether number is prime or not:

``` clojure
(defn prime? [n]
   (= 2 (reduce +
                (for [i (range 1 (inc n))]
                  (if (= 0 (mod n i)) 1 0)))))
```

We iterate from 1 up to number and calculate reminder of division number by iteration counter.
If result is zero we return 1, otherwise we return 0.
For each number we get list with length of that number that marked with 1 for exact divisors.
For example for `6` list will be `(1 1 1 0 0 1)`, and for `7` list will be `(1 0 0 0 0 0 0 1)`.
Than we calculate sum of this list, which represents number of divisors. If count is two, that means number is prime.
This is simplest algorithm for testing prime number, built from prime number definition.

Performing small optimization you can see that in lists above, first and last elements are always equals to 1.
We can just drop them. Another optimization is we don't need go on when we found at least one remainder.
And I'm pretty sure you can find lot of flaws in this implementation.

But the most useful optimization is assumption that we don't need iterate to `n`, we can iterate to `sqrt(n)`.
This is based on fact that if we found some number `a` that is divisor of `n`, then there is another number `b = n / a`.
And `b` is also divisior of `n`. Little transformation gives us `a * b = n`. Assume `a < b`, so if we found `a` we don't need to seek for `b`.
Number `n` is not prime. Maximum value of a achieved when `a` equals to `b`, `a^2 = n`, `a = sqrt(n)`.
This is reduce complexity of algorithm from `O(n)` to `O(sqrt(n))`.

In [previous problem](/blog/clojure-euler-problem-002/) we mentioned `clojure.contrib.lazy-seqs` package,
where we have primes lazy sequence. Let's use it!

Algorithm for solving our problem is following:

1. Iterate through all prime number that less than square root of number
2. If this number is divisor add it to list
3. Find maximum from that list

To transform statements above into clojure, we get:

``` clojure
(defn greatest-prime-of [number]
  (reduce max (filter #(zero? (mod number %))
                      (take-while #(< % (sqrt number)) primes))))
```

Something new here:

* [max](http://clojuredocs.org/clojure_core/clojure.core/max) - function that returns the greatest number of its arguments.
* [zero?](http://clojuredocs.org/clojure_core/clojure.core/zero_q) - predicate that test whether number is equal to zero or not.
The same as `#(= 0 %)`.
* [sqrt](http://clojuredocs.org/clojure_contrib/clojure.contrib.math/sqrt) - function from `clojure.contrib.math`. Name is obvious.

Now, we want to find result. Run the line

``` clojure
(greatest-prime-of 600851475143))
```

**Congratulations!** Third problem solved.

[GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem003.clj) for lazy!

**P.S.** Instead of using `clojure.contrib.math` for square root we could just write `(Math/sqrt n)` and got rid of dependency.
But for me is better to reuse something that was implemented in library. It's tested and worked, where your implementation can miss some point.
If you have time, dig more into `clojure.contrib.math`. It needs about 9 minutes. There are only 9 functions,
but you will probably reinvent them all the time:
[expt](http://clojuredocs.org/clojure_contrib/clojure.contrib.math/expt),
[abs](http://clojuredocs.org/clojure_contrib/clojure.contrib.math/abs),
[sqrt](http://clojuredocs.org/clojure_contrib/clojure.contrib.math/sqrt),
[round](http://clojuredocs.org/clojure_contrib/clojure.contrib.math/round) and others.
