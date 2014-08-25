---
layout: post
title: "Clojure Euler: Problem 014"
date: 2013-02-16 01:08
comments: true
categories: [clojure, programming, project-euler]
published: true
---

> Which starting number, under one million, produces the longest Collatz chain?

Permalink: [http://projecteuler.net/problem=14](http://projecteuler.net/problem=14)

<!-- more -->

> The following iterative sequence is defined for the set of positive integers:
>
> n → n/2 (n is even)
>
> n → 3n + 1 (n is odd)
>
> Using the rule above and starting with 13, we generate the following sequence:
>
> 13 → 40 → 20 → 10 → 5 → 16 → 8 → 4 → 2 → 1
>
> It can be seen that this sequence (starting at 13 and finishing at 1)
> contains 10 terms. Although it has not been proved yet (Collatz Problem),
> it is thought that all starting numbers finish at 1.
>
> Which starting number, under one million, produces the longest chain?
>
> NOTE: Once the chain starts the terms are allowed to go above one million.

Sequences again.
So, first of all we need function to detect next collatz number.
It is pretty straightforward:

``` clojure
(defn collatz-next [n]
  (if (even? n) (/ n 2) (inc (* n 3))))
```

Using this *next-element-generator* we can generate infinite collatz sequence,
starting from number `n`:

``` clojure
(iterate collatz-next n)
```

But instead of infinite sequence, we stop processing when we reach `1` and calculate
count of collatz chain sequence:

``` clojure
(defn collatz-chain-length [n]
  (inc (count (take-while #(> % 1) (iterate collatz-next n)))))
```

Now we have almost all code logic and just use it: calculate collatz chain
for all numbers in range `[1..1000000]`, find maximum and output its index:

``` clojure
(first (reduce #(if (> (second %1) (second %2)) %1 %2)
               (map #(list % (collatz-chain-length %)) (range 1 1000000))))
```

Run it. Wait a minute. I mean minute *literally*.

The time of caclulation is slightly greater than **one minute** on my machine,
and I have no idea how to solve it another way.

I am sure there is a better algorithm than mine. But I don't know it.
What I know is try to re-read problem definion if you are stuck. Especially that part:

> 13 → 40 → 20 → 10 → 5 → 16 → 8 → 4 → 2 → 1

So, for `13` the chain length is `10`, for `40` is `9`, for `20` is `8`,...

Got it?

Each time we calculate length we perform *repeated calculation*. If we know
chain length of `13`, and whole its path to `1` we don't need to calculate length
for `40`, `20` and so on.

So the optimization is *somehow to cache the results*. But at first we need to rewrite
it to have recursive solution.

Here the first (*and final*) version:

``` clojure
(defn collatz-chain-recursive [n]
  (if (= n 1) 1
      (inc (collatz-chain-recursive (collatz-next n)))))
```

Recursion is impressive at the point that its implementation
almost exactly follows the problem definition. It is easy to understand.

But, as you may notice our implementation not
[tail-recursive](http://en.wikipedia.org/wiki/Tail_call) so we can
have troubles. Do not think about it unless we actually have them.

For the sake of fun let's run this function instead of previous to
obtain the results.

**20 seconds**, it is great. I even don't think that
this simple recursive function gives such boost in performance.

One little tweak in result function:

``` clojure
(first
  (apply max-key second
         (map #(list % (collatz-chain-recursive %)) (range 1 1000000))))
```

And we have **~12 seconds**.

Unexpected "The End". Yeah, I love The Doors.

[code-code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem014.clj)

**P.S.** I really wanted to explain technique called
[memoization](http://en.wikipedia.org/wiki/Memoization) and
simple clojure function [memoize](http://clojuredocs.org/clojure_core/clojure.core/memoize),
but seems that caching result doesn't have much sense here because
12 seconds is acceptable. We will have chance to introduce
memoization later, where it will be more useful. By the way, I am *lazy*.
