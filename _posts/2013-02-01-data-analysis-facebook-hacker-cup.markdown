---
layout: post
title: "Data Analysis: Facebook Hacker Cup 2013"
date: 2013-02-01 12:42
comments: true
sharing: true
categories: [programming, statistics, data, clojure, selenium]
published: true
---

What programming language is most used?

<!-- more -->

To answer this question we use results from the qualification round
of [Facebook Hacker Cup 2013](https://www.facebook.com/hackercup)

If you only interested in statistics, [skip!](#stats)

### 0. Intro

As you may know, Facebook Hacker Cup is a programming contest. You have programming
problem and you need to solve it, in most cases in efficient way. You get input file
and you need to submit the output file in less than 6 minutes. Pretty simple.
The interesting part you need to submit your source code, that can be reviewed by
any of contestants after round.

These sources we use to get programming language statistics.

### 1. Data

Primary resource in statistics is data.

We have [scoreboard](https://www.facebook.com/hackercup/scoreboard?round=185564241586420) page
(*You must be logged to FB to see results*) and interested in all links named *source*.
Open every link manually takes much time, so we will use automated approach
and will write program. Yes, in clojure.

#### Dependencies

For facebook authentification we can use:

* [OAuth authentification](https://github.com/DerGuteMoritz/clj-oauth2)
* [Facebook app](https://developers.facebook.com/)
* [Selenium](http://seleniumhq.org/)

I didn't think too much about alternatives,
because I wanted to try selenium in clojure. It's a time!

In few words, selenium provides capability to perform programmatically
browser events. Often used in automation. To use selenium in clojure program
just add `[clj-webdriver "0.6.0-beta2"]` to your project dependencies.

To simplify HTTP GET access we use library `[clj-http "0.6.3"]` and for
additional help `[org.clojure/clojure-contrib "1.2.0"]`.

In code we use following *requires*

``` clojure
(:require [clj-http.client :as http])
(:require [clj-webdriver.taxi :as web])
(:require [clj-webdriver.core :as c])
(:require [clojure.contrib.math :as math])
```

#### Automation

Before we will automate actions to gather sources we need to decide
what these actions are?

* Run browser
* Open facebook url
* Login to facebook
  * Enter login
  * Enter password
  * Press submit
* Go to scoreboard page
* Process each page `loop [1..n]`
  * Go to page `n`
  * Get all links
  * Filter links with sources
  * Process each link `loop [1..k]`
    * Go to link url `k`
    * Get all page source
    * Filter out source code
    * Generate file name
    * Write source to file

Pretty clear, right? Let's code it sequentially.

#### Implementation

* *Run browser* and *Open facebook url*

``` clojure
(web/set-driver! {:browser :firefox} "http://facebook.com")
```

* *Login to facebook*

``` clojure
(web/input-text "#email" username)
(web/input-text "#pass" password)
(web/submit "#pass")
```

* *Go to scoreboard page*

``` clojure
(web/to "https://www.facebook.com/hackercup/scoreboard?round=185564241586420")
```

* *Process each page*

``` clojure
(doseq [page-num (range 1 (inc 114))]
  (process-page page-num))
```

Number of pages is hardcoded. It's ok.
What is `process-page` method?

``` clojure
(defn process-page [n]
  (let [url (str "https://www.facebook.com/hackercup/scoreboard?round=185564241586420&page=" n)]
    (web/to url)
    (doseq [e (web/find-elements {:css "a"})
            :let [url (c/attribute e "href")]
            :when (and url (.startsWith url "https://fbcdn-dragon-a.akamaihd.net/"))]
      (process-url url))))
```

First of all,  we concat scoreboard link with page number to get actual link to each page.
Then we go to that page obtain all `a` elements, get their `href` values and filter
to save only ones that contain source code.

* *Process each url*

``` clojure
(defn process-url [url]
  (let [source-code (:body (http/get url))
        file-name (generate-filename url)]
    (spit file-name source-code)))
```

In this part we obtain html source of url and get its `:body` tag.
As all source urls contain just plain text, we don't need additional
filtering. Just save it to file.

* *Generate file name*

I don't want to name file as url. That's why for naming we use following
function: `timestamp + underscore + absolute value of url hashcode`

``` clojure
(defn generate-filename [url]
  (str DIRECTORY (System/currentTimeMillis) "_" (math/abs (hash url)) ".txt"))
```

`DIRECTORY` is just `def` for folder where you want to place all sources.

Whole script source available [here](https://github.com/mishadoff/clj-statistic-tools/blob/master/src/clj_statistic_tools/fbhackercup2013.clj)

#### Execution

To run that script you need to write in clojure REPL
`(run "username" "password")`, with correct values for *username* and *password*, obviously.

It works some time. *Some time* equals to eight hours on my machine. Long enough.
But it's ideal time for *night crawling*!

In the morning I had all work done.

```
ls -1 | wc -l && du
```

prints

```
20348
291120  .
```

Good sign. We have more than 20K of source codes with total size almost 300Mb.

### 2. Analysis

Data is good. But no one interested in raw data, so we need process it.

Basically, we need to detect programming language by source file.
No extensions.

You can [write your own classifier](https://www.bionicspirit.com/blog/2012/02/09/howto-build-naive-bayes-classifier.html)
or use some existing tool.

I did few-minutes research on this topic and found
[linguist](https://github.com/github/linguist) project.
It is written in *Ruby* and used in [Gist](https://gist.github.com/) to
detect snippet language. Exactly what we need!

*Unfortunately, I do not know ruby. I even could not build and run linguist classifier to
detect language in my files. Rvm, gems and modules driving me crazy. I surrendered.*

Another solution to use javascript library [Highlight.js](http://softwaremaniacs.org/soft/highlight/en/).
It is used in syntax coloring, but have automatic language detection. Again, javascript and
reading files from filesystem... Don't tell me about Node.js

I decided to write my own "classifier". Honestly, it's just regexp matching mechanism
on common language constructions: keywords, imports, most used functions, etc.

Iterative approach has been used.

Select some popular language construction say `#include <iostream>` and filter it as `C`. After filtering
we detect some subset of `C` language, remove them from all files list and repeat again with another construction.

*I don't know how `C++` different from `C`, so I accept them both as interchangeable languages but call it `C/C++`.
By the way, `C - C++ = 0`, so we assume they are equal.*

I developed some number of patterns (they can be reviewed
[here](https://github.com/mishadoff/clj-statistic-tools/blob/master/src/clj_statistic_tools/fbhackercup2013.clj))
and processed all gathered source files.

Unfortunately, not all files were processed succesfully. I reviewed approximately 2000 files manually, few
new languages were detected but big amount of them were the crap: input data, binary files, some text information.

I think we don't lose too much if we say *only 99% of files were processed*.

### <a id="stats"></a>3. Statistics

![](http://i.imgur.com/sWyfLqv.png)

If you don't like this visualization, you can create your own. Here is data:

```
C/C++          10524
Java            3117
Python          3102
C#              1233
PHP              821
Ruby             488
Perl             142
Pascal/Delphi    136
Javascript       109
Haskell           85
Scala             72
Clojure           29
Go                28
Visual Basic      19
F#                12
Scheme             8
OCaml              7
Common Lisp        6
Lua                5
Matlab             4
Cocoa              3
Groovy             2
Dart               2
awk                1
Powershell         1
bash               1
Kotlin             1
ActionScript       1
Dylan              1
--------------------
crap             192
not detected     196
--------------------
TOTAL          20348
```

### 4. Conclusion

* Top 5 was predicted but it's extremely crazy that C/C++ occur more than 50%
* Python has almost the same amount as Java
* Good occurence of functional languages: Haskell, F#, Scala, Clojure, Ocaml
* 29 Clojure files that means there ~10 clojurists.
* Two languages I never heard of:
[Dart](http://www.dartlang.org/) and
[Dylan](http://opendylan.org/)
* *Parenthesis* languages still used: Common Lisp, Scheme and Clojure (of course)
* Respect to matlabists
* Few *exotic languages* used: awk, powershell, bash
* [Kotlin](http://kotlin.jetbrains.org/) becomes popular!

*Warning:* Do not treat this statistics as real-world pattern. It is a programming competition with
a lot of geeks, they can use all they want. Also do not blame their code for quality. It was created just for
correctness and speed. Pay attention to code quality in production, but always remember:

> Your code may be elegant, by mine fucking works.
>
> -- [Leon Fayer](http://omniti.com/seeds/your-code-may-be-elegant)
<br />

**P.S.** First of all, it is not high-accurate statistics. As I am not
programming guru, I don't know all possible languages' constructions
so it is likely that one construction overlapped with another. In that case
only first one will be checked. Ideally, would be good to have language
detecting library in clojure for future analysis. *Maybe*, I will do it.
Second of all, code presented here is not beautiful, not optimized, have a lot
of hardcode, but it works. Just in the spirit of Facebook Hacker Cup.
