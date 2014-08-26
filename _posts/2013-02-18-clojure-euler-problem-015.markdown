---
layout: post
title: "Clojure Euler: Problem 015"
date: 2013-02-19 00:38
comments: true
sharing: true
categories: [clojure, programming, project-euler]
published: true
---

> Starting in the top left corner of a 2 x 2 grid, there are 6 routes
> (without backtracking) to the bottom right corner.
>
> How many routes are there through a 20 x 20 grid?

Permalink: [http://projecteuler.net/problem=15](http://projecteuler.net/problem=15)

<!-- more -->

![](http://projecteuler.net/project/images/p_015.gif)

Picture is always a huge help to find correct the solution.
Let's see the pattern here, with another picture

*Warning:* If you don't understand art, don't look at that picture.

![](http://i.imgur.com/WNEQNxG.png)

It's a solution for 3 x 3 grid. We trying to process it by steps:

* At the start we located at the top left corner `[0, 0]`. There is just one case
to be at the start so we put `1` to that point.
* Then we move either down `[1, 0]` or right `[0, 1]`, and we put `1`
on the edges of the second diagonal line.
* Obviously, the point `[1, 1]` we can reach either from `[0, 1]` or from `[1, 0]`,
so there are two routes, and so on.

If you just output the numbers from each diagonal line you'll get:

```
       1
      1 1
     1 2 1
    1 3 3 1

```

It's famous [Pascal's triangle](http://en.wikipedia.org/wiki/Pascal%27s_triangle).
Each element in this triangle obtained by sum of two closest elements from the upper row.

To get desired result we need to sum these numbers again, until we get one number.
But it's also numbers from Pascal's triangle.

```
    1   3   3   1
      4   6   4
       10  10
         20
```

We can treat Pascal's triangle as a sequence, and each row is an element. Then the
following function calculates next row:

``` clojure
(defn routes-extend [lst]
  (let [size (count lst)]
    (for [i (range (inc size))]
      (if (or (= 0 i) (= size i)) 1
        (+ (nth lst (dec i)) (nth lst i))))))
```

Usage:

```
(routes-extend [1]) => [1 1]
(routes-extend [1 1]) => [1 2 1]
```

Now we can build infinite sequence of Pascal's triangle rows:

``` clojure
(iterate routes-extend [1])
```

For `3 x 3` grid we had 7 diagonals, each of them represented one row.
Take a guess. For `n x n` grid we need to get `2 * n + 1` row. And take it
middle element, which, of course, will be with the index `n`.

That's the final solution:

``` clojure
(let [n 20 d (inc (* n 2))]
  (nth (last (take d (iterate routes-extend [1]))) n))
```

Problem solved.

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem015.clj)

**P.S** Two points I want to admit here. First of all, drawing pictures
and trying to understand problem with pictures is a powerful technique that must be
used for every problem.

Second of all, you can see that our algorithm has complexity of `O(n^2)`. But ideal
solution is `O(1)` with usage [binomial coefficient](http://en.wikipedia.org/wiki/Binomial_coefficient)
`(2*n, n)`.
