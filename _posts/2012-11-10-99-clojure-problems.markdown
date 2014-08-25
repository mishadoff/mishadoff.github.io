---
layout: post
title: "99 Clojure Problems"
date: 2012-11-10 16:20
comments: true
categories: [clojure, programming]
published: false
---

There is a popular article [99 Prolog Problems](https://sites.google.com/site/prologsite/prolog-problems) that describes
how [Prolog](http://en.wikipedia.org/wiki/Prolog) solves various small programming problems.
There are couple of remakes in [Lisp](http://www.ic.unicamp.br/~meidanis/courses/mc336/2006s2/funcional/L-99_Ninety-Nine_Lisp_Problems.html),
[Scala](http://aperiodic.net/phil/scala/s-99/), [Haskell](http://www.haskell.org/haskellwiki/H-99:_Ninety-Nine_Haskell_Problems)
and even half-complete in [Python](http://wiki.python.org/moin/ProblemSets/99%20Prolog%20Problems%20Solutions).
I found articles of such type extremely useful for programming language understanding,
as they show solution for common real-world problems.

In this article, codename **CLJ-99**, I provide my solutions to these problems,
reusing as much as possible standard `clojure.core` and `clojure.contrib` functions.

<!-- more -->

Some functions will be used with namespace qualifier e.g. `sets/union` and
corresponding required namespace can be viewed at the namespace requirements.

### Namespace requires

``` clojure
(:require [clojure.contrib.combinatorics :as comb])
(:require [clojure.set :as sets])
(:require [clojure.contrib.lazy-seqs :as lazy])
(:require [clojure.contrib.math :as math])
```

Solutions will be grouped by topic. To not reinvent wheel, I use the same groups as in original article.

Complexity introduced by stars: \* - simple, \*\* - medium, \*\*\* - hard.

Solution for problem is a function, called `p` plus `two digits number` representing number of problem.
E.g. `p03`, `p37`. Rarely problem will be splitted in two parts, like `p34-a`, `p34-b`.

Improvements are very welcome!

Ready? Go!

## Lists

### P01. (*) Find the last element of a list.

*Example:*
`(p01 [1 2 3 4]) => 4`

``` clojure
(defn p01 [lst]
  (last lst))
```

### P02. (*) Find the last but one element of a list.

*Example:*
`(p02 [1 2 3 4]) => 3`

``` clojure
(defn p02 [lst]
  (last (butlast lst)))
```

### P03. (*) Find the Kth element of a list.
The first element in the list is number 1.

*Example:*
`(p03 [1 2 3 4] 2) => 2`

``` clojure
(defn p03 [lst k]
  (nth lst (dec k)))
```

### P04. (*) Find the number of elements of a list.

*Example:*
`(p04 [1 2 3 4]) => 4`

``` clojure
(defn p04 [lst]
  (count lst))
```

### P05. (*) Reverse a list.

*Example:*
`(p05 [1 2 3 4]) => (4 3 2 1)`

``` clojure
(defn p05 [lst]
  (reverse lst))
```

### P06. (*) Find out whether a list is a palindrome.
A palindrome can be read forward or backward; e.g. [x a m a x]

*Example:*
`(p06 [1 2 1]) => true`

``` clojure
(defn p06 [lst]
  (= (reverse lst) lst))
```

### P07. (**) Flatten a nested list structure.
Transform a list, possibly holding lists as elements into a 'flat' list by
replacing each list with its elements (recursively).

*Example:*
`(p07 [1 [2 [3 4] 2] 1]) => (1 2 3 4 2 1)`

``` clojure
(defn p07 [lst]
  (flatten lst))
```

### P08. (**) Eliminate consecutive duplicates of list elements.
If a list contains repeated elements they should be replaced with
a single copy of the element. The order of the elements should not be changed.

*Example:*
`(p08 [1 1 1 1 2 3 3 1 1 4 5 5 5 5]) => (1 2 3 1 4 5)`

``` clojure
(defn p08 [lst]
  (map first (partition-by identity lst)))
```

### P09. (**) Pack consecutive duplicates of list elements into sublists.
If a list contains repeated elements they should be placed in separate sublists.

*Example:*
`(p09 [1 1 1 1 2 3 3 1 1 4 5 5 5 5]) = ((1 1 1 1) (2) (3 3) (1 1) (4) (5 5 5 5))`

``` clojure
(defn p09 [lst]
  (partition-by identity lst))
```

### P10. (*) Run-length encoding of a list.
Use the result of problem P09 to implement the so-called run-length encoding
data compression method. Consecutive duplicates of elements are encoded
as terms [N E] where N is the number of duplicates of the element E.

*Example:*
`(p10 [1 1 1 1 2 3 3 1 1 4 5 5 5 5]) => ((4 1) (1 2) (2 3) (2 1) (1 4) (4 5))`

``` clojure
(defn p10 [lst]
  (map (juxt count first) (p09 lst)))
```

### P11. (*) Modified run-length encoding.
Modify the result of problem P10 in such a way that if an element has
no duplicates it is simply copied into the result list.
Only elements with duplicates are transferred as [N E] terms.

*Example:*
`(p11 [1 1 1 1 2 3 3 1 1 4 5 5 5 5]) => ((4 1) 2 (2 3) (2 1) 4 (4 5))`

``` clojure
(defn p11 [lst]
  (map #(if (= 1 (first %)) (second %) %) (p10 lst)))
```

### P12. (**) Decode a run-length encoded list.
Given a run-length code list generated as specified in problem P11.
Construct its uncompressed version.

*Example:*
`(p12 [[4 1] 2 [2 3] [2 1] 4 [4 5]]) => (1 1 1 1 2 3 3 1 1 4 5 5 5 5)`

``` clojure
(defn p12 [lst]
  (flatten (map #(if (coll? %) (repeat (first %) (second %)) %) lst)))
```

### P13. (**) Run-length encoding of a list (direct solution).
Implement the so-called run-length encoding data compression method directly.
I.e. don't explicitly create the sublists containing the duplicates,
as in problem P09, but only count them. As in problem P11,
simplify the result list by replacing the singleton terms [1 X] by X.

*Example:*
`(p13 [1 1 1 1 2 3 3 1 1 4 5 5 5 5]) => ((4 1) (1 2) (2 3) (2 1) (1 4) (4 5))`

``` clojure
(defn p13 [lst]
  (loop [[h & t] lst prev (first lst) c 0 acc []]
    (if h (if (= prev h)
            (recur t prev (inc c) acc)
            (recur t h 1 (if (= c 1) (conj acc prev) (conj acc [c prev]))))
        (if (= c 1) (conj acc prev) (conj acc [c prev])))))
```

### P14. (*) Duplicate the elements of a list.

*Example:*
`(p14 [1 2 3 3 4]) => (1 1 2 2 3 3 3 3 4 4)`

``` clojure
(defn p14 [lst]
  (interleave lst lst))
```

### P15. (**) Duplicate the elements of a list a given number of times.

*Example:*
`(p15 [1 2 3] 3) => (1 1 1 2 2 2 3 3 3)`

``` clojure
(defn p15 [lst k]
  (mapcat #(repeat k %) lst))
```

### P16. (**) Drop every Nth element from a list.

*Example:*
`(p16 [1 2 3 4 5 6 7 8 9 0] 3) => (1 2 4 5 7 8 0)`

``` clojure
(defn p16 [lst n]
  (flatten (partition-all (dec n) n lst)))
```

### P17. (*) Split a list into two parts; the length of the first part is given.

*Example:*
`(p17 [1 2 3 4 5 6 7 8 9 0] 3) => ((1 2 3) (4 5 6 7 8 9 0))`

``` clojure
(defn p17 [lst n]
  (split-at n lst))
```

### P18. (**) Extract a slice from a list.
Given two indices, `N` and `K`, the slice is the list containing the elements
between the Nth and Kth element of the original list (both limits included).
Start counting the elements with 1.

*Example:*
`(p18 [1 2 3 4 5 6 7 8 9 0] 3 7) => (3 4 5 6 7)`

``` clojure
(defn p18 [lst n k]
  (take (inc (- k n)) (drop (dec n) lst)))
```

### P19. (**) Rotate a list N places to the left.

*Example:*
`(p19 [1 2 3 4 5 6 7] 3) => (4 5 6 7 1 2 3)`

*Example:*
`(p19 [1 2 3 4 5 6 7] -1) => (7 1 2 3 4 5 6)`

``` clojure
(defn p19 [lst n]
  (let [k (if (pos? n) n (+ n (count lst)))
        [a b] (split-at k lst)]
    (concat b a)))
```

### P20. (**) Remove the Kth element from a list.

*Example:*
`(p20 [1 2 3 4] 2) => (1 3 4)`

``` clojure
(defn p20 [lst k]
  (concat (take (dec k) lst) (drop k lst)))
```

### P21. (**) Insert an element at a given position into a list.

*Example:*
`(p21 100 [1 2 3 4] 2) => (1 100 2 3 4)`

``` clojure
(defn p21 [e lst k]
  (concat (take (dec k) lst) (list e) (drop (dec k) lst)))
```

### P22. (*) Create a list containing all integers within a given range.

*Example:*
`(p22 4 9) => (4 5 6 7 8 9)`

``` clojure
(defn p22 [a b]
  (range a (inc b)))
```

### P23. (**) Extract a given number of randomly selected elements from a list.
The selected items shall be put into a result list.

*Example:*
`(p23 [1 2 3 4 5 6 7 8] 3) => (3 7 2)`

``` clojure
(defn p23 [lst k]
  (take k (shuffle lst)))
```

### P24. (**) Lotto: Draw N different random numbers from the set 1..N
The selected numbers shall be put into a result list.

*Example:*
`(p24 6 49) => (23 1 17 33 21 27)`

``` clojure
(defn p24 [k n]
  (take k (shuffle (range 1 (inc n)))))
```

### P25. (*) Generate a random permutation of the elements of a list.

*Example:*
`(p25 [1 2 3 4 5 6]) => (3 6 1 5 2 4)`

``` clojure
(defn p25 [lst]
  (shuffle lst))
```

### P26. (**) Generate the combinations of K distinct objects chosen from the N elements of a list
In how many ways can a committee of 3 be chosen from a group of 12 people?
We all know that there are C(12,3) = 220 possibilities
(C(N,K) denotes the well-known binomial coefficients).
For pure mathematicians, this result may be great.
But we want to really generate all the possibilities (via backtracking).

*Example:*
`(p26 [1 2 3 4 5 6] 3) = [[1 2 3] [1 2 4] ... [4 5 6]]`

``` clojure
(defn p26 [lst k]
  (comb/combinations lst k))
```

### P27. (**) Group the elements of a set into disjoint subsets.
a) In how many ways can a group of 9 people work in 3 disjoint subgroups
of 2, 3 and 4 persons? Write a predicate that generates
all the possibilities via backtracking.

*Example:*
`(p27-a #{:a :b :c :d :e :f :g :h :i}) => [[#{:a :b} #{:c :d :e} #{:f :g :h :i}] ...]`

``` clojure
(defn p27-a [st]
  (for [fg (comb/combinations st 2)
        sg (comb/combinations (remove #(contains? (set fg) %) st) 3)
        :let [lg (remove #(contains? (sets/union (set sg) (set fg)) %) st)]]
    (map set [fg sg lg])))
```

b) Generalize the above predicate in a way that we can specify a list
of group sizes and the predicate will return a list of groups.

*Example:*
`(p27-b #{:a :b :c :d :e :f :g :h :i} [2 2 5]) => [[#{:a :b} {:c :d} {:e :f :g :h :i}] ...]`

**Note:** that we do not want permutations of the group members; i.e.
[[:a :b] ...] is the same solution as [[:b :a] ...].
However, we make a difference between [[:a :b] [:c :d] ...] and
[[:c :d] [:a :b] ...].

``` clojure
(defn p27-b [st groups]
  (let [n (count groups)
        f-rec (fn [st groups]
                (cond (= 1 (count groups)) [[st]]
                      :else (let [[g1 & gs] groups
                                  fg (comb/combinations st g1)]
                              (for [f fg]
                                (map #(cons (set f) %)
                                     (p27-b (sets/difference st (set f)) gs))))))]
    (partition n n (flatten (f-rec st groups)))))
```

### P28. (**) Sorting a list of lists according to length of sublists.

a) We suppose that a list contains elements that are lists themselves.
The objective is to sort the elements of top list according to their length.
E.g. short lists first, longer lists later, or vice versa.

*Example:*
`(p28-a [[:a :b :c] [:d :e] [:f :g :h] [:d :e] [:i :j :k :l] [:m :n] [:o]]) => [[:o] [:d :e] [:d :e] [:m :n] [:a :b :c] [:f :g :h] [:i :j :k :l]]`

``` clojure
(defn p28-a [lst]
  (reverse (sort-by count lst)))
```

b) Again, we suppose that a list contains elements that are lists themselves.
But this time the objective is to sort the elements of top list according
to their length frequency; i.e. in the default, where sorting is done ascendingly,
lists with rare lengths are placed first, others with a more frequent length come later.

*Example:*
`(p28-b [[:a :b :c] [:d :e] [:f :g :h] [:d :e] [:i :j :k :l] [:m :n] [:o]]) => [[:i :j :k :l] [:o] [:a :b :c] [:f :g :h] [:d :e] [:d :e] [:m :n]]`

``` clojure
(defn p28-b [lst]
  (let [freqs (frequencies (map count lst))]
    (sort-by #(get freqs (count %)) lst)))
```

## Arithmetic

### P29. (**) Determine whether a given integer number is prime.

*Example:*
`(p29 7) => true`

``` clojure
(defn p29 [n]
  (= n (first (drop-while #(< % n) lazy/primes))))
```

### P30. (**) Determine the prime factors of a given positive integer.
Construct a flat list containing the prime factors in ascending order.

*Example:*
`(p30 315) => [3 3 5 7]`

``` clojure
(defn p30 [n]
  (loop [[p & ps] lazy/primes num n fact []]
    (if (= 1 num) fact
        (if (zero? (mod num p)) (recur lazy/primes (/ num p) (conj fact p))
            (recur ps num fact)))))
```

### P31. (**) Determine the prime factors of a given positive integer (2).
Construct a list containing the prime factors and their multiplicity.

*Example:*
`(p31 315) => [[3 2] [5 1] [7 1]]`

``` clojure
(defn p31 [n]
  (map (juxt first count) (partition-by identity (p30 n))))
```

### P32. (*) A list of prime numbers.
Given a range of integers by its lower and upper limit, construct a list of all prime numbers in that range.

*Example:*
`(p32 10 20) => (11 13 17 19)`

``` clojure
(defn p32 [a b]
  (take-while #(<= % b) (drop-while #(< % a) lazy/primes)))
```

### P33. (**) Goldbach's conjecture.
Goldbach's conjecture says that every positive even number greater than 2 is the sum of two prime numbers.
Example: 28 = 5 + 23. It is one of the most famous facts in number theory that has not been proved
to be correct in the general case. It has been numerically confirmed up to very large numbers.
Write a predicate to find the two prime numbers that sum up to a given even integer.

*Example:*
`(p33 28) => [5 23]`

``` clojure
(defn p33 [n]
  (let [ps (take-while #(< % n) lazy/primes)]
    (first (for [i ps j ps :when (= (+ i j) n)]
             [i j]))))
```

### P34. (**) A list of Goldbach compositions.
a) Given a range of integers by its lower and upper limit, print a list of all even numbers
and their Goldbach composition.

*Example:*
`(p34-a 9 20) => [[10 3 7] [12 5 7] [14 3 11] [16 3 13] [18 5 13] [20 3 17]]`

```clojure
(defn p34-a [a b]
  (for [i (range a (inc b)) :when (even? i)]
    (cons i (p33 i))))
```

b) In most cases, if an even number is written as the sum of two prime numbers,
one of them is very small. Very rarely, the primes are both bigger than say 50.
Try to find out how many such cases there are in the range 3..2000.

*Example:*
`(p34-b 3 2000 50) => [[992 73 919] [1382 61 1321] [1856 67 1789] [1928 61 1867]]`

```clojure
(defn p34-b [a b limit]
  (for [[q w e] (p34-a a b) :when (and (> w limit) (> e limit))]
    [q w e]))
```

### P35. (**) Determine the greatest common divisor of two positive integer numbers.

*Example:*
`(p35 36 63) => 9`

```clojure
(defn p35 [a b]
  (math/gcd a b))
```

### P36. (**) Determine whether two positive integer numbers are coprime.
Two numbers are coprime if their greatest common divisor equals 1.

*Example:*
`(p36 35 64) => true`

```clojure
(defn p36 [a b]
  (= 1 (p35 a b)))
```

### P37. (**) Calculate Euler's totient function phi(m).
Euler's so-called totient function `phi(m)` is defined as the number of positive integers
`r` in range `1 <= r < m` that are coprime to `m`.

*Example:*
`(p37 10) => 4`

```clojure
(defn p37 [m]
  (reduce + (for [r (range 1 m)]
              (if (p36 r m) 1 0))))
```

### P38. (**) Calculate Euler's totient function phi(m). (2)
See problem P37 for the definition of Euler's totient function.
If the list of the prime factors of a number m is known in the form of problem P31
then the function `phi(m)` can be efficiently calculated as follows:

Let [[p1 m1] [p2 m2] [p3 m3] ...] be the list of prime factors (and their multiplicities)
of a given number `m`. Then `phi(m)` can be calculated with the following formula:

`phi(m) = (p1 - 1) * p1^(m1 - 1) * (p2 - 1) * p2^(m2 - 1) * (p3 - 1) * p3^(m3 - 1) * ...`

*Example:*
`(p38 10) => 4`

```clojure
(defn p38 [m]
  (reduce * (for [[p m] (p31 m)]
              (* (dec p) (math/expt p (dec m))))))
```

### P39. (**) Compare the two methods of calculating Euler's totient function.
Compare result and execution time.

```clojure
(defn p39 [m]
  (let []
    (println "1st implementation: p37 =>" (time (p37 m)))
    (println "2nd implementation: p38 =>" (time (p38 m)))))
```

## Logic and Codes

### P40. (**) Truth tables for logical expressions.
Define binary predicates `and`, `or`, `nand`, `nor`, `xor`, `impl` and `equ` (for logical equivalence)
and build truth table for passed expression.
