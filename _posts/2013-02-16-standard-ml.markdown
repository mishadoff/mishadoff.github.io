---
layout: post
title: "Standard ML"
date: 2013-02-16 13:33
comments: true
sharing: true
categories: [programming, fp, sml, coursera]
published: true
---

Last weekend I was hacking [Standard ML](http://en.wikipedia.org/wiki/Standard_ML) a bit.

<!-- more -->

### Coursera

There are excellent course on coursera called [Programming Languages](https://class.coursera.org/proglang-2012-001/class/index)

Currently, the first half is available, that explains basic and advanced functional programming constructions
with Standard ML as primary language. Later some idioms will be presented using
**Racket** and **Ruby** (*maybe, I will finally understand ruby*) so you can treat this course as introduction
to these languages. In fact, it teaches much more than "*how to code in XXX*".

### Tools

For playing with ML you need [SML/NJ](http://www.smlnj.org/) compiler and
[sml-mode](http://www.smlnj.org/doc/Emacs/sml-mode.html) for emacs.

*If you don't have emacs installed, do it right now!*

### Play

Following snippets give you basic understanding of ML or
refresh your knowledge for syntax.

Print "Hello, world!":

``` sml
fun print_hello_world () = print "Hello, world\n";
```

Variables (*in fact, values*):

``` sml
val a = 38;
val b = a + 3;
val c = b + 1;
```

Conditionals:

``` sml
val cond = if 5 > 4 then 5 else 4
```

Boolean operators have terrible names:

``` sml
val cond2 = if 5 > 4 andalso 4 > 5 then 5 else 7
```

Function with one parameter:

``` sml
fun square x = x * x
```

Function with more than one parameter:

``` sml
fun sum (x, y, z) = x + y + z
```

Actually, it's not three parameters, it's one parameter - tuple:

``` sml
fun swap (x, y) = (y, x)
```

To access tuple elements use hash-number, note you can specify tuple type:

``` sml
fun get_month (date : int * string * int) : string= (#2 date)
val month1 = get_month(2012, "January", 21);
```

You can specify input types and return type, but ML is very clever. Almost always
it can detect correct type using *type inference* without explicit definition.

*I don't understand it! Explain, please*. Comments:

``` sml
(* Operation '*' defined for int types, so this function has type int -> int *)
fun cube x = x * x * x
```

There is records. It's like named tuples.

``` sml
fun get_month_real ({year, month, day}) = month
val month2 = get_month_real({year=2012, month="January", day=21});
```

Using `let` form we can define new lexical scope:

``` sml
fun add3 x =
    let
        val num = 3
    in
        x + 3
    end
```

This leads to closures (*function + context*):

``` sml
fun add3 x =
    let
        val num = 3
        fun add_to x = x + num
    in
        add_to x
    end
```

Function `add_to` capture `num` value from context.

By the way functions are first-class objects and values also,
so we can assign one function to another:

``` sml
val another_cube = cube
```

Function composition becomes a piece of cake:

``` sml
val cube_and_cube = cube o cube
```

We even can pass function as a parameter:

``` sml
fun binary_apply (f, x, y) = f (x, y)
binary_apply(swap, 1, 2);
```

We don't forced to provide function name, we can use lambdas:

``` sml
binary_apply(fn (x, y) => x * y, 23, 46)
```

This helps us easily implement such useful functions as `map`, `reduce` and `filter`.
But they are already available in
[Standard ML Basis Library](http://www.standardml.org/Basis/manpages.html)


Another great feature is *pattern-matching*. Here is factorial function

``` sml
fun factorial x =
    case x of
        0 => 1
      | _ => x * factorial(x - 1)
```

or list concatenation

``` sml
fun concat (x, y) =
    case x of
        [] => y
      | x::xs => x::concat(xs, y)
```

Honestly, it's just baby examples of pattern matching. In fact, it is much more
powerful.

And the last thing I wanted to show is datatypes:

To define a list:

``` sml
datatype 'a list = EMPTY | CONS of 'a * 'a list
```

To create that list:

``` sml
val list = CONS(1, CONS(2, CONS(3, EMPTY)))
```

That's very small part of all that can be done in ML. There are also a lot of useful
constructs and idioms that not covered: type synonyms, polymorphic types,
equality types, tail recursion, mutual recursion, function wrapping,
currying, partial application, advanced pattern matching, signatures,
modules and lot more.

If you really interested you will find a lot of these terms exciting.

Full code is available on
[github](https://github.com/mishadoff/prog-experiment/blob/master/sml/play.sml)

### Impressions

ML is a great language. Perhaps, it is not so great as Java in enterprise, and not
so great as Clojure in rapid prototyping, but it's worth to devote
few days for ML basics. It really improve your dev skills and maybe made your
*Factories*, *Bridges* and *Proxies* better.
