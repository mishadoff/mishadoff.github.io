---
layout: post
title: "Announcing numberto"
date: 2013-09-01 04:23
comments: true
categories: [clojure, programming]
published: true
---

All you need to play with numbers!

<!-- more -->

[numberto](https://github.com/mishadoff/numberto)
- simple clojure library with a bunch of functions operating on numbers.

I created this project for two reasons:

1. Solving problems on [Project Euler](http://projecteuler.net), [4Clojure](http://www.4clojure.com/) and just playground with numbers needed common functions. I just extracted them to separate util library.
2. Practice to develop clojure project with unit tests, continuos integration, documentation and publishing to clojars.

### Get it

Current version is `0.0.2` available to download on
[Clojars](https://clojars.org/numberto/versions/0.0.2) or
as lein dependency `[numberto "0.0.2"]`

### Example usage

``` clojure
(number->roman 19) => "XIX"
(take 10 fibonacci) => (1 1 2 3 5 8 13 21 34 55)
(shift-right 123456789 3) => 789123456
(count-digits (! 100000)) => 456574
(factorize 234) => [2 3 3 13]
```

Can you read long numbers?

``` clojure 
(number-name 16532561257523723757234781264) =>
"sixteen octillion five hundred thirty two septillion five hundred sixty one sextillion
two hundred fifty seven quintillion five hundred twenty three quadrillion seven hundred
twenty three trillion seven hundred fifty seven billion two hundred thirty four million
seven hundred eighty one thousand two hundred sixty four"
```

### Structure

Each logical set of functions moved to separate namespace. Here is the list of namespaces:

* **Converters**.
  number to digits, char to digit, roman to arabic
* **Math**.
  Often used functions, such as `count-digits`, `shuffle-num`, `reverse-num` and some from common math `abs`, `gcd`, `lcm`
* **Seqs**.
  Various lazy sequences: `fibonacci`, `farey`, `palindromes`.
* **Primes**.
  Prime numbers lazy sequence, factorization and totient function
* **Factorial**.
  Standard and Improved version of factorial
* **Generators**.
  Generate random numbers
* **Printers**.
  Pretty print for long numbers

Check out [detailed intro](https://github.com/mishadoff/numberto/blob/master/doc/intro.md)

**P.S.** I still experimenting, so expect a lot of changes between versions until make something stable. Comments and advices are welcome!
