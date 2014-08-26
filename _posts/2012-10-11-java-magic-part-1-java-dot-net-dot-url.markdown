---
layout: post
title: "Java Magic. Part 1: java.net.URL"
date: 2012-10-11 16:30
comments: true
sharing: true
categories: [java, programming]
published: true
---

Recently, I found on reddit very interesting Java code snippet (slightly modified):


```java
HashSet set = new HashSet();
set.add(new URL("http://google.com"));
set.contains(new URL("http://google.com"));
Thread.sleep(60000);
set.contains(new URL("http://google.com"));
```

What do you think the output for lines 3 and 5 will be?

<!-- more -->

Definitely not `true, true` if the question was asked. Think for two minutes.

Ok. In most cases it will be `true, false` because you have internet connection (*How else you can read this?*).
Turn off your network cable or wi-fi, and you'll get `true, true`.

The reason is in implementation of `hashCode()` and `equals()` methods for `URL` class.

Let's see how hashCode calculated:

```java
public synchronized int hashCode() {
  if (hashCode != -1)
    return hashCode;
  hashCode = handler.hashCode(this);
  return hashCode;
}
```

We can see hashCode is an instance variable that calculates once. Makes sense, `URL` is [immutable](http://en.wikipedia.org/wiki/Immutable_object).
What is handler? It's an instance of one of `URLStreamHandler` subclasses, depends on protocol type (file, http, ftp), that have helper hashCode implementation.
Just look at `URL.hashCode()` javadoc:

> The hash code is based upon all the URL components relevant for URL comparison. As such, this operation is a blocking operation.

Stop! **BLOCKING OPERATION?!**

*- Sorry, I couldn't check email yesterday due to hashCode calculation.*

or even better

*- No, mom, I can't watch porn, It's hashCode, you know.*

Ok, let it be blocking. Another exciting part, that handler **resolves host IP address** for hashCode calculation.
Tries to resolve, to be honest. If it can not do this, it calculates hashCode based on host, which is *google.com* for our example.
Shit happens when IP is dynamic, or host have request balancer that also changes host IP.
In that case we got different hashCodes for one host name, and will have two (or even more) instances in HashSet. Not good at all.
By the way, hashCode and equals performance is terrible because of `URLStreamHandler` opens `URLConnection`. But it's another topic.

### How to avoid this?

* Use `java.net.URI` instead of `java.net.URL`. It's not the best choice though, but have deterministic hashCode implementation.
* Do not use `java.net.URL` in collections. Good option to maintain collection of String objects (that represent host name) and get URL when needed.
* Disable your network adapter during hashCode calculation. (*It's a joke, but it helps*)
* Use your own subclass of URLStreamHandler with proper implementation of hashCode.

Finally, I'm pretty sure `java.net.URL` class has lot of useful applications. But not that way.
