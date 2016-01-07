---
layout: post
title: "Code Golf: New Year Tree"
date: 2016-01-07 14:05
comments: true
categories: [programming, clojure, code golf]
sharing: true
published: true
---

Let's celebrate _geeky_ New Year by drawing New Year Tree using Clojure! 

<!-- more -->

# Problem

> Write a program which prints New Year Tree to console.  
> Source code should take as small number of characters as possible. 

I came up with the following tree rules:

![](http://i.imgur.com/LPJ1uVG.png)

- **Star.** There is should be a star on top of the tree.
- **Triangles.** Tree body is a set of triangle levels. Let the number of levels will be an input argument to the program. Next level height is a previous level height plus one.
- **Decorations.** Yellow squares is a possible place for decorations (_place 'o' instead of empty space_). Let them appear randomly with the specified probability (_[0, 1]_)
- **Stem.** And finally, every tree should have a stem at the bottom.

So, the function signature is following:

```clojure
(fn [level ratio])
```

# Code

``` clojure
(fn[l r](let[p print n println e range m(reduce + 2(e(+ l 1)))
f(fn[w n](dotimes[i n](p(if(fn? w)(w)w))))d #(f(fn[]
(if(<(rand)r)\o\ ))%)](f" "m)(n\★)(doseq[b(e l)h(e(+ b 3))
:let[u(quot(*(+ b 1)b)2)a(=(+ b 2)h)t(+ h h u u 1)]]
(f\ (- m h u 1))(p\/)(cond(and(=(+ b 1)l)a)(f"_"t)
a(do(p\_)(d(- t 2))(p\_))1(d t))(n\\))
(dotimes[i 2](f\ (- m 1))(n"| |"))))
```

354 characters in Clojure.

# Demo

[<img src="http://i.imgur.com/M0Xs2E0.gif" width="780" height="490" />](http://i.imgur.com/M0Xs2E0.gif)


**P.S.** Later I realized we could reuse _symmetry_ property of the tree to make program more compact.
