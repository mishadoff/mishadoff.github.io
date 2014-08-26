---
layout: post
title: "Numberto: Expressions"
date: 2014-03-29 00:25
comments: true
sharing: true
categories: [clojure, programming]
published: true
---

Numberto has new features!

<!-- more -->

In [previous post](/blog/announcing-numberto)
I wrote about simple clojure library [numberto](https://github.com/mishadoff/numberto) for experiments with numbers.

New version of *numberto* has a bunch of new features.

### Expressions

Expressions package provides two functions: `eval-infix` to evaluate infix expression
and `infix->prefix` to build prefix lisp-style expression from mathematical notation.

Let's give it alias for simplicity

``` clojure
(def e eval-infix)
```

Evaluate simple math expression

``` clojure
(e "1+2") => 3
```

or more complex

``` clojure
(e "1+2*(3-4/2)") => 3
```

handle priorities

``` clojure
(e "2+2*2") => 6
```

and left/right associativity

``` clojure
(e "1024/2/2/2/2") => 64
(e "2^3^4") => 2417851639229258349412352N
```

Oh, what's this? Long numbers? Sure, ratios and floats supported as well

``` clojure
(e "1/3") => 1/3
(e "1.1/0.9") => 1.2222222222222223
```

Unary operations

``` clojure
(e "(-1)^100") => 1
```

functions and symbols

``` clojure
(e "sin(e) + sqrt(pi)") => 2.183235141408425
```

vararg functions

``` clojure
(e "sum(1,2,3,sum())/max(1,2)") => 3
```

You can also provide bindings for unknown functions and symbols

``` clojure
(e "factorial(n)/20"
   {:bindings
     {"factorial" #(reduce *' (range 1 (inc %)))
      "n" 10}})
=> 181440
```

Worth to mention that you can easily redefine existing
or define your own new unary, binary operations, functions
and symbols. Just add additional properties to `eval-infix`

``` clojure
;; return current time in millis
(e "now()" {:bindings {"now" #(.getTime (java.util.Date.))}}) => some long number
;; override priorities
(e "1+2*3" {:binary-ops {"+" {:function + :priority 100}}}) => 9
```

`infix->prefix` has exactly the same functionality, but it builds prefix expression instead.

``` clojure
(infix->prefix "(1+2)*3-(4/avg(3,5)-sum(1))")
=>
"(- (* (+ 1 2) 3) (- (/ 4 (avg 3 5)) (sum 1)))"
```

It can be useful if you googled some formula but bored to translate it manually to clojure.

For example, take the [Simpson's rule](http://en.wikipedia.org/wiki/Simpson%27s_rule)

![](http://upload.wikimedia.org/math/1/a/0/1a0fb4456375307fdde8ab85954d95be.png)

``` clojure
(infix->prefix "(b-a)/6*(f(a)+4*f((a+b)/2)+f(b))")
=>
"(* (/ (- b a) 6) (+ (+ (f a) (* 4 (f (/ (+ a b) 2)))) (f b)))"
```

#### Implementation

Would be good to try [instaparse](https://github.com/Engelberg/instaparse) for such purpose,
but I decided to use custom implementation using standard
[Shunting-yard algorithm](http://en.wikipedia.org/wiki/Shunting-yard_algorithm).
Just couple of hacks added to handle unaries and vararg functions.
Code is awful. If you really want to dig in - run debug mode.

``` clojure
(binding [*DEBUG* true]
  (e "1+2"))
```

#### Limitations

- No way to define non-prefix unary operation, like "10!" or "|10|"
- Can not handle simplified math form, like "2x^2 + 3^x + 5".
Multiplication sign should be declared explicitly
- Not able to simplify ops in lisp-style expression. `(* (* 1 2) 3)` instead of `(* 1 2 3)`
- False positives handling. Some stupid expressions indicates about error `(e "1+")`, some not `(e "1+()1)`. I still working on this, but it is not critical.

### Solvers

Here is the puzzle:

> You have four numbers [3, 4, 5, 6].  
> You have four binary operations [+, -, *, /] and parentheses ()
>
> How to insert operations between numbers to get number 42?

Hah, that simple `3*4 + 5*6 = 42`

Ok, get `42`, but you forced to use one division `/`.

Not so obvious?

``` clojure
(solve-insert-ops-num [3 4 5 6] 42) =>
([42N "3+45-6"] [42N "3/4*56"] [42N "3*4+5*6"])
```

If you use `solve-insert-ops` function it gives all possible values can be obtained by inserting operations between numbers.

``` clojure
(solve-insert-ops [3 4 5 6]) => ;; long list
```

Default implementation uses 4 basic operations, no parens and no restrictions. Instead, you can override options

to use parens, specify level

``` clojure
(solve-insert-ops-num [3 4 5 6] 42 {:parens 1}) =>
([42N "3+45-6"] [42N "(3+45)-6"] [42N "3+(45-6)"]
 [42N "3/4*56"] [42N "(3/4)*56"] [42N "3/(4/56)"]
 [42N "3*4+5*6"] [42N "(3*4)+5*6"] [42N "3*4+(5*6)"])
```

limit some operations

``` clojure
(solve-insert-ops-num [3 4 5 6] 42 {:rules [[:max "*" 1]]}) =>
([42N "3+45-6"] [42N "3/4*56"])
```

`:max`, `:min`, `:max-in-a-row`, `:min-in-a-row` options are supported.

Add new operations (supported by expressions package)

``` clojure
(solve-insert-ops-num [3 4 5 6] 80
                      {:ops ["+" "-" "*" "/" "^"]
                       :rules [[:max "^" 1]]}) =>
([80N "3^4+5-6"])
```

Keep in mind, always limit time consuming operations (*like* `^`) as it builds all possible permutations and you can wait your answer forever.

There are also couple of *new* interesting things, like
getting digits of `pi`, `e`, `sqrt(n)`, ratio numbers up to desired level and other. [Check it out](https://github.com/mishadoff/numberto/blob/master/doc/intro.md)
