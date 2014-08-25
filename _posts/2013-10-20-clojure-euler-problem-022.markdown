---
layout: post
title: "Clojure Euler: Problem 022"
date: 2013-10-20 13:22
comments: true
categories: [programming, clojure, project-euler]
published: true
---

> Using [names.txt](http://projecteuler.net/project/names.txt)
> a 46K text file containing over five-thousand first names,
> begin by sorting it into alphabetical order. Then working out the alphabetical
> value for each name, multiply this value by its alphabetical
> position in the list to obtain a name score.
>
> For example, when the list is sorted into alphabetical order,
> COLIN, which is worth 3 + 15 + 12 + 9 + 14 = 53, is the 938th name in the list.
> So, COLIN would obtain a score of 938 x 53 = 49714.
>
> What is the total of all the name scores in the file?

Permalink: [http://projecteuler.net/problem=22](http://projecteuler.net/problem=22)

<!-- more -->

Problem is very easy, assuming you know how to work with files.

First of all read the file, clojure has awesome simple function `slurp`, discussed in [Clojure Euler: Problem 008](/blog/clojure-euler-problem-008).

``` clojure
(slurp "names.txt")
```

Then, you need to select all words in that file:

``` clojure
(re-seq #"\w+" (slurp "names.txt"))
```

It return a list with the names. How that simple, yeah?

Next two steps are:

- sort list in ascending order, what is done by calling `sort` function
- calculate score for each word

By the way, `score` consists of two parts. First of all, you must calculate natural score of the word, without its actual position in sorted list, and then you must multiply that score on its position.

First part looks like this:

``` clojure
(defn score [string]
  (reduce + (map #(- (int %) 64) string)))
```

Convert each character to its positional number in alphabet. For example the character "A" (*capitalized*) has ascii value of `65`. If we substract `64` it becomes `1`, what means "A" is a first letter in alphabet and so on.

To track positions in list we use
[map-indexed](http://clojuredocs.org/clojure_core/clojure.core/map-indexed)
function. It works almost the same way as map, except it accept function of two arguments, index of item in current list, and item itself.

For example:

``` clojure
(map-indexed #(vec [%1 %2]) ["a" "b" "c"]) => ([0 "a"] [1 "b"] [2 "c"])
```
As indices start from zero, do not forget increment!

Bind all together

``` clojure
(reduce +
        (map-indexed #(* (inc %1) (score %2)) 
                     (sort (re-seq #"\w+" (slurp "names.txt"))))))
```

Solved! [Code is here](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem022.clj)

*P.S.* Actually, this code is not elegant. We need to read from the end to the beginning of expression. And it can be solved by Threading Macro. Read the nice **Fogus** [explanation](http://blog.fogus.me/2009/09/04/understanding-the-clojure-macro/)

Our last solution, becomes transformed to:

``` clojure
(->> "names.txt"
	(slurp)
	(re-seq #"\w+")
	(sort)
	(map-indexed #(* (inc %1) (score %2)))
	(reduce +))
```

Way more readable, huh?
