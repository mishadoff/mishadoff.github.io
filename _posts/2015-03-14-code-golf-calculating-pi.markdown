---
layout: post
title: "Code Golf: Calculating PI"
date: 2015-03-14 09:56
comments: true
categories: [code golf, programming, clojure]
mathjax: true
sharing: true
published: true
---

Let's celebrate [Pi day](http://en.wikipedia.org/wiki/Pi_Day) in clojure!

<!-- more -->

### Tweet

Exactly 140 characters to print 9 digits of $\pi$ (`3.141592654`)

``` clojure
(let[r reduce N(rest(range))t take](clojure.pprint/cl-format nil"~11f"
(r +' -2(map #(/(r *' 2(t % N))(r *'(t %(take-nth 2 N))))(t 99 N)))9))
```

### "Sugared" version

There are two parts of a problem:

- Calculate rational for $\pi$ using [arctangent approximation](http://en.wikipedia.org/wiki/Approximations_of_%CF%80#Arctangent)

$$
\pi = 2 \cdot (1 + \frac{1}{3} + \frac{1 \cdot 2}{3 \cdot 5}
+ \frac{1 \cdot 2 \cdot 3}{3 \cdot 5 \cdot 7} + ...)
$$

``` clojure
(defn pi 
  "Calculate PI by arctangent algorithm"
  [& {:keys [iterations limit] 
      :or {iterations 99 limit 9}}]
  (->> (range 1 (inc iterations))
       (map #(/ (reduce *' (take % (iterate inc 1)))
                (reduce *' (take % (filter odd? (iterate inc 1))))))
       (reduce +')
       (* 2)
       (dec)
       (dec)))
```

- Convert rational to decimal. Handled automatically by awesome `clojure.pprint/cl-format`

``` clojure
(clojure.pprint/cl-format nil "~4f" 22/7) ;;=> "3.14"
```

### Tweaks

To achieve 140 characters barrier, some functionality was relaxed

- Tweak number `99` to control number of iterations for $\pi$ calculation (if you have a high number of iterations, be patient - wait)
- Tweak number `9` to increase number of digits (that makes sense if you improved number of iterations)

**P.S.** Calculating $\pi$ and some other irrationals is available in
[numberto](https://github.com/mishadoff/numberto/blob/master/src/numberto/irrational.clj#L20)
