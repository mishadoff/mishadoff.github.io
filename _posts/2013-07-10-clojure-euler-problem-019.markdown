---
layout: post
title: "Clojure Euler: Problem 019"
date: 2013-07-10 01:59
comments: true
categories: [clojure, programming, project-euler]
---

> How many Sundays fell on the first of the month
> during the twentieth century (1 Jan 1901 to 31 Dec 2000)?

Permalink: [http://projecteuler.net/problem=19](http://projecteuler.net/problem=19)

<!-- more -->

Project Euler also gives following information:

* 1 Jan 1900 was a Monday.
* A poem

  Thirty days has September,<br>
  April, June and November.<br>
  All the rest have thirty-one,<br>
  Saving February alone,<br>
  Which has twenty-eight, rain or shine.<br>
  And on leap years, twenty-nine.<br>
* A leap year occurs on any year evenly divisible by 4, but not on a century unless it is divisible by 400.

Implementin calendars logic is great, but java interoperability is much better!

First of all, take standard java `GregorianCalendar` class.
We can import it *almost* the same way as we use `require` 

``` clojure
(import [java.util GregorianCalendar])
```

To create an object use function `new`

``` clojure
(new GregorianCalendar)
```

But it is more idiomatic way to create java objects with special syntax:
*period after class name* 

``` clojure
(GregorianCalendar.)
```

To configure calendar object to specific date we need to set some its
properties. Setters syntax is following (assuming `calendar`
is an object of calendar):

Java

``` java 
calendar.set(GregorianCalendar.YEAR, 2013);
```

Clojure

``` clojure
(.set calendar GregorianCalendar/YEAR 2013)
```

Executing lot of setters is the same boilerplate as in java:

``` clojure
(do
  (.set calendar GregorianCalendar/YEAR 2013)
  (.set calendar GregorianCalendar/MONTH 11)
  (.set calendar GregorianCalendar/DAY_OF_MONTH 1))
```

To avoid repetition of object name we can use `doto` macro:

``` clojure
(doto (GregorianCalendar.)
  (.set GregorianCalendar/YEAR 2013)
  (.set GregorianCalendar/MONTH 11)
  (.set GregorianCalendar/DAY_OF_MONTH 1))
```

This macro *magically* performs the same set of operations as above, but in more readable way.

Now we can create calendar objects for the *first of the
specific month of the specific year*

``` clojure
(defn calendar-for [year month]
  (doto (GregorianCalendar.)
    (.set GregorianCalendar/YEAR year)
    (.set GregorianCalendar/MONTH month)
    (.set GregorianCalendar/DAY_OF_MONTH 1)))
```

Just iterate through all such objects in date range given in problem definition
and count Sundays.

``` clojure
(reduce +
  (for [year (range 1901 (inc 2000)) month (range 1 (inc 12))]
    (let [c (calendar-for year month)]
          (if (= GregorianCalendar/SUNDAY 
                 (.get c GregorianCalendar/DAY_OF_WEEK)) 1 0))))
```

[Problem solved](https://github.com/mishadoff/project-euler/blob/master/src/project_euler/problem019.clj)!

**P.S.** Java interoperability is ugly but still useful. 
