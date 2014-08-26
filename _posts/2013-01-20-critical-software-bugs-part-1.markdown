---
layout: post
title: "Critical Software Bugs: Part 1"
date: 2013-01-20 18:48
comments: true
sharing: true
categories: [programming, java, history, bug]
published: true
---

Little journey through the history of most critical software bugs
with some code examples.

*Do not try to reproduce any of these!*

<!-- more -->

### Intro

> Every last bug is the last but one.

**Bugs** are bad. Some of them cause uncomfortable work, more actions
than expected, inconsistency, layout issues, etc.
Some of them "[not a bug](http://en.wikipedia.org/wiki/Undocumented_feature)" at all.

**Bad bugs** much worse. They cause data corruption, invalid data representation,
unavailability to perform action, losing integrity and so on.

There are **critical bugs** with painful consequences. They can damage the health, nature, people
break buildings, technics. Theoretically, they
[can lead to World War III](http://www.washingtonpost.com/wp-srv/inatl/longterm/coldwar/shatter021099b.htm).
Hope, it's just theoretically.

If you are software engineer, you definitely will commit the bugs. No panacea.
But if we aware about common mistakes, then less likely we make that mistakes.

This article is review of famous most critical software bugs
including brief history, consequences, root causes, possible solutions, code examples and some advices.

### [Mariner 1](http://en.wikipedia.org/wiki/Mariner_1) (1962)

**History:**
In 1962, spacecraft *Mariner 1* was guided by computer program with error causes
racket do not follow its expected trajectory. To prevent tragic consequences, racket
was destoyed immediately.

**Consequences:** Money, $18 million.

**Reason:**
There are tons of versions about what type of error it was, but official version
is *missing hyphen* ('-') in program. Maybe it's just a myth for publicity,
but, anyway, if the reason of bug is incorrect handwritten formula transcription
from paper to computer, it's sad.

Probably, the root cause of that is writer and computer operator were different people.
It's common case for that times. *Programming* was synonym to the *scientific calculations* not
an enterprise, as nowadays. That's why scientist, programmer, and operator could be
different people.

Related story, it's why zero symbol has a period or diagonal line inside - to prevent
confusing with "big O" for "typer" person. Just compare `0` and `O`.

**Solution:**
It's hardly can be a problem today (as it was 50 years ago), but if you write for somebody on the paper, write it accurate.
If you type into computer from someone's paper sheet, check, re-check and double re-check
what you typing. Ask if you have concerns. Especially, if it is the program for spaceship.

In very *far-fetched* example missing minus sign can result into invalid computation:

``` java
double getDiscountPercent() {
    return 0.25; // instead of return -0.25;
}

double calculatePrice(double initial) {
    return initial * (1.0 + getDiscountPercent());
}
```

That way instead of paying $75, you pay $125.

**Lesson:** Always check your manual rewriting actions.

### [Siberian pipeline sabotage](http://en.wikipedia.org/wiki/Siberian_pipeline_sabotage) (1982)

**History:** Cold war. Soviet Union steal technology for managing gas pipelines from Canada. This
technology intentionally contained the bug ([Logic Bomb](http://en.wikipedia.org/wiki/Logic_bomb)), developed by CIA
for sabotage purposes. Bomb triggered.

**Consequences:** The greatest non-nuclear explosion in the world.

**Reason:** Every microship from stolen technology would
work fine just for 10 million cycles, after that switches to another mode.

They *used someone's code*.

**Solution:**

> You shall not steal
>
> -- [Ten Commandments](http://en.wikipedia.org/wiki/Ten_Commandments)

Honestly, if someone's technology used for critical areas, like gas
transportation, it must be reviewed and tested. 100 times.

Story can be translated as:

I found memory card on the city dump.
Card contained one shell script `please-execute-me.sh`.
I *executed* it. System crashed. Who's guilty? Just me.

Use software from trusted sources.

In modern software world, we often use someone's libraries, frameworks,
APIs to build our own applications. We *expect* that code is *reviewed* and *tested*.
If not, we can help to do it. How we can be sure these libs without logic bombs?

Trust or review the sources. That's why I love open source software.

Logic bomb detection is not that hard. Just find the code that causes error
and see the reason of that error. Again, code review helps *to prevent* the
logic bomb instead of *fixing it*. Build application to production only from
version control system (*which is controlled, right?*)

Just for educational purposes: simple example of time-triggered logic bomb

``` java
public class LogicBombRunner {
        public static void main(String[] args) {
                Thread thread = new Thread(new LogicBomb());
                thread.start();
        }
}

class LogicBomb implements Runnable {

        private long toStop;
        private final long TIME_TO_TRIGGER = 1000 * 60 * 60 * 24 * 100;

        public LogicBomb() {
                toStop = System.currentTimeMillis() + TIME_TO_TRIGGER;
        }

        @Override
        public void run() {
                while (true) {
                        if (System.currentTimeMillis() < toStop) {
                                System.out.println("System works fine.");
                                try {
                                        Thread.sleep(1000);
                                } catch (InterruptedException e) {
                                        e.printStackTrace();
                                }
                        } else {
                                System.out.println("Logic bomb triggered.");
                                throw new RuntimeException();
                        }
                }
        }
}

```

Yes. It throws `RuntimeException` after approximately 100 days from `LogicBomb` object creation.

**Lesson:** Do not steal. Trust to very limited circle of people.

### [Therac-25](http://en.wikipedia.org/wiki/Therac-25) (1985 - 1987)

**History:** Therac-25 was a medical device for radiation therapy.
It could deliver either beta-particles or X-rays. Unfortunately,
operating system for controling the device was written with error,
that causes sad results.

**Consequences:** At least 2 patients died, 4 patients were given overdose,
approximately 100 times intended dose.

**Reason:** Investigation concluded the common reason was a
*bad software design*, *development practices* and a *set of bugs*
instead of some particular critical bug.

These bad practices and bugs include:

* No code review
* Poor exception handling
* No integration testing until device was assembled in the hospital
* Poor hardware design
* *Bad code* reuse
* Concurrency issues

**Solution:** Code review its a must. Even if you working
alone in the team, show sources to some your friend (programmer).
Two pair of eyes can spot the mistake two times more.

*Poor exception handling*. If failure occurs, no one knows about that failure.
Never handle exception like that:

``` java
catch (SomeException e) {

}
```

In 99% there are must be a proper handling. If you don't know how
to handle, rethrow it, possible with wrapping:

``` java
catch (SomeException e) {
   throw new SpecificException(e);
}
```

Much better to throw responsibility for handling one level up
in method signature:

``` java
void method() throws SomeException
```

If your real intention to left catch block empty, say this:

``` java
catch (SomeExcpetion e) {
  // swallowing this exception is intended
}
```

*Bad code reuse*. [Code reuse](http://en.wikipedia.org/wiki/Code_reuse) is a good thing.
Except the case when you reuse something with errors.

For example, I have following method for testing if number is even in some of my
previous projects (*yes, look at your code that was written few years ago*):

``` java
static boolean isEven(int num) {
    return num % 2 != 1;
}
```

This method returns incorrect result `true` for all negative odd numbers. Just because
`%` operator returns `-1` except `1` for negative numbers. Better do *NOT* reuse such method.
Just write new. And replace old method immediately.

``` java
static boolean isEven(int num) {
    return num % 2 == 0;
}
```

*Concurrency*. Software world hardest issues are concurrency issues.

> You have a problem and decide to use threads.
> Two now problems have you.
>
> -- Some guy from internet
<br>

> You have a problem and decide to use locks.
> Now you have
>
> -- Some another guy from internet

If you know that some object will be shared between threads, one solution to
add `synchronized` ([Monitor object](http://en.wikipedia.org/wiki/Monitor_%28synchronization%29))
to each read/write state method. It will slow
your work with that object but prevent a lot of unexpected errors.

Although, there are much flexible synchronization mechanisms in java that
plain old `synchronized` as [semaphores](http://en.wikipedia.org/wiki/Semaphore_%28programming%29),
[mutexes](http://en.wikipedia.org/wiki/Mutual_exclusion),
[read-write locks](http://en.wikipedia.org/wiki/Read_write_lock_pattern).

I reccomend to use java package [java.util.concurrent](http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/package-summary.html).
It consists of a lot of concurrency techniques, like `Synchronizers`, `Executors`,
`Atomics`, `Concurrent Collections`, `Futures`, `Callbacks` etc.

**As a bonus:** Good books about concurrency in Java:

* [Java Concurrency in Practice](http://www.goodreads.com/book/show/127932.Java_Concurrency_in_Practice)
* [Concurrent Programming in Java](http://www.goodreads.com/book/show/629608.Concurrent_Programming_in_Java)

**Lesson:** Always handle exceptions. Pay more attention to concurrency issues.

### [Patriot Missile](http://en.wikipedia.org/wiki/MIM-104_Patriot) (1991)

**History:** In 1991, American battery fired Patriot Missile to intercept Iraq missile.
Interception failed because of system bug in time step calculation.
Iraq missile destroyed an American Army barracks.

**Consequences:** 28 soldiers died, 100 injured.

**Reason:** Floating point rounding error. System's internal clock was calculating
time by 0.1 seconds step (0.1, 0.2, 0.3, ...). As `0.1 = 1/10` have infinite binary representation
`0.0001100110011001100110011001100110011001100110011001100..`
and for this calculation 24-bit register was used,
register hold just `0.00011001100110011001100` introducing truncating error
`0.00000000000000000000000110011` what is approximately `0.0000001` in decimal.

Small enough, huh?

Not really, Multiplying this number for 100 hours gives:

    0.0000001 * 100 * 60 * 60 * 10 = 0.36 sec

During that time Iraq missile travels a half kilometer, and was out of tracking radius
for Patriot Missile. Bad things happen.

**Solution:** Floats are very error-prone if we don't handle them correctly.

Predict the output of the following code:

``` java
for (double d = 0.0; d != 1.0; d = d + 0.1) {
    System.out.println("Iteration");
}
```

Infinite loop. The same problem as described above.

One solution is to use float that have exact binary representation.
`0.125`, `0.25`, `0.5` for example.
But this greatly reduces the space of allowed values and give less flexibility.

Another solution to use big decimal or ratio types.
Java, as example, have out-of-the-box `BigDecimal` class. Loop above rewritten with
that class works as expected:

``` java
BigDecimal init = new BigDecimal("0.0");
BigDecimal bound = new BigDecimal("1.0");
BigDecimal step = new BigDecimal("0.1")

for (BigDecimal d = init; !d.equals(bound); d = d.add(step)) {
        System.out.println(d);
}
```

One disadvantage it has slower execution than with floats. Consider trade-off
between time and accuracy. If human lives on the stake, it's not relevant choice.

`Ratio` class can be defined without any problems and used similar way.

**Lesson:** If you need very accurate calculations use big decimals or ratios.

### [Mars Climate Orbiter](http://en.wikipedia.org/wiki/Mars_Climate_Orbiter) (1998)

**History:** Again about space. In 1998, communication with Mars Climate Orbiter
was lost, because of changed trajectory. Space probe was crashed near Mars.

**Consequences:** Money, $327 million

**Reason:** Navigation system design was developed by another team.
To represent Force they used [pound-force](http://en.wikipedia.org/wiki/Pound-force)
from [Imperial units](http://en.wikipedia.org/wiki/Imperial_units)
instead of [Newton](http://en.wikipedia.org/wiki/Newton_%28unit%29)
from [metric system](http://en.wikipedia.org/wiki/Metric_system).

    1 Newton = 0.22 pound-force

Without provided conversion between them system failed.

Issue resemble to using dates in different timezones, but much more critical.

**Solution:** First of all, at least one science-aware person must perform code review.
At least for code blocks with formulas and calculations. If there are comments about using pound-force,
the problem easy spotted. Second of all, received code must be tested on model
instead of physical machine. This is also, likely, detects the issue.
Developer who wrote that less guilty, but still guilty.

There are different approaches to implement this in java:

very bad approach, we don't know what units are used:

``` java
new Force(1.533);
```

better (still bad) approach to indicate with comment:

``` java
// force in Newtons!
new Force(1.533);
```

better (still bad) approach to indicate different constructors for different units:

``` java
new ForceInNewtons(1.533);
```

I prefer approach where you specifies a *value* and *unit* in the constructor, and
they converted to something common unit (metric) convenient for use in internal representation:

``` java
enum ForceUnit {
  NEWTON,
  POUND_FORCE
}

new Force(1.533, ForceUnit.NEWTON);
```

A bit more code, but it is understandable and easy to use for different unit systems.
Unit conversion method can be defined either at the `enum ForceUnit` or `class Force` level.

**Lesson:** Always explicitly indicate what units used in your code.

*To be continued.*

### Links

* [List of Software Bugs](http://en.wikipedia.org/wiki/List_of_software_bugs)
* [Collection of Software Bugs](http://www5.in.tum.de/~huckle/bugse.html)
* [History's Worst Software Bugs](http://www.wired.com/software/coolapps/news/2005/11/69355?currentPage=all)
* [Top Ten Most Infamous Software Bugs Of All Time](http://www.sundoginteractive.com/sunblog/posts/top-ten-most-infamous-software-bugs-of-all-time)
* [NASA Mariner 1](http://nssdc.gsfc.nasa.gov/nmc/spacecraftDisplay.do?id=MARIN1)
* [Soviets Burned By CIA Hackers?](http://www.wired.com/culture/lifestyle/news/2004/03/62806)
* [An Investigation of the Therac-25 Accidents](http://courses.cs.vt.edu/cs3604/lib/Therac_25/Therac_1.html)
* [An Analysis of the Patriot Missile System](http://seeri.etsu.edu/SECodeCases/ethicsC/PatriotMissile.htm)
* [The Patriot Missile Failure](http://www.ima.umn.edu/~arnold/disasters/patriot.html)
* [Mystery of Orbiter Crash Solved](http://www.tysknews.com/Depts/Metrication/mystery_of_orbiter_crash_solved.htm)
