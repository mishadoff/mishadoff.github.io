---
layout: post
title: "Clojure Euler: Problem 026"
date: 2017-05-09 12:19
comments: true
categories: [programming, clojure, project-euler]
sharing: true
published: true
---

> Find the value of d < 1000 for which 1/d contains the longest recurring cycle in its decimal fraction part.

Permalink: [https://projecteuler.net/problem=26](https://projecteuler.net/problem=26)

<!-- more -->

> 1/2 = 0.5  
> 1/3 = 0.(3)  
> 1/4 = 0.25  
> 1/5 = 0.2  
> 1/6 = 0.1(6)  
> 1/7 = 0.(142857)  
> 1/8 = 0.125  
> 1/9 = 0.(1)  
> 1/10 = 0.1  

As you see for `d` from `2` to `10`, the longest cycle length is `6`, for `d=7`.

Let's start with a function, which finds the cycle length for given `d`

``` clojure
(defn unit-fraction [denom]
  (loop [numer 1 i 1 known {}]
    (let [r (rem (* 10 numer) denom)]
      (cond (zero? r) 0
            (get known r) (- i (get known r))
            :else (recur r (inc i) (assoc known r i))))))
```

Optimal algorithm is a little bit tricky. 

In simplest case, we need to find a cycle for `1/4`. Calculate next decimal digit (we index digits number with `i` variable), by finding quotient. `1/4` does not produce integer number, so advance decimal index (result becomes `0.` something) and multiply 1 by 10. Quotient `10/4=2` and remainder is `2` (result becomes `0.2` something). Again `2/4` does not produce integer number, so advance index and multiply by 10. Quotient of `20/4=5`, *without* remainder, so whole result is equal to `0.25`, no cycle detected. 


For `d`, which contain cycles we introduce `known` map to store all previous numbers. Assume `d` is `11`, starting with remainder `1` we don't have integer result (result so far `0.`) and we store the fact, for `remainder=1`, `index=1`. Proceeding, we multiple remainder by 10, but `10/11` does not produce integer result as well (result so far `0.0`) and store `remainder=10, index=2`. Proceeding, For third index `100/11=9`, with remainder `1`. But remainder `1` has been seen before, at index `1`, so all subsequent calculation will produce the cycle. And cycle length is difference between current index and index seen before, `3-1=2`, and as result we have `0.(09)` with cycle length `2`.

Finally, just map this function to all denominators and find the one which will produce max result.

``` clojure
(->> (range 1 1000)
     (map #(vec [% (unit-fraction %)]))
     (apply max-key second)
     (first))
```

Easy.

[Code](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem026.clj)
