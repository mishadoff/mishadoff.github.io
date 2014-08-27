---
layout: post
title: "Clojure Euler: Problem 024"
date: 2014-08-28 00:43
comments: true
sharing: true
categories: [programming, clojure, project-euler]
published: true
---

> A permutation is an ordered arrangement of objects.
> For example, 3124 is one possible permutation of the digits 1, 2, 3 and 4.
> If all of the permutations are listed numerically or alphabetically, we call it lexicographic order.
> The lexicographic permutations of 0, 1 and 2 are:
> 
> 012   021   102   120   201   210
>
> What is the millionth lexicographic permutation of the digits 0, 1, 2, 3, 4, 5, 6, 7, 8 and 9?

Permalink: [http://projecteuler.net/problem=24](http://projecteuler.net/problem=24)

<!-- more -->

Instead of developing permutation function we use [math.combinatorics](https://github.com/clojure/math.combinatorics)

`permutations` function does the trick

``` clojure
(->> (range 10)
     (comb/permutations)
     (drop (dec 1000000))
     (first)
     (apply str)))
```

That was easy! It returns result in just 3 seconds, what is acceptable.

If you need to solve this problem faster, observe the interesting properties:

```
1      - 0123456789
2      - 0123456798
...
9!     - 0987654321
9!+1   - 1023456789
...
2*9!   - 1987654320
2*9!+1 - 2013456789
...
3*9!   - 2987654310
3*9!+1 - 3012456789
```

`3*9!` is greater than `1000000`, so we stop on the `2*9!` permuations and guessed first number, it's `2`

Proceed the same way and you've got the idea. On each factorial permutation step, when you guessed the number remove it from further permutations and decrement the factorial

``` clojure
(let [! (fn [n] (reduce * (range 1 (inc n))))]
  (loop [available-digits (range 10)
         num 1000000
         current-digit 0
         init 9
         result []]
    (let [f (! init)]
      (cond
        (= 0 init)
        (apply str (concat result available-digits))
         
        (< f num) (recur available-digits
                         (- num f)
                         (inc current-digit)
                         init
                         result)

        :else (recur (concat (take current-digit available-digits)
                             (drop (inc current-digit) available-digits))
                     num
                     0
                     (dec init)
                     (conj result (nth available-digits current-digit)))))))
```

Solution is less readable, but time is just `5 msecs`. There is always tradeoff.

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem024.clj)
