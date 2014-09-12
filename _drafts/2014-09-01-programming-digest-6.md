---
layout: post
title: "Programming Digest 6"
date: 2014-09-01 02:10
comments: true
categories: [programming, digest]
sharing: true
published: true
---

Life, Java, Search, Parallel Programming, Machine Learning and Hearthstone

<!-- more -->

### 1. [Life is a game. This is your strategy guide](http://oliveremberton.com/2014/life-is-a-game-this-is-your-strategy-guide/)

> Real life is the game that everyone is playing. But it can be tough. This is your guide.

Article raises important questions about skills in your life and strategy
you should chouse to make it more comfortable.

And yes. Nothing about programming. 

### 2. [Collection Pipeline](http://martinfowler.com/articles/collection-pipeline/)

Who does not know Martin Fowler?

Collection pipeline is a pattern for building new functionality from a sequence
of isolated actions. 

For those who familiar with unix

```bash
ls | grep ".md" | sort | head -3
```

For clojurists

```clojure
(->> (list-of-files)
     (filter markdown?)
	 (sort)
	 (take 3))
```

Yes, something like *chain of responsibility*

Despite this approach looks simple and common pattern for sequential processing,
article share some important insights.

### 3. [A Few Useful Things to Know about Machine Learning](http://homes.cs.washington.edu/~pedrod/papers/cacm12.pdf)

> ...article summarizes twelve key lessons that
> machine learning researchers and practitioners have learned.
> These include pitfalls to avoid, important issues to focus on,
> and answers to common questions.

### 4. [Solr 4. The NoSQL Database](https://www.youtube.com/watch?v=WYVM6Wz-XTw)

[Solr](http://lucene.apache.org/solr/) is a search engine backed by [Lucene](http://lucene.apache.org/)
Video overview of the main Solr features from its creator.

### 5. [Distributed systems (for fun and profit)](http://book.mixu.net/distsys/single-page.html)

If you do not know anything about distibuted systems (*like me*) this intro could be helpful.
Just make sure you have *few* hours for reading. 

### 6. [Predicting the next Math.random() in Java](http://franklinta.com/2014/08/31/predicting-the-next-math-random-in-java/)

Do you still trust to your random distribution?

The article discusses how Java `Random` could be 100% predicted after the first generated number.

For *true randomness* use [java client](https://github.com/mishadoff/randomorg) for [random.org](http://random.org)

### 7. [So You Want To Write Your Own CSV code?](http://tburette.github.io/blog/2014/05/25/so-you-want-to-write-your-own-CSV-code/)

Recently, we discussed with colleague how write parser for `csv`. Really, `String.split(",")` is far from enough.

Article just a set of questions you must answer if you are writing production-ready csv-parser.

> Use a solid library.

### 8. []()

### 9. []()

### 10. []()
