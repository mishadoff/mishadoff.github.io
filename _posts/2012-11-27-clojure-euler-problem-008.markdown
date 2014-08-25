---
layout: post
title: "Clojure Euler: Problem 008"
date: 2012-11-27 22:53
comments: true
categories: [clojure, programming, project-euler]
---

> Find the greatest product of five consecutive digits in the 1000-digit number.

Permalink: [http://projecteuler.net/problem=8](http://projecteuler.net/problem=8)

*Large number under the cut.*

<!-- more -->

```
73167176531330624919225119674426574742355349194934
96983520312774506326239578318016984801869478851843
85861560789112949495459501737958331952853208805511
12540698747158523863050715693290963295227443043557
66896648950445244523161731856403098711121722383113
62229893423380308135336276614282806444486645238749
30358907296290491560440772390713810515859307960866
70172427121883998797908792274921901699720888093776
65727333001053367881220235421809751254540594752243
52584907711670556013604839586446706324415722155397
53697817977846174064955149290862569321978468622482
83972241375657056057490261407972968652414535100474
82166370484403199890008895243450658541227588666881
16427171479924442928230863465674813919123162824586
17866458359124566529476545682848912883142607690042
24219022671055626321111109370544217506941658960408
07198403850962455444362981230987879927244284909188
84580156166097919133875499200524063689912560717606
05886116467109405077541002256983155200055935729725
71636269561882670428252483600823257530420752963450
```

Idea behind this problem is pretty straightforward:

1. Init `maximum` with some small value, zero would be good.
2. Take first 5 digits, calculate the product.
3. If product is greater than our current `maximum`, reassign it.
4. Shift, to take other 5 digits, and back to the step 1.

A simple optimization can be applied: if our last element of 5 digit group is zero,
then skip 5 shifts. But it's details.

Let's make our problem little harder to get more from clojure.

Assume that these digits stored in the file. First thing we need: to read the file.

``` clojure
(slurp "path/to/the/file")
```

[slurp](http://clojuredocs.org/clojure_core/clojure.core/slurp) is the simplest
function to read file content (*In fact, not only files*).
It just returns string representation of the whole file.

Then we need to break one large string into symbols.

``` clojure
(seq "helloyo") => (\h \e \l \l \o \y \o)
```

[seq](http://clojuredocs.org/clojure_core/clojure.core/seq) return a sequence with linear access
to collection. If applied to string, return sequence of characters.

But, there are some garbage in the file we don't want, new lines for example.

``` clojure
(remove #(= \newline %) [\h \e \l \l \o \newline \y \o])
```

\(*slash*) + something - represents character in clojure.
`\newline` is a new line, known as `\n` in other languages.

Now we have long sequence with all digits from file.
Need to get all consecutive groups with 5 letters.

``` clojure
(partition 5 1 [\h \e \l \l \o \y \o]) => [[\h \e \l \l \o] [\e \l \l \o \y] ... ]
```

[partition](http://clojuredocs.org/clojure_core/clojure.core/partition)
is an excelent function for such tasks. We specify `5` - size of partition,
and `1` shift size. It generates all consecutive groups with 5 elements till the end.

Calulating the product for each group gives us the solution.

``` clojure
(reduce * [1 2 3 4])
```

Not so fast.

You, probably, get `ClassCastException`.

After reading the file, we just get bunch of characters,
instead of digits in mathematical sense. And applied mathematical operation `*` to them.
So, simply speaking, `\1` is not `1`.

That's why we need to transform every digit character to it's actual digit value.

``` clojure
(map #(- (int %) 48) [\1 \2 \3]) => [1 2 3]
```

This awful implementation do the work.

* Cast each character to int, get the [ASCII](http://en.wikipedia.org/wiki/ASCII)
representation for digit.
* From each ASCII value we subtract `48` and get digit value.

Applying `product` of digits defined earlier we get solution to this problem.

Again, full code on [GitHub](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem008.clj).

**P.S.** `slurp` function very powerful and has may applications. You can specify encoding
for reading as you want or read whole html page by url. But it is also very dangerous function,
because reads all file contents to the memory and can take considerable amount of time. If you have
a large file, say 15GB, read it line by line with buffered reader. Also, note
[spit](http://clojuredocs.org/clojure_core/clojure.core/spit) function. It is opposite to `slurp` -
writes string to the file.
