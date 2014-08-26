---
layout: post
title: "Programming Digest 1"
date: 2013-02-12 02:00
comments: true
sharing: true
categories: [programming, digest]
published: true
---

How to get job at Google, estimate complexity of your algorithm,
what is *Structural Immutability*, why PHP is awful and Java is awesome, best programming jokes and lot more.

<!-- more -->

### 1. [Get that job at Google](http://steve-yegge.blogspot.com/2008/03/get-that-job-at-google.html)

> ...it's highly likely that someone on the loop will be unimpressed with you,
> even if you are Alan Turing. Especially if you're Alan Turing,
> in fact, since it means you obviously don't know C++

The quote accurately explains what this post about. The author gives great mental and
technical tips about interviewing process (not only in Google).
Big portion of motivation guaranteed, perhaps you man feel dumber after reading.

In few words, you must know: algorithm complexity, sorting, hashtables, trees,
graphs, other data structures, math, operating systems and at least one programming language very well.
Not bad, right? After reading that article I've build long-term learning plan for myself, which is still
not succesful. Great article.

### 2. [A Gentle Introduction to Algorithm Complexity Analysis](http://discrete.gr/complexity/)

This article helps you partially cover first thing from previous article: *algorithm complexity*.
By the way, it is written with understandable (*for programmers*) language, examples, pictures, excerpts - I like
such style.

After reading, you'll understand what time/space complexity is, difference between *Theta* and *Big-O* notations,
recursive functions complexity and lot more. By the way, you will learn basic classes of time complexities, and get
skill to classify that complexities on O(n), O(n^2 ), O(n*logn), etc. Note, the articles consists of some excercises, which immediately followed
with solutions. It is more productive to solve this exercises by yourselves and then compare to solutions.

If you already know all that, don't skip! It also helpful to fresh you knowledge.

### 3. [On Structural Immutability](http://clojurefun.wordpress.com/2013/01/21/on-structural-immutability/)

What is Structural Immutability?

We know difference between mutable and immutable data structures and their pros and cons.
Structural immutability means that you *can* change the contents of you data, but *can not* change their structure.
It gives better performance than plain immutable objects, but also keep your concurrent programs easier than with
full mutability. Plain old array is an example. Read it to know more.

### 4. [PHP: a fractal of bad design](http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/)

> Virtually every feature in PHP is broken somehow

Whole article is a criticism of bad PHP language design.

> PHP was originally designed explicitly for non-programmers (and, reading between the lines, non-programs)

Total destroy. I mean it - total.

> Array... This one datatype acts as a list, ordered hash, ordered set,
> sparse list, and occasionally some strange combination of those.
> How does it perform? What kind of memory use will there be?
> Who knows? Not like I have other options, anyway.

As I never know PHP, and hope won't, that article was one of the funniest things I ever read.

> equality operator is not transitive...comparison is not even consistent

If you PHPist don't go away, just read it. Maybe it will change your life.

> Genericism... There is none. If a function might need to do two slightly different things, PHP just has two functions.

Stop quoting. Just read it.

### 5. [Do Not Pass This Way Again](http://grimoire.ca/mysql/choose-something-else)

> Considering MySQL? Use something else. Already on MySQL? Migrate.

This article logically continues previous one, but for now critisism on MySQL.
Perhaps, it is not powerful as PHP version, but still worth to read.

### 6. [Big arrays in Java](http://www.omsn.de/blog/big-arrays-in-java)

Two articles of hate in a row, and currently Java? No way.
How much we hate Java so we love it. By the way, JDK8 is soon.

Maybe this post not so expressive as previous ones, but it reveals capability I never knew of.
How to create array in Java with size greater than `Integer.MAX_VALUE`. Use the undocumented
class `sun.misc.Unsafe`. Just note this only works in SunJDK and OpenJDK.

### 7. [State of the Lambda](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-4.html)

If we started about JDK8...

The most interesting feature set in expected JDK8 is Project Lambda.

Functional interfaces and functions without all that finals in lexical scope and anonymous classes,
common patterns for list processing like `map`, `reduce` and `filter`,
parallelism, streams, method references and lot more comming soon.

*What is the default method implementation in the interface?!*

### 8. [40+ Fantastic Game Development Tutorials From Across the Web](http://gamedev.tutsplus.com/articles/roundups/fantastic-gamedev-tutorials-from-across-the-web/)

Have you ever had the passion to write your own game?

I had. Because I like games and I even tried. Unfortunately, not so succesful as expected, but anyway.

> We need to go deeper

Honestly, it's not a joke that I post link to another digest. It gathers a lot of gamedev tutorials and if
you really interested in gamedev, you'll find a lot of there including game algorithms,
popular engine tutorials, math behind the games, design questions and more useful tips. Great stuff.

### 9. [How to pair socks from a pile efficiently?](http://stackoverflow.com/questions/14415881/how-to-pair-socks-from-a-pile-efficiently)

Bored to pair your socks every time? Learn how to do it efficiently.

At first glance it is a humorous question, indeed it is, but also it is an interesting real-world
problem with variety of algorithms can be applied. I think all algorithm courses should be teached
that way.

### 10. [What is your best programming joke](http://stackoverflow.com/questions/234075/what-is-your-best-programmer-joke/1284458)

> If you put a million monkeys at a million keyboards, one of them will eventually write a Java program.
>
> The rest of them will write Perl programs.

If you have an hour or two reserved for laughing, just read this. A **lot** of
crazy programming jokes. Stackoverflow community is amazing.
