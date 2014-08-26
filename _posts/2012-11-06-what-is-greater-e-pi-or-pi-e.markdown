---
layout: post
title: "What is greater: e^pi or pi^e?"
date: 2012-11-06 16:00
comments: true
sharing: true
categories: [math, fun, xkcd]
published: true
---

You can answer almost immediately using calculator, but what if you don't have it?

<!-- more -->

I don't remember the exact origin of this meme, but this situation was almost every time when we played with numbers.

> -- Propose a number.
>
> -- e^π.
>
> -- Integer, shithead!

This even might appear in usual conversations:

> -- What is her size?
>
> -- e^π
>
> -- So small. I prefer π^e.

And a following arguing about **what is greater?** Until we simply compare results using calculator.

Let's see how this problem can be solved without using a calc.

**Warning:** Goal of this article not only to show correct solution to the problem, but provide other solutions (*even incorrect!*) that can convince somebody.

## Origins

[π](http://en.wikipedia.org/wiki/Pi) is the mathematical constant. It's just a number that represents ratio between circle circumference and diameter `C/d`.
Approximately, equal to `3.1415`. [Get more decimal places for FREE!](http://webdocs.cs.ualberta.ca/~smillie/Pi10000.html)

<img src="http://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Pi_eq_C_over_d.svg/600px-Pi_eq_C_over_d.svg.png" width="300" height="300">

[e](http://en.wikipedia.org/wiki/E_%28mathematical_constant%29) is the harder one. If you sum infinite sequence below, you'll get `e`.
Approximately, equal to `2.7183`. [FREE decimal places without registration!](http://apod.nasa.gov/htmltest/gifcity/e.2mil)

![](http://upload.wikimedia.org/math/8/0/5/8050a584d4b528e6293bf02c34f6e399.png)

I'm pretty sure that's more than enough you need to know to solve the problem.

## Google It

[Get answer from google](https://www.google.com/#hl=uk&gs_nf=3&tok=WfGdb7x83ofvKLc_85HnIw&cp=5&gs_id=227&xhr=t&q=e^pi+-+pi^e&pf=p&output=search&sclient=psy-ab&oq=e^pi+&gs_l=&pbx=1&bav=on.2,or.r_gc.r_pw.r_qf.&fp=51e735cc3a152124&bpcl=37643589&biw=1280&bih=634)

## Rough estimate

![](http://i.imgur.com/Jv0W1.jpg)

We can use exponent function on integers, so let's just round the numbers.
`3.1415` becomes `3`, `2.7183` becomes... `3` also.

    3^3 vs 3^3
    27 = 27

They are equal. Wrong answer.

Let's use [floor and ceiling](http://en.wikipedia.org/wiki/Floor_and_ceiling_functions) functions.

Floor: `floor(e) = 2`, `floor(π) = 3`

    2^3 vs 3^2
    8 < 9

Ceiling: `ceil(e) = 3`, `ceil(π) = 4`

    3^4 vs 4^3
    81 > 64

Two different results depend on round function that we use. Can't be sure.
Need another method for verification.

## Calculus

![](http://i.imgur.com/Low4R.jpg)

This needs basic understanding of [logarithm](http://en.wikipedia.org/wiki/Logarithm) and [derivative](http://en.wikipedia.org/wiki/Derivative)

    e^π vs π^e

Use logarithm property `a < b => log(a) < log(b)`, and take `log` with base `e` (`ln`).
Honestly, it's not logarithm property, but [monotonic function](http://en.wikipedia.org/wiki/Monotonic_function) property. Who cares?

    ln(e^π) vs ln(π^e)

Use logarithm property `log_a(a^b) = b`

    π vs ln(π^e)

Use logarithm property `log_a(b^c) = c * log_a(b)`

    π vs e * ln(π)

Now, people who know value of `ln(π)` can answer the question. We don't know. Move `ln(π)` to the left side

    π/ln(π) vs e

Take a look at expression `π/ln(π)`. Change `π` to `x`. Got `x/ln(x)`. What is it? It's a function!

Let's find extrema of this function `f(x)`. Take first derivative.

    f'(x) = (ln(x) - 1)/(ln^2(x))

We get extrema at the points where derivative equals to zero

    (ln(x) - 1)/(ln^2(x)) = 0

Ratio number equals to zero if its numerator equals to zero.

    ln(x) - 1 = 0

Move `1` to the right

    ln(x) = 1

Logarithm equals to `1` if base equals to argument. Therefore

    x = e

And if you know `1/ln(x)` behavior, you get that extremum is a minimum of function `f(x)`.
Substitute `x` with `e`

    e/ln(e) = e

Function minimum is `e` where `x = e`. If `x = π`, due to minimum, function value will be **greater than** `e`.
Remember that we do this to get estimate of `π/ln(π)` which was on the left side of expression `π/ln(π) vs e` and if its **greater than** e,
`e^π` greater than `π^e`. Huh. Done.

**Note:** We skip mathematical proof that extrema `x = e` is minimum. Not hard to show.

## Manual computations

![](http://i.imgur.com/tTTCC.jpg)

Why don't we perform calculation manually?

Ok, we have `π`, which is `3.1415` or `22/7` its famous simplest ratio approximation.
On the other side we have `e`, which is `2.7183`. But I'm not aware of any `e` approximation.
Let's create one from the definition of `e`. Take and sum first 5 elements.

    1 + 1 + 1/2 + 1/6 + 1/24 = 65/24

Excellent. Now we need just compare `2.7183^(22/7)` and `3.1415^(65/24)`. What can be simpler?

Rational exponent can be expanded as nth-root of some power:

![](http://upload.wikimedia.org/math/a/4/3/a43d1edf99a795101aa58ce39cc72f52.png)

To take n-th root manually we use a special case of [Newton's method](http://en.wikipedia.org/wiki/Newton%27s_method).
Just one iteration:

![](http://upload.wikimedia.org/math/a/2/5/a25d581b7161170fc8ab0dbe74532ecc.png)

Estimate for `e^π`:

    2.7183^(22/7)
    sqrt_7(2.7183^22)

...Multiplying 22 times...

In fact, not all so bad. To calculate `a^22` we need only 6 multiplications:

    a^1 * a^1 = a^2
    a^2 * a^2 = a^4
    a^4 * a^4 = a^8
    a^8 * a^8 = a^16
    a^16 * a^4 = a^20
    a^20 * a^2 = a^22

Nth-root we obtain (*decimal dropped*):

    sqrt_7(3585440111)

Applying Newton's method. Select closest root:

    1^7 = 7 < 3585440111
    2^7 = 128 < 3585440111
    3^7 = 2187 < 3585440111
    ...
    23^7 = 3404825447 < 3585440111
    24^7 = 4586471424 > 3585440111

Good. Most closest value is `23`. Then

    sqrt_7(3585440111.25)
    sqrt_7(23^7 + 180614664)
    23 + 180614664/(7 * 23^6)
    23 + 180614664/1036251223
    23 + 0.17
    23.17

And the same for `π^e`:

    3.1415^(65/24)
    sqrt_24(3.1415^65)

This power `65` much larger than `22`. But it needs just 7 multiplications.

We can cheat here. We get rough result `2 * 10^32`. Now we test `23^24` (*most closest root from previous*) and its value `4.8 * 10^32`.
So, definitely nth-root will be lower than `23`. Solved.

Don't ask how many basic arithmetic operations we performed. A lot.

## Liar approach

Actually, it's offensive to use the word "liar".
There is a whole technique called [Mathematical Fallacy](http://en.wikipedia.org/wiki/Mathematical_fallacy).
It based on some illegal math transformation (*division by zero, roots from negative numbers, etc.*) to get **any desired result**.

This is powerful instrument for mathematicians to show magic for not mathematicians.

Suppose, we don't know exact value of π. Not a problem, but we know [complex numbers](http://en.wikipedia.org/wiki/Complex_number).
And also the fact that `sqrt(-1) = i`. Where `i` is [imaginary unit](http://en.wikipedia.org/wiki/Imaginary_unit)

We have bad memory and can't remember π value, but we know [Euler's formula](http://en.wikipedia.org/wiki/Euler%27s_formula)

    e^(i * x) = cos(x) + i * sin(x)

x is a variable. Take `x = 2π`:

    e^(2π * i) = cos(2π) + i * sin(2π)

From the school math course we know: `cos(2π) = 1`, `sin(2π) = 0`. Just substitute:

    e^(2π * i) = 1 + i * 0

Everything multiplied by `0` is `0`. Next step:

    e^(2π * i) = 1

`a^b` gives `1` if `b = 0`. That we also know from logarithms.

    2π * i = 0

It means either `2` or `π` or `i` is zero. `2` is not zero. `i` is not zero. `π` we don't know.
Ok, that means `π` equals to `0`.

Returning to comparing our constants:

    e^0 vs 0^e

Left term is `1`, right is `0`. That means `e^π` **greater than** `π^e`.

The trick that we used hard to spot. Look carefully again through the proof.

The fallacy was where we implicitly took a logarithm and said that `2π * i = 0`.
The number `e^(2π * i)` is not real and we not allowed to logarithm it.
But if person who listens you don't know this fact... This is an excellent proof.

## Programming approach

![](http://imgs.xkcd.com/comics/e_to_the_pi_minus_pi.png)

You not allowed to use calculator. Nothing said about programming language.

One-liner in Java:

``` java
System.out.println(Math.pow(Math.E, Math.PI) > Math.pow(Math.PI, Math.E));
```

## Fun approach

Derived from joke:

> Scientific lab wasted a lot of money for calculating decimal places of π. They come up with idea, let's π will be equal to 4, to save money.
>
> -- But why 4 instead of 3?
>
> -- Because 3 reserved for e.

That way `e^π` equal to `81`, and `π^e` equal to `64`. `81 > 64`. Simple, concise way.

## Conclusion

There are much more fun about these irrational bastards. Math is fun.

If you were asked question of such type on interview, 99% that interviewer is joking and want to test your reaction.
Last solution would be good. But if he or she really expect solution with using calculus, nothing to lose.
