---
layout: post
title: "Clojure Euler: Problem 018"
date: 2013-05-17 00:26
comments: true
categories: [clojure, programming, project-euler]
---

> By starting at the top of the triangle below and moving to adjacent numbers on the row below, the maximum total from top to bottom is 23.

```
       3
      7 4
     2 4 6
    8 5 9 3
```

> That is, 3 + 7 + 4 + 9 = 23.
>
> Find the maximum total from top to bottom of the triangle below:
>
> [Check out big triangle in original link]
>
> NOTE: As there are only 16384 routes, it is possible to solve this problem by trying every route.
> However, Problem 67, is the same challenge with a triangle containing one-hundred rows;
> it cannot be solved by brute force, and requires a clever method! ;o)

Permalink: [http://projecteuler.net/problem=18](http://projecteuler.net/problem=18)

<!-- more -->

As note says we can try every route and it will be just good.
Instead of that, we can behave much better with clever method.

(Some time ago, I gave basic explanation on my approach in [StackOverflow question](http://stackoverflow.com/questions/8002252/euler-project-18-approach))

We have example triangle:

```
       3
      7 4
     2 4 6
    8 5 9 3
```

Instead of testing each route from top to the bottom, we moving from bottom
to the top and always select the best choice. As we want to find the max sum, we perform addition im place.

Assuming we on the 3rd line (2 4 6).

**From 2** we can go either 8 or 5, 8 is better (maximize your sum), then you calculate first
sum `8 + 2 = 10`.

**From 4** we can go either 5 or 9, 9 is better, `4 + 9 = 13`.

**From 6** we can go either 9 or 3, 9 is better, `9 + 6 = 15`.

Now, we removing last row, because we processed all routes and selected best ones, and replacing
3rd row with calculated sums. Current triangle looks like:

```
        3
      7  4
    10 13 15
```

Repeat the same action until we get one number, which will be solution.

Let's transform this idea to clojure.

First of all we need datastructure to represent triangle.
List of lists would be good:

``` clojure
(def triangle '((3) (7 4) (2 4 6) (8 5 9 3)))
```

Now we want to know, what direction generates the best sum.

``` clojure
(defn max-row [lst]
  (map #(reduce max %) (partition 2 1 lst)))
```

It breaks list to pairs, and select max from each pair.
For example, `[8 5 9 3]` we split to `[[8 5] [5 9] [9 3]]`
and select max from each sublist `[8 9 9]`.

Just add this list to previous one

``` clojure
(defn step-max [lst1 lst2]
  (map + (max-row lst1) lst2))
```

One iteration of `step-max` creates smaller triangle with sums on the bottom row.
But we need only one number.

Correct, `reduce` is ideal!

``` clojure
(reduce step-max (reverse triangle))
```

That's it.

[code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem018.clj)

**P.S.** Problem 067 can be solved with the same code.
