---
layout: post
title: "Programming Digest 2"
date: 2013-04-22 14:43
comments: true
categories: [programming, digest]
published: true
---

Why math is needed for developers, what is REST, how to become experienced
developer...in 10 years, "coding it's just writing" and some practice:
Naive Bayes Classifier, functional programming and Java 8.

<!-- more -->

### 1. [Math For Programmers](http://steve-yegge.blogspot.com/2006/03/math-for-programmers.html)

Another great post by Steve Yegge about math. Math for developers.
As everyone might to retort, knowing math is not mandatory for software engineering field, but you know:

> Math is... ummm, please don't tell anyone I said this;
> I'll never get invited to another party as long as I live.
> But math, well... I'd better whisper this, so listen up: (it's actually kinda fun.)

I totally agree. It's a lot of fun.
Just read subreddit [math](http://www.reddit.com/r/math/), even if you don't understand anything.

Pick your favourite math subject and try to dig more.
Statistics, Linear Algebra, Discrete Math, Combinatorics, Operation Research, Group Theory
and more; one of them can be a good start.

Does that really helps? Well, maybe. But it's definitely improve your problem solving skill.

And believe. Math makes you a *better* programmer.

### 2. [How I Explained REST to My Wife](http://tomayko.com/writings/rest-to-my-wife)

Good article about how web works. I found it very useful for understanding
some parts I thought I understood. By the way, great examples.

> Ryan: I can't drive a cup and I can't drink a car. But some verbs are almost universal like GET, PUT, and DELETE.
>
> Wife: You can't DELETE a cup.
>
> Ryan: Well, okay, but you can throw it away.

Unfortunately, author of this article take it down due to lot of complaints about
article gender-oriented nature. I don't think it's offensive and here is the link with
[copy from web archive](http://web.archive.org/web/20130116005443/http://tomayko.com/writings/rest-to-my-wife)

### 3. [Programmer Competency Matrix](http://www.indiangeek.net/wp-content/uploads/Programmer%20competency%20matrix.htm)

Do you think you are great programmer? Test yourself.

**Algorithms**

* **2^n (Level 0)**. Unable to find the average of numbers in an array
(It's hard to believe but I've interviewed such candidates)
* **n^2 (Level 1)**. Basic sorting, searching and data structure traversal and retrieval algorithms
* **n (Level 2)**. Tree, Graph, simple greedy and divide and conquer algorithms, is able to understand
the relevance of the levels of this matrix.
* **log(n) (Level 3)**. Able to recognize and code dynamic programming solutions, good knowledge of graph algorithms,
good knowledge of numerical computation algorithms, able to identify NP problems etc.

In any case, this *test* helps you to build plan for programming
related skills and gives understanding what you need to know to become an expert.

### 4. [Teach Yourself Programming in Ten Years](http://norvig.com/21-days.html)

Article by Peter Norvig.
Great guy, I got to know from [Artificial Intelligence](https://www.ai-class.com/) course.

His writing is critique of popular books, like "Learn Java in 21 Days".
Peter explains that it is impossible to be a great
engineer in such short period, and to become an expert you need aproximately 10 years.

It reminds me a picture "How to become a C++ Developer in 21 Days":

![](http://i.imgur.com/3FckcgF.png)

If you totally disagree with his idea, just test yourself in previous article.

### 5. [On Being A Senior Engineer](http://www.kitchensoap.com/2012/10/25/on-being-a-senior-engineer/)

And logically continue. What behaviour differentiate senior engineer from "not senior" one?

> Mature engineers seek out constructive criticism of their designs.

Actually you could find more useful rules there and list of
**The Ten Commandments of Egoless Programming**.

> 1. Understand and accept that you will make mistakes.
> 2. You are not your code.
> 3. No matter how much “karate” you know, someone else will always know more.
> 4. Don’t rewrite code without consultation.
> 5. Treat people who know less than you with respect, deference, and patience.
> 6. The only constant in the world is change. Be open to it and accept it with a smile.
> 7. The only true authority stems from knowledge, not from position.
> 8. Fight for what you believe, but gracefully accept defeat.
> 9. Don’t be "the coder in the corner."
> 10. Critique code instead of people – be kind to the coder, not to the code.

Note, that article touch some psychological aspect of person. So I don't
think it can be 100% applied to everyone, but still worth to read.

### 6. [Coding: It's Just Writing](http://www.codinghorror.com/blog/2008/11/coding-its-just-writing.html)

Jeff Atwood explais basic the idea of:

> The unexpected relationship between writing code and writing.

To write good code, you must write good.

Article contains a reference to the book "The Elements of Style" with some quotes.

> Vigorous writing is concise. A sentence should contain no unnecessary words,
> a paragraph no unnecessary sentences, for the same reason that a drawing should
> have no unnecessary lines and a machine no unnecessary parts.
> This requires not that the writer make all his sentences short,
> or that he avoid all detail and treat his subjects only in outline, but that every word tell.

Read this book. Perhaps it is boring, it contains some advices to improve you writing skills.

### 7. [How To Build a Naive Bayes Classifier](https://www.bionicspirit.com/blog/2012/02/09/howto-build-naive-bayes-classifier.html)

Now some practice.

Article is great explanation of how Naive Bayes Classifier work.
With pictures, code examples, common pitfalls and good sense of humour.

Although, it's one of the simplest classification algorithms, but with some tweeks and
good train set it can give good results.

![](https://www.bionicspirit.com/assets/img/conditional-prob-9a27ee0e.png)

Here is the place where the math is needed. But article is so great,
that even monkey with banana (and degree in math) can understand how it works.

### 8. [Functors, Applicatives, And Monads In Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)

Number of articles that explain what is monad grow fast. Just another one.

Even if you don't understand what Haskell and monad is, look at the cool pictures.

![](http://adit.io/imgs/functors/fmap_just.png)

### 9. [List Out Of Lambda](http://stevelosh.com/blog/2013/03/list-out-of-lambda/)

Good introduction to functional programming. Not for beginners, I think.

> This blog post is a thought exercise.
> It’s not something you’d ever use for real code.
> But just like a guitarist practices scales that she won’t ever play in a song,
> we programmers should be exercising our brains every so often.

How to implement fancy list, map, filter features with just
first-class functions. Basic operations on numbers included. JavaScript.

### 10. [Everything about Java 8](http://www.techempower.com/blog/2013/03/26/everything-about-java-8/)

Let's review again what will be in Java 8. Most wanted release ever.

In this overview you will test other important changes, not only lambdas.

*Sad, that release postponed to early 2014 due to security issues.*
*But more time to play with developer preview features.*
