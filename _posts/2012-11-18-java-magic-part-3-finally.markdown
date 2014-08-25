---
layout: post
title: "Java Magic. Part 3: Finally"
date: 2012-11-18 19:39
comments: true
categories: [java, programming]
published: true
---

Every experienced java programmer should know that `finally` block always executed. But is it true?

<!-- more -->

It depends on our definition of program execution. But, generally speaking, **yes**.

### Normal program execution

*Aha, look at this*, someone might retort:

``` java
try {
  System.exit(1);
} finally {
  System.out.println("I'm here, man");
}
```

*You just said, finally block always executed?*

Well. In that case no, because we speaking about normal flow of program execution.
This is *abnormal*.

From the [official tutorial](http://docs.oracle.com/javase/tutorial/essential/exceptions/finally.html)

> Note: If the JVM exits while the try or catch code is being executed, then the finally block may not execute.

Your counter question might be: *If second line of that code always exectued?*

``` java
System.out.println("Line 1");
System.out.println("Line 2");
System.out.println("Line 3");
```

Sure, because it's linear flow. Nothing can break...**BANG**...`Electricity unavailable. Program stopped.`

What about this? It's also abnormal program execution, and we can't guarantee anything for 100%.
In fact, this is the same as `System.exit(1)` or `reset` button on your computer or whatever.

That's why, we are talking about *normal* program execution. Only *normal*.

> I said yes? I meant no!

### Perpetuum Mobile

Consider the following code:

``` java
try {
  while (true) {
    System.out.println("I print here some useful information");
  }
} finally {
  System.out.println("Let me run");
}
```

Will be the line *"Let me run"* printed? Maybe yes, if printing error to standard output appear. Almost always the answer is no.

In that case, there are no difference between simple statement and finally block. None of them will be executed, throw this example away.

### Threads

What about threads? We know that execution flow controlled by threads and they can be interrupted.

Assume that we have thread that perform some work, and other thread kills first one right before finally block. Finally wasn't executed.

Assume that we have deadlock between two threads, right before finally line. The same thing.

From the [same tutorial](http://docs.oracle.com/javase/tutorial/essential/exceptions/finally.html)

> ...if the thread executing the try or catch code is interrupted or killed, the finally block may not execute even though the application as a whole continues.

So, we can treat thread like a program, and make one effective rule:

**Rule 1**. Finally executes always, except the case where controlling program or thread was aborted.

### Finally we return

Ok, now we know when finally is not executed. But do we know when finally is executed?

Consider the example:

``` java
int someFunc() {
  try {
    return 0;
  } finally {
    return 1;
  }
}
```

The result is obviously `1`. Just because `finally` is always executed.

Consider, next example:

``` java
int someFunc() {
  try {
    throw new RuntimeException();
  } finally {
    return 1;
  }
}
```

The result is `1` again. And it's a problem. We just lose the exception.
Such issue known as *exception swallowing*. It is very dangerous, because client's code expect either exception or some value, but it always get only value.

One more less imaginary example.

``` java
String deposit(int amount) throws DAOException {
  try {
    return dao.deposit(amount);
  } finally {
    return "OK";
  }
}
```

The logic behind `finally` is to have some default value, and our `deposit` method throws the `DAOException` where client code is
responsible for its handling. Unfortunately, perhaps compiler forces you to handle this `DAOException` it never occurs. And string "OK" will be returned.

**Rule 2**. Never use `return` from `finally` block.

### Instead of conclusion

A lot of programmers are aware about this common mistake. But some are not.
Maybe these two simple rules give you a tiny light on coward `finally`.
