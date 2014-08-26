---
layout: post
title: "Clojure Euler: Problem 004"
date: 2012-10-31 19:30
comments: true
sharing: true
categories: [clojure, programming, project-euler]
published: true
---

> A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 x 99.

> Find the largest palindrome made from the product of two 3-digit numbers.

Permalink: [http://projecteuler.net/problem=4](http://projecteuler.net/problem=4)

<!-- more -->

[Palindrome](http://en.wikipedia.org/wiki/Palindrome) is very interesting thing.
It's a sequence (of letters, numbers, words) that reads the same backwards. Examples: *MADAM*, *DID*, *2002*.
There are even some fun to compose palindrome sentences, like `He Goddam Mad Dog, eh?`

Function to check if sequence is palindromic is very simple.
Compare first element with the last, second with the penultimate and so on.
It needs just `n/2` comparisons, so complexity of that algorithm is `O(n)`. Here is recursive solution:

``` clojure
(defn palindrome-recursive? [s]
  (if (empty? s) true
    (let [a (first s) b (last s)]
      (if (= a b) (recur (rest (butlast s))) false))))
```

Note two functions: function [last](http://clojuredocs.org/clojure_core/clojure.core/last) - gets last element of collection,
[butlast](http://clojuredocs.org/clojure_core/clojure.core/butlast) - drops last element of collection.
Both they work in linear time, so you might assume overall complexity is `O(n^2)`.
Using vectors we can get index access, and perform test much faster, but this detail is not important for now.

Using more cleaner way (obviously not the best) to implement this, which in particular I prefer, is the following:

``` clojure
(defn palindrome? [s]
  (= s (reverse s)))
```

Function [reverse](http://clojuredocs.org/clojure_core/clojure.core/reverse), returns given sequence in reverse order in linear time.
Overall complexity is also `O(n)`.

To test whether number is palindrome or not, we just cast it to string, then cast string to sequence of chars, and using above function:

``` clojure
(defn palindrome-number? [n]
  (palindrome? (seq (str n))))
```

Final solution is following:

``` clojure
(reduce max (filter palindrome-number?
                      (for [i (range 100 1000) j (range i 1000)] (* i j))))
```

We just generate all combinations of 3-digit numbers multiplication, filter palindromes, and find maximum. Done.

This technique (*wow, is this technique?*) called [brute-force](http://en.wikipedia.org/wiki/Brute-force_search).
It's very consumable, because test each possible combination.
But it's common algorithm to such sort of problems.
There are lot of clever brute force optimization to reduce set of possible values, but it is still brute force.
As you see, in the code above, we also used small optimization.
We iterate first multiplicand `i` from `100` to `1000` to cover all 3-digits numbers, and `j` iterated not from `100` but from `i`.
This is because multiplication `375 * 468` gives the same result as `468 * 375`. Just because [commutative property](http://en.wikipedia.org/wiki/Commutative_property).
Roughly, this reduces number of iterations by factor of 2.
Obviously, there are other optimizations, but they are not so value and reduces readability
(*If first number ends with 5 and second is even, then result ends with 0, and we skip this because in palindromic value 0 should be at the beginning*).

**Congratulations!** 4 problems solved. And we know "so much" about clojure.

[GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem004.clj).

**P.S.** We create two separate functions for palindromes: one test sequence for palindrome,
second test number for palindrome and reuse first implementation. Some can say it's not good,
because we write functionality that we not needed (function `palindrome?`).
Particularly, I agree. We could hide `palindrome?` implementation inside `palindrome-number?` or make it private.
But we haven't did this and have function `palindrome?` that we can reuse in other problems. Also it shows important concept,
how we can reuse existing implementations to get new functionality. And again: maybe there is (*I'm sure there is*) another solution
which is more clever than bruteforce. But this solution works only `0.2` sec. on my machine, so this is ok.
