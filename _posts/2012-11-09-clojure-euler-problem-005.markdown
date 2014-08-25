---
layout: post
title: "Clojure Euler: Problem 005"
date: 2012-11-09 19:01
comments: true
categories: [clojure, programming, project-euler]
published: true
---

> 2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder.

> What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?

Permalink: [http://projecteuler.net/problem=5](http://projecteuler.net/problem=5)

<!-- more -->

It's a tricky problem. First look gives us straightforward solution:
Take a number, test if it evenly divisible by all of needed numbers, if yes - we good,
otherwise increment number and test again, and again, and again...

No, really, assume it's our function to test divisibility by all 20 numbers:

``` clojure
(defn evenly-divisible-by-20? [n]
  (zero? (reduce +
    (for [i (range 2 21)]
      (mod n i)))))
```

Awful implementation, but keeps the idea.
If sum of all remainders equals to zero, then every remainder equals to zero.
Now, using this function we write naive implementation:

``` clojure
    (first
      (drop-while #(not (evenly-divisible-by-20? %)) (iterate inc 1)))
```

Note, function [drop-while](http://clojuredocs.org/clojure_core/clojure.core/drop-while)
similar to take-while, but skips values until they meet predicate condition.

Waiting for result? Take a cup of tea and wait a little. Still waiting? Wait a little more...

I did not post exact answer here, but result greater than 100 millions.
And obviously this linear algorithm will work considerable amount of time.
So, stop the execution and let's see at the problem from the other end.

*What is the smallest number that evenly divisible by 1 and 2?*
Obviously 2.

*What is the smallest number that evenly divisible by 2 and 3?*
A bit harder, but answer is 6.

*What is the smallest number that evenly divisible by 4 and 5?*
20.

> Oh, it's just multiplication

Unfortunately, no. Take numbers `4` and `6`, multiplication gives us `24` and it will be evenly divisible by `4` and `6`, but
this number is not the *smallest*. Snmallest number for this case will be `12`.

There is a function, that finds smallest number divisible by 2 different numbers.
This function called [Least Common Multiple](http://en.wikipedia.org/wiki/Least_common_multiple) (*lcm*).
There are lot of different methods to calculate it, but we will use one that uses
[Greatest Common Divisor](http://en.wikipedia.org/wiki/Greatest_common_divisor) (*gcd*)

![](http://upload.wikimedia.org/math/5/b/3/5b3f3c62dd59cc5594af7b2ece3798fb.png)

Where *gcd* calculated according to [Euclid's algorithm](http://en.wikipedia.org/wiki/Euclidean_algorithm)

``` clojure
(defn gcd [n k]
  (loop [a n b k]
    (if (zero? b) a (recur b (mod a b)))))
```

Using formula above, our *lcm* function will look like this:

``` clojure
(defn lcm [n k]
  (/ (abs (* n k)) (gcd n k)))
```

You see, here we need `abs` function. Nothing hard to implement it, but if we remember
[previous problem](/blog/clojure-euler-problem-004) there are was a reference
to `clojure.contrib.math` library. `abs` there and we can use it, just add to your namespace definition

``` clojure
(:use [clojure.contrib.math])
```

Oh look, even `lcm` and `gcd` functions there. Excellent.

Now we can solve the problem. Remember the `reduce` function?

``` clojure
(reduce lcm (range 1 21))
```

Actually, this one-liner is solution. And it gives us correct result.

What happens here? We iteratively (*with using reduce*) find the lcm for `1` and `2`,
then for result and `3`, for result and `4` and so on.

**Great!** One more problem solved!

[GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem005.clj)

**P.S.** One lesson we learned here. Be aware about library functionality. It helps you
to solve problems and avoid coward mistakes. For example, we have incorrect above implementation
for `lcm` function. If we call `(lcm 0 0)` program fails with `DivideByZero` exception.
It still solve our problem, but in future uses can be an additional problem.
But in lib implementation this error is covered. And we can be sure that *almost always* libs contain much less
errors that your *new fancy wheel*.
