---
layout: post
title: "Code Golf: Game of Life"
date: 2013-03-07 17:59
comments: true
categories: [programming, clojure, code golf]
published: true
---

Conway's Game of Life in a tweet.

<!-- more -->

Recently, my friend pointed out me an article [Life in a tweet](http://trelford.com/blog/post/140.aspx)
where [Game of Life](http://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) was implemented in one tweet
(*less than 140 characters*) in Ruby and F#. I took this challenge.

### Tweet

Here is the final version in `137` characters of Clojure:

``` clojure
(fn[g r](reduce(fn[i j](update-in i j(fn[v](get[v 1]
(-(apply +(map #(get-in g% 0)(for[a[-1 0 1]b[-1 0 1]]
(map + j[a b]))))v 2)0))))g r))
```

### "Sugared" version

Translate tweet to more readable code:

``` clojure
(defn step-sugar [grid range]
  (let [count-neighbours ;; determine number of neighbours
        (fn[[i j]]
          (reduce + (map #(get-in grid % 0)
                         (for[a [-1 0 1] b [-1 0 1]]
                           [(+ i a) (+ j b)]))))
        new-value  ;; calculate new value for cell
        (fn [v [i j]]
          (let [c (- (count-neighbours [i j]) v)]
            (cond (= 3 c) 1
                  (= 2 c) v
                  :else 0)))
        evolve-cell ;; update cell in a grid
        (fn[g i] (update-in g i #(new-value % i)))]
    (reduce evolve-cell grid range)))
```

Actually, this version is cheating,
due to passing one extra-parameter `range` that contains all indices
need to be updated in form `[[0 1] [1 1]]...`

On the other side it gives us two features:

* **Works for rectangular grid**. Although, initial
implementation assumes the world is square.
* **Specific part of world can be updated**. It gives possibility
to update specific area in the world, for example *west organisms evolve faster*.

To use this function you need:

* World

``` clojure
(def grid [[0 0 0]
           [1 1 1]
           [0 0 0]])
```

* Evolution

``` clojure
(defn evolution [g]
  (let [p (count g) q (count (get g 0))
        range (for [i (range p) j (range q)][i j])]
    (iterate #(step-sugar % range) g)))
```

* Visualization

``` clojure
(defn print-grid [g]
  (doseq [s (map #(apply str (replace {0 "." 1 "⚫"} %)) g)]
    (println s)))
```

### Testing

To make sure results are correct use the following
`run` function and compare results to some common
[examples](http://en.wikipedia.org/wiki/Conway%27s_Game_of_Life#Examples_of_patterns).

``` clojure
(defn run [grid]
  (doseq [g (evolution grid)]
    (print-grid g)
    (Thread/sleep 1000)))
```

Pattern examples:

``` clojure
(def block [[0 0 0 0]
            [0 1 1 0]
            [0 1 1 0]
            [0 0 0 0]])

(def glider [[0 0 1 0 0 0 0 0]
             [1 0 1 0 0 0 0 0]
             [0 1 1 0 0 0 0 0]
             [0 0 0 0 0 0 0 0]
             [0 0 0 0 0 0 0 0]
             [0 0 0 0 0 0 0 0]])
```

Full history of implementation and more life patterns
[available here](https://github.com/mishadoff/prog-experiment/blob/master/clojure/life.clj)

Enjoy!
