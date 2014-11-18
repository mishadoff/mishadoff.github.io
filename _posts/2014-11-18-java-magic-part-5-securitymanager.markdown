---
layout: post
title: "Java Magic. Part 5: SecurityManager"
date: 2014-11-18 15:27
comments: true
categories: [programming, java]
sharing: true
published: true
---

Pouring a bit light on `SecurityManager` and its use cases.

<!-- more -->

# Intro

You can do a *terrible things* in java using `sun.misc.Unsafe` class. Some really creepy examples were discussed in [Java Magic. Part 5: sun.misc.Unsafe](/blog/java-magic-part-4-sun-dot-misc-dot-unsafe)

[SecurityManager](https://docs.oracle.com/javase/8/docs/api/java/lang/SecurityManager.html) is a guard, which could help to prevent some sensitive actions (io, net, reflection, access etc.)

``` java
SecurityManager manager = System.getSecurityManager();
if (manager != null) {
    manager.checkAction(action);
}
```

If action is not allowed `SecurityExeption` occurs.

# Use Case

Now its a time to write some code.

Assume we are building **online grader**, a system which accepts some java code, runs it, gets results and verify that results are correct. Such graders are useful for computer science courses in MOOC platforms like coursera, udacity, etc.

Obviously, running untrusted code is unsafe, so we need to make sure code submitter does not break/compromise whole grader system. For example such sumbitter could read passwords and modify grading entry in database. Or even worse, it could fill out the whole file system, RAM or consume all threads and prevent grading for other submitters.

`SecurityManager` solves these issues.  
Extend it and override needed policies, specifying what is allowed and what is not.

``` java
class MySecurityManager extends SecurityManager {
    @Override
    public void checkRead(FileDescriptor fd) {
        throw new SecurityException("File reading is not allowed");
    }

    @Override
    public void checkWrite(FileDescriptor fd) {
        throw new SecurityException("File writing is not allowed");
    }

    @Override
    public void checkConnect(String host, int port) {
        throw new SecurityException("Socket connections are not allowed");
    }
}
```


You can set such security manager in runtime using:

``` java
System.setSecurityManager(new MySecurityManager());
```

**Note:** `setSecurityManager` is controlled by security manager as well.

If some restricted action is executed, `SecurityException` occurs.

Inspect methods from `SecurityManager` which starts with `check` prefix. There are plenty of checks JVM may run before your code. 

Though, security manager is useful tool for configuring access to subsystems and prevent untrusted code from doing a terrible things, some actions are not controlled by security manager.

## Memory Allocation

(un)fortunately, memory allocation is not controlled by a programmer and the same way `SecurityManager` can't restrict object creation. If you need validate that untrusted code fulfills memory requirements, execute it in a separate JVM and give it maximum amount of memory `java -Xmx128m`. If memory requirements are broken `OutOfMemory` occurs, but as long as this was executed in another JVM, this won't affect grader.

For more accurate memory management you need to attach instrumentation agent to a java process.

## Threads

There is no way to limit number of threads spawned by a java process.
If only `ExecutorService` responsible for thread creation, then introduce limit by using `ExecutorService.newFixedThreadPool(limit)` inside the code and make this as a convention.

Otherwise you need to write a custom agent that tracks number of active threads. Such functionality available in some proprietary java agents.

## Timeouts

To make sure that program finishes in specific time period, use some external tool for setting a timeout for it. For linux it's a [coreutils project](http://ss64.com/bash/timeout.html), see a related discussion on [stackoverflow](http://stackoverflow.com/questions/601543/command-line-command-to-auto-kill-a-command-after-a-certain-amount-of-time)

## Libraries

You can prevent whole package usage by `SecurityManager.checkPackageAccess`. The same way you can prevent usage of some external libraries or products from whole organisations. But if you want prevent usage of some specific method, like `java.lang.Math.min()`, you probably need to manually scan java source file and detect such call.
