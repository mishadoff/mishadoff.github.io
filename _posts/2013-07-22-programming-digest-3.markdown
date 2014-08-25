---
layout: post
title: "Programming Digest 3"
date: 2013-07-22 20:33
comments: true
categories: [programming, digest]
published: true
---

Why your software sucks and what is the "Secret Weapon". Also,
algorithm complexities refresher, functional programming, including
Scala and Clojure, concurrency and lot of humor.

<!-- more -->

### 1. [Big Ball of Mud](http://www.laputan.org/mud/)

> What does your programming day look like? 

![](http://www.laputan.org/images/pictures/spaghetti-medium.jpg)

The article describes problems in software design and explains
why almost every system nowadays look like "Big Ball of Mud".

There are seven *real* patterns in software design:

* BIG BALL OF MUD
* THROWAWAY CODE
* PIECEMEAL GROWTH
* KEEP IT WORKING
* SHEARING LAYERS
* SWEEPING IT UNDER THE RUG
* RECONSTRUCTION 

*caps preserved due to original article*

And our favorite phrase:

> It works, so why fix it?

### 2. [No Silver Bullet](http://www.cs.nott.ac.uk/~cah/G51ISS/Documents/NoSilverBullet.html)

Classic article by Frederick Brooks, Jr.

Object-oriented programming? Artificial Intelligence? Expert systems? Time-sharing?
Graphical or Automated programming?

Nothing helps you.

> The most radical possible solution for constructing software is not to construct it at all.

Demotivating, but helpful.

### 3. [Beating the Averages](http://www.paulgraham.com/avg.html)

This is inspiring one.

Brooks told "There is no Silver Bullet", but Paul Graham reveals "The Secret Weapon".

The article is success story of startup called Viaweb.
Chosen technologies were so good, that allowed Viaweb to compete with all similar startups.

> Our secret weapon was similar.
> We wrote our software in a weird AI language, with a bizarre syntax full of parentheses.

As you probably understood, it was Lisp.

### 4. [Know Thy Complexities!](http://bigocheatsheet.com/)

Plain algorithm complexity cheatsheet. Contains time and space complexities
for common algorithms and datastructures.

### 5. [Adventures in Functional Programming](http://vimeo.com/45140590)

> Did you know that recursion is just syntactic sugar over higher-order functions?

Awesome screencast by Jim Weirich about Y-Combinator in JavaScript.

### 6. [99 Scala Problems](http://aperiodic.net/phil/scala/s-99/)

I am not using Scala and prefer Clojure for rapid protyping, but find such articles
extremely useful when learning new language. Just exploring the code shows that Scala is
the powerful and expressive language.

**P.S.** I am on half way to publish "99 Clojure Problems".

### 7. [Locks, Actors, And STM In Pictures](http://adit.io/posts/2013-05-15-Locks,-Actors,-And-STM-In-Pictures.html)

Great pictures. This is nice intro to concurrency and overview of some common cocurrency problems.

![](http://adit.io/imgs/concurrency/threads_using_mutex.png)

### 8. [Understanding Clojure Concurrency](http://blakesmith.me/2012/05/15/understanding-clojure-concurrency-part-1.html)

Clojure is exciting language with great concurrency capabilities:
default immutability, atoms, refs, agents, promises, futures and software transactional memory.

This article is an introduction to them. Check out the [2nd part](http://blakesmith.me/2012/05/25/understanding-clojure-concurrency-part-2.html)

### 9. [Wat](https://www.destroyallsoftware.com/talks/wat)

This is the video screencast of Gary Bernhardt's talk.
Sarcastic and great presentation of weird Ruby and JavaScript behaviour.

> An object plus an object is actually.... Not A Number!

### 10. [What is the most intellectual joke you know](http://www.reddit.com/r/AskReddit/comments/1h1cyg/whats_the_most_intellectual_joke_you_know/)

We touch humorous side and this article (*reddit thread*) not about programming, just jokes.

> Q: What does the "B" in Benoit B. Mandelbrot stand for?
>
> A: Benoit B. Mandelbrot.

In fact, some of the jokes related to programming.

> There are only two hard things in Computer Science: cache invalidation, naming things, and off-by-one errors.

Some of them a bit stupid, but still fun.

> How many surrealists does it take to screw in a light bulb?
>
> A fish.

Definitely, worth to read!
