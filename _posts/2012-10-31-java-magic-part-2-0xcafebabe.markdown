---
layout: post
title: "Java Magic. Part 2: 0xCAFEBABE"
date: 2012-10-31 19:42
comments: true
categories: [java, programming]
---

Did you know that all java classes start with the same 4 bytes. In hex it's `CAFEBABE`.

<!-- more -->

To make sure, create simple java file, call it `Hello.java`:

``` java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hell, O'World!");
    }
}
```

Compile it by `javac Hello.java`, and open `Hello.class` with hex-editor (`M-x hexl-mode` for emacs)

![](http://i.imgur.com/VztJknQ.png)

James Gosling [explained](http://radio-weblogs.com/0100490/2003/01/28.html) this:

> We used to go to lunch at a place called St Michael's Alley.
> According to local legend, in the deep dark past, the Grateful Dead used to perform there before they made it big.
> It was a pretty funky place that was definitely a Grateful Dead Kinda Place.
> When Jerry died, they even put up a little Buddhist-esque shrine.
> When we used to go there, we referred to the place as Cafe Dead.
> Somewhere along the line it was noticed that this was a HEX number.
> I was re-vamping some file format code and needed a couple of magic numbers: one for the persistent object file, and one for classes.
> I used CAFEDEAD for the object file format, and in grepping for 4 character hex words that fit after "CAFE" (it seemed to be a good theme)
> I hit on BABE and decided to use it. At that time, it didn't seem terribly important or destined to go anywhere but the trash-can of history.
> So CAFEBABE became the class file format, and CAFEDEAD was the persistent object format. But the persistent object facility went away,
> and along with it went the use of CAFEDEAD - it was eventually replaced by RMI.

`0xCAFEBABE` is `3405691582` in decimal.
If we sum all digits we get `43`. One more than `42` - *Ultimate Answer to the Life, the Universe, and Everything*.
By the way, `43` is a prime number. You see, magic everywhere. Even in the last sentence.
