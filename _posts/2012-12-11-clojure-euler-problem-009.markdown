---
layout: post
title: "Clojure Euler: Problem 009"
date: 2012-12-11 17:41
comments: true
categories: [clojure, programming, project-euler]
---

>  A Pythagorean triplet is a set of three natural numbers, a < b < c, for which, a^2 + b^2 = c^2
>
> For example, 3^2 + 4^2 = 9 + 16 = 25 = 5^2

> There exists exactly one Pythagorean triplet for which a + b + c = 1000.
> Find the product abc.

Permalink: [http://projecteuler.net/problem=9](http://projecteuler.net/problem=9)

<!-- more -->

Thinking about clever way to solve it...

Nothing good came up in your mind? Try bruteforce.

Again, the idea behind bruteforce for this problem to iterate over all possible values
for `a`, `b` and `c`, that sums to `1000` and compose Pythagorean triplet.
That's why we need predicate to test if three numbers compose triplet:

``` clojure
(defn is-triplet? [a b c]
  (= (+ (* a a) (* b b)) (* c c)))
```

Then our bruteforce solution looks like this:

``` clojure
(first (for [a (range 1 1000) b (range 1 1000) c (range 1 1000)
             :when (and (is-triplet? a b c) (= (+ a b c) 1000))] (* a b c)))
```

It finds the right solution, in ~45 seconds. Bad enough.

### Trick #1

Do not iterate on `c` variable, because knowing `a` and `b` we always
can calculate `c = 1000 - a - b`.

Problem solution transformed into next one:

``` clojure
(first (for [a (range 1 1000) b (range 1 1000)
             :let [c (- 1000 a b)]
             :when (is-triplet? a b c)] (* a b c)))
```

### Trick #2

Use rule `a < b < c` from problem definition. Then our iteration will be:

``` clojure
(for [a (range 1 1000) b (range a (- 1000 a))])
```

### Trick #3

`is-triplet?` predicate use 3 multiplications, addition and comparison.
We can add to `:when` section, predicate that compare `c` to `b`. This is also
slightly reduce number of `is-triplet?` execution.

``` clojure
:when (and (> c b) (is-triplet? a b c))
```

Now solution found in ~45 msecs. 1000 times faster. Not bad.

[(apply str (reverse "buHtiG"))](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem009.clj)

**P.S.** Unfortunately, there was nothing in this problem about new clojure
functions, programming and even problem was not challenging. But you see the way how
we solve it. We tried "bad" approach with knowing about all its disadvantages.
One more step and we improved it and got right solution. So it is not that bad
approach to try simple solution, even if it is wrong.
