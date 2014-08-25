---
layout: post
title: "Stirling's Approximation"
date: 2013-02-23 01:38
comments: true
categories: [math, algorithms]
mathjax: true
published: true
---

Do you like factorials?

Probably, you do. If no, whatever, read this post to know
how get rid of factorials.

<!-- more -->

### TL;DR

$$
ln(n!) = n \cdot ln(n) - n + 1
$$

It is [Stirling's approximation](http://en.wikipedia.org/wiki/Stirling%27s_approximation)
or just Stirling's formula.
It allows to replace factorials with their approximation.
If you not interested in math, skip to [formula usage](#usage)

### Proof

Replace factorial with its definition:

$$
ln(n!) = ln(1 \cdot 2 \cdot 3 ...)
$$

Transform logarithm of multiplication to sum of logarithms:

$$
ln(1 \cdot 2 \cdot 3 ...) = ln(1) + ln(2) + ln(3) + ... + ln(n)
$$

Take a look at the integral $\int_a^b f(x) dx$.
Replace $f(x)$ with $ln(x)$, $a$ with $1$, and $b$ with $n$. Then use numerical
integration with step `1` and [right rectangle rule](http://en.wikipedia.org/wiki/Rectangle_method)

$$
\int_1^n ln(x) dx \approx ln(2) + ln(3) + ... + ln(n)
$$

Right-hand parts of two previous formulas are equal, with respect $ln(1)=0$. Then:

$$
ln(1 \cdot 2 \cdot 3 ...) \approx \int_1^n ln(x) dx
$$

Calculate integral using [integration by parts](http://en.wikipedia.org/wiki/Integration_by_parts):

$$
\int ln(x) dx = x \cdot ln(x) - \int x d(ln(x)) = x \cdot ln(x) - \int dt = x \cdot ln(x) - x + C
$$

Applying boundaries `[1..n]`

$$
n \cdot ln(n) - n + C - 1 \cdot ln(1) + 1 - C = n \cdot ln(n) - n + 1
$$

yields final result:

$$
ln(n!) = n \cdot ln(n) - n + 1
$$

Exactly what on the top.

### Developer Proof

It is much simpler proof, with lose of accuracy, but still valid for partial applications.

Replace factorial with its definition:

$$
ln(n!) = ln(1 \cdot 2 \cdot 3 ...)
$$

Transform logarithm of multiplication to sum of logarithms:

$$
ln(1 \cdot 2 \cdot 3 ...) = ln(1) + ln(2) + ln(3) + ... = \sum_{i=1}^n ln(i)
$$

From monotonic property of logarithms $a \leq b \rightarrow ln(a) \leq ln(b)$
take upper bound for each $ln(i)$:

$$
\forall i \; ln(i) \leq ln(n)
$$

Then, using worst case substitution, sum can be rewritten as:

$$
\sum_{i=1}^n ln(i) \leq \sum_{i=1}^N ln(n)
$$

But $ln(n)$ does not depend on `i`, so get rid of sum:

$$
\sum_{i=1}^n ln(n) = n \cdot ln(n)
$$

Done.

### <a id="usage"></a>Usage

* **Algorithm complexity**

No one cares about `O(log(n!))` of your algorithm, but everybody
knows that `O(n log n)`, [linearithmic complexity](http://en.wikipedia.org/wiki/Time_complexity#Linearithmic.2Fquasilinear_time),
is very good property of algorithm. By the way, do not scare people with factorial sign.

* **Overflow prevention**

If you try to calculate `ln(n!)` with most straightforward implementation
without suitable datatype, you'll likely get the number overflow exception, in spite
of result value is not very large. For example for `n=100` overall result is
approximately `363` (*Stirling's approximation gives 361*) where factorial value is $10^{154}$.

* **Speedup**

As far as I know, calculating factorial is `O(n)` complexity algorithm,
because we need `n` multiplications. Using Stirling's approximation we need to calculate $n^n$ term, that can
be calculated in `O(log n)` with [clever trick](http://en.wikipedia.org/wiki/Exponentiation_by_squaring)

* **Gamma function**

It can be surprising, but factorials defined not only for positive integers.
So, for example `2.5!` is completely valid expression. `-6!` also. They can be calculated
using [Gamma-functions](http://en.wikipedia.org/wiki/Gamma_function) for real numbers. Unfortunately, they are
pretty complicated, so people use Stirling's formula instead.

* **Partial applications**

Almost everything that using factorials can use Stirling's approximation. Permutations,
binomial coefficients, fractals, number theory and a huge amount of topics I'm not aware of.

**P.S.** Stirling's formula presented here is rough enough. More accurate formula is
$$n! = \sqrt{2\pi} n^{n + 0.5}e^{-n}$$.
Check its proof [here](http://www.sosmath.com/calculus/sequence/stirling/stirling.html).
