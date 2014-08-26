---
layout: post
title: "Programming Digest 4"
date: 2013-11-25 22:50
comments: true
sharing: true
categories: [programming, digest]
---

How to live in the Kingdom of Nouns, why mathematicians cry, filling gaps in algorithms, portion of clojure, STM, HTTP and more.

<!-- more -->

### 1. [Execution in the Kingdom of Nouns](http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html)

Another great Steve Yegge's post. Not a post, a tale about *Javaland*, a place where all verbs are owned by nouns. Accept it like an analogy between OOP and FP.

> Verbs in Javaland are responsible for all the work, but as they are held
> in contempt by all, no Verb is ever permitted to wander about freely.
> If a Verb is to be seen in public at all,
> it must be escorted at all times by a Noun.

Of course *to be escorted* must be replaced by **VerbEscorter**.

> The Verb "execute", and its synonymous cousins "run", "start", "go",
> "justDoIt", "makeItSo", and the like, can perform the work of any
> other Verb by replacing it with an appropriate Executioner and a
> call to execute(). Need to wait? Waiter.execute().
> Brush your teeth? ToothBrusher(myTeeth).go().
> Take out the garbage? TrashDisposalPlanExecutor.doIt().
> No Verb is safe; all can be replaced by a Noun on the run.

You are not forced to use functions everywhere, but use them where it is appropriate to simplify solution and avoid [enterprise hell](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition). You know, not everything is an object. The same way not everything is a function. 

### 2. [A Mathematician's Lament](http://www.maa.org/external_archive/devlin/LockhartsLament.pdf)

Long paper by Paul Lockhart about problems in mathematical education.

> Math class is stupid and boring. - Students.

Author claims that mathematics is an art, instead of some practical thing. Of course, it can be applied in real world and it is, but it's not primary goal of math.

> A mathematician, like a painter or poet is a maker
> of patterns. If his patterns are more permanent than theirs,
> it is because they are made wit *ideas*.

Very exciting reading.

### 3. [Algorithms](http://cstheory.stackexchange.com/questions/19759/core-algorithms-deployed/19773#19773)

Algorithms are not just devils you need to learn for CS course or to prepare for interview. You can *apply* them to solve real-world problems. This SO answer provides links and some explanations where specific algorithm is used in linux kernel, chromium browser and some utils.

> ...
> Red-Black trees are used for scheduling, virtual memory management,
> to track file descriptors and directory entries, etc.

> grep, implements the Boyer-Moore algorithm

> fgrep implements the Aho-Corasick string matching algorithm.
> ...

Even Bubblesort has its [application](https://github.com/mirrors/linux-2.6/blob/b3a3a9c441e2c8f6b6760de9331023a7906a4ac6/drivers/media/common/saa7146/saa7146_hlp.c)!

### 4. [Rich Hickey's Greatest Hits](http://thechangelog.com/rich-hickeys-greatest-hits/)

I'm huge Rich Hickey's fan. It's just a list of his most interesting presentations. Definitely, must see.

![](http://farm4.staticflickr.com/3229/2613013337_bcc9c7857d.jpg)

* [Are We There Yet (2009)](http://www.infoq.com/presentations/Are-We-There-Yet-Rich-Hickey)
* [Hammock Driven Development (2010)](http://www.youtube.com/watch?v=f84n5oFoZBc)
* [Simple Made Easy (2011)](http://www.infoq.com/presentations/Simple-Made-Easy)
* [The Value of Values (2012)](http://www.infoq.com/presentations/Value-Values)
* [The Language of the System (2012)](http://www.youtube.com/watch?v=ROor6_NGIWU)
* [Design, Composition and Performance (2013)](http://www.infoq.com/presentations/Design-Composition-Performance)

### 5. [Clojure Persistent Vector](http://hypirion.com/musings/understanding-persistent-vector-pt-1)

Two parts explanation how clojure vector works.

Yeah, simple square brackets `[1 2 3]` in clojure have a lot complex things under the hood. Moreover, it gives understanding not only how vectors work, but how all other persistent structures can be implemeted in efficient manner.

### 6. [Software Transactional Memory](http://java.ociweb.com/mark/stm/article.html)

Have you heard about STM?

It's a good model for concurrency control, like database transactions, but on memory. Much simpler and less error-prone to use. This article explain STM basics with some implementation details. 

For my attempt to implement STM refer [presentation](http://www.slideshare.net/mishadoff/implementing-stm-in-java)

### 7. [A Software Developer's Huide to HTTP](http://odetocode.com/articles/741.aspx)

Five Parts article of looking at HTTP from developer's perspective. Worth reading to dig into some details. 

### 8. [100 Ideas for Computing](https://github.com/samsquire/ideas)

Great list, perhaps most ideas are already partially implemented.

> This is a vision for how computing could be.
> I began writing ideas down a number of years ago,
> this is a selection of ideas old and new.

Some of them are [great](https://github.com/samsquire/ideas#68-right-click-outsource):

> Task management between computers is an unsolved problem in desktop
> environments. Many users have multiple devices. Sending tasks,
> inputs or outputs to different computers is painful. Usually sending
> audio to be played through another computer, taking control of an
> application running on another computer, sharing files or
> streaming data to other machines requires special software. 

It would be great to have the same in reverse order also. I get a phone call, but phone is far away. Much better to click on current machine *Accept Call* and to talk. Security can be a challenging.

Some [problematic](https://github.com/samsquire/ideas#18-shortcut-format):

> Keyboard shortcuts are configured differently in each program.
> Shortcut configuration should be in a standard format.
> This way you can load them and share them between operating
> systems and share configurations between applications.

This reminds me of xkcd comics:

![](http://imgs.xkcd.com/comics/standards.png)

Some ideas are [not so great](https://github.com/samsquire/ideas#31-mounting-source-code):

*Mounting source code* does not solve a problem at all, it's just adds a bit of sugar to project structure and adds more pain to build tools.

### 9. [Top 10 Lists Common Java Mistakes](http://java.dzone.com/articles/top-10-lists-common-java)

Metalist of Top 10 items of Top 10 java pitfalls.

Hundred times discussed, but if you are java programmer, you may refresh your knowledge. List of common java pitfalls includes:

> Using Singletons

> The GOF religion

> Return null from API methods

By the way, this list contains some other useful java things, like top 10 java people and top 15 worst Computer Software blunders. Not worst, but definitely bad.

### 10. [Rare Are GC Talks](http://furious-waterfall-55.heroku.com/ruby-guide/internals/gc.html)

Different Garbage Collector algorithms briefly explained. Bookshelf used as an analogy:

> Bookshelf - Memory, Books - Objects.

Ruby GC implementation discussed, but you are not forced to know ruby to understand that. By the way, article contains a lot of links to more detailed GC algorithms explanation.
