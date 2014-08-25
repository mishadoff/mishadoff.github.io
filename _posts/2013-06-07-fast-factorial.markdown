---
layout: post
title: "Fast Factorial"
date: 2013-06-07 01:08
comments: true
categories: [algorithms, clojure]
---

Factorial function is simple enough. But there is still some fun about it.
In [Stirling's Approximation](/blog/stirlings-approximation/) article
we've seen how to calculate good factorial approximation faster than *exact* value.
By the way, there is an algorithm to calculate *exact* value of factorial faster
than "by definition".

<!-- more -->

### Factorials

Most of programming languages tutorials shows the following
approach to calculate factorial (*transformed to clojure*):

``` clojure
(defn factorial [n]
  (if (zero? n) 1
      (* n (factorial (dec n)))))
```

Simple recursive solution.

*Note:* Since clojure allows using `!` character in function name, it is good name for factorial.

`(! 10)` works, `(! 20)` works, `(! 30)`...

```
> ArithmeticException integer overflow
```

Oops.

Default clojure integer type is 64 bit (`Long`).
It does not fit well for *such large input*, so we rewrite function.

``` clojure
(defn ! [n]
  (if (zero? n) 1
      (*' n (! (dec n)))))
```

Just one character added, `*` became `*'`. It is automatic type promotion.
If type can not hold the value without losing precision, it automatically extends.
In this case, `Long` becames `BigInteger` and all works fine.

`(! 30)` works, `(! 300)` works, `(! 3000)` works,
`(! 30000)`...

```
> StackOverflowError
```

Yet another overflow.

Well, stack size is limited. We don't need to consume stackframes and
will use `loop/recur` approach.

``` clojure
(defn ! [n]
  (loop [cur n acc 1]
    (if (zero? cur) acc
        (recur (dec cur) (*' cur acc)))))
```

It is also recursive approach, but instead of saving so far calculated value
in stack, we pass it to the function as a parameter.
Clojure optimize tail recursion to iterative version and does not consume stack.
It succesfully calculates factorial for 30000,
and for any other value. Great!

If we look at the factorial as a calculation process, not definition, it means
"product of all numbers between 1 and n". It is very concise for functional programming:

``` clojure
(defn ! [n]
  (reduce *' (range 1 (inc n))))
```

Does not consume stack, no number overflow, but works
~2 times slower than `loop/recur` version.

I don't know the reason, but it seems time wasted
by `reduce` for creating intermediate results. So we choose
previous `loop/recur` function as baseline factorial.

### Fast Factorial

Here is another way to calculate factorial.

Let's consider example `12!`.

Factorial is a product of numbers `12! = 1 * 2 * 3 * ... * 11 * 12`.
Perform prime factorization of each number.
For example, prime factorization of `12` is `2 * 2 * 3`.

After that we get a factorization of the `12!`, which contains only prime numbers.
To find factorial value we can just multiply them all. But we do clever trick instead.

Group every prime number and its power.
E.g. if we have factorization `2 * 2 * 2 * 3 * 5` it becomes vector of pairs `[[2 3] [3 1] [5 1]]`.

To calculate power we have simple `power` implementation:

``` clojure
(defn power [x n]
  (reduce *' (repeat n x)))
```

Trick is using exponentiation by squaring, which reduce
exponentiation complexity from `O(n)` to `O(log n)`.

``` clojure
(defn power [x n]
  (cond (= 0 n) 1
        (= 1 n) x
        (even? n) (power* (*' x x) (/ n 2))
        (odd? n) (*' x (power* (*' x x) (/ (dec n) 2)))))
```

It is not tail-recursive solution and theoretically may cause
stackoverflow, but its not critical. It works well for large numbers.

Actually, we do not perform factorization. We just know that factorization of `n!` contains all prime numbers below or equal `n` and every number have some multiplicity. 

The function calculates how many times prime number `k` occurs in factorial factorization for `n`

``` clojure
(defn- find-power [n k]
  (loop [total n sum 0]
    (let [i (int (/ total k))]
      (if (zero? i) sum
          (recur i (+ sum i))))))
```

Binding all together:

``` clojure
(defn !! [n]
  (loop [[h & t] 
         (map #(power % (find-power n %))
              (take-while #(<= % n) (primes)))
         acc 1]
    (if h (recur t (*' h acc)) acc)))
```

[Code available here](https://github.com/mishadoff/numberto/blob/master/src/numberto/factorial.clj)

*Note:* `primes` is a function generates lazy-sequence of prime numbers. It can be taken from `clojure.contrib.lazy-seqs`.

### Testing

> Anyway, why do you think it is faster?
> You performing more calculations than just multiplying numbers.

Let's see.

``` clojure
(do (println "== 10 ==")
    (time (! 10))
    (time (!! 10))
    (println "== 100 ==")
    (time (! 100))
    (time (!! 100))
    (println "== 1000 ==")
    (time (! 1000))
    (time (!! 1000))
    (println "== 10000 ==")
    (time (! 10000))
    (time (!! 10000))
    (println "== 100000 ==")
    (time (! 100000))
    (time (!! 100000))
    (println "== 1000000 ==")
    (time (! 1000000))
    (time (!! 1000000)) nil)
```

```
== 10 ==
"Elapsed time: 0.051054 msecs"
"Elapsed time: 0.137587 msecs"
== 100 ==
"Elapsed time: 0.081365 msecs"
"Elapsed time: 0.376653 msecs"
== 1000 ==
"Elapsed time: 1.59252 msecs"
"Elapsed time: 3.212842 msecs"
== 10000 ==
"Elapsed time: 161.856965 msecs"
"Elapsed time: 76.452529 msecs"
== 100000 ==
"Elapsed time: 19403.375319 msecs"
"Elapsed time: 6372.35266 msecs"
== 1000000 ==
"Elapsed time: 2863893.471718 msecs"
"Elapsed time: 1079632.255919 msecs"
nil
```

For small factorials `(< 1000)` improved version works ~2 times slower.
Around the thousand it has the same performance as standard version.
And, finally, some win (up to 3 times) for larger numbers.

Theoretically, you can implement generalized factorial with these two algorithms
and switch between them, depending on input. But who really need it? 

> Question: What the complexity of this algorithm?

**P.S.** Enterprise lovers would say factorial complexity is `O(1)`.
Just precompute all values and save them to database.
