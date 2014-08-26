---
layout: post
title: "Clojure Pitfalls: Part 1"
date: 2014-05-22 01:48
comments: true
sharing: true
categories: [programming, clojure]
published: true
---

Clojure is a great language, and as every language,
it has some unobvious and strange things, which seem confusing.

Some of such things are gathered here.
Most of them encountered by stupidity, docs misunderstanding,
clojure specifics and procrastination.

<!-- more -->

### 1. Anybody home?

``` clojure
(contains? [10 20 30] 20) => false
```

Surprised?

The issue here is `contains?` function
checks if *key* (not a value!) is present in collection.
For vector - key is index, and obviously there is no 20th element.

If you need to check if *value* is present in collection,
use `some` predicate:

``` clojure
(some #(= % 20) [10 20 30]) => true
```

Keep in mind this function works in linear time.
If you need to call it often, build a set and use
it as a function.

``` clojure
(def numbers (set [10 20 30]))
(numbers 20) => 20 ;; treated as true
(numbers 1) => nil ;; treated as false
```

Note: if set contains `nil` or `false`, it won't work.
More discussion [here](http://stackoverflow.com/questions/3249334/test-whether-a-list-contains-a-specific-value-in-clojure).

### 2. Breaking the Law

You need stack. In clojure you can treat `list` like a stack.

``` clojure
;; take stack head
(peek '(1 2 3)) => 1

;; pop stack
(pop '(1 2 3)) => (2 3)

;; push item into stack
(cons 0 '(1 2 3)) => (0 1 2 3)
```

Everything is fine. But if you call `pop` after `cons`...

> ClassCastException clojure.lang.Cons cannot be cast to clojure.lang.IPersistentStack

The problem is `cons` is type destructive operation.

``` clojure
(type '(1 2 3))            => clojure.lang.PersistentList
(type (cons 0 '(1 2 3)))   => clojure.lang.Cons
```

And clearly, there is no `pop` defined for `Cons` type.

To preserve collection type, use `conj` function.

``` clojure
(type '(1 2 3))            => clojure.lang.PersistentList
(type (conj '(1 2 3) 0))   => clojure.lang.PersistentList
```

### 3. Apples or Oranges? Part I

What's the difference between
`(last [1 2 3])` and `(last '(1 2 3))`?

Functionally there is no difference, both snippets return `3`

Moreover, from performance view there is no difference as well.
Both snippets work in linear time. Linear. O(n). Even for vector.

What's the difference between
`(count [1 2 3])` and `(count '(1 2 3))`?

Much better here, `count` works in constant time for vector
and linear for list. So, if you really need fast `last` for vector,
use `count`.

``` clojure
;; 10M size vector
(def v (vec (range 1e7)))

(time (last v)) => 9999999
;; "Elapsed time: 1704.172859 msecs"

(time (get v (dec (count v)))) => 9999999
;; "Elapsed time: 0.087302 msecs"
```

*Huge difference*.

Do not use vector specific functions
to list. Also, consider using `mapv`
instead of `map` when processing vectors.

### 4. Apples or Oranges? Part II

Everybody knows `reduce` and `apply` functions.
Sometimes using choosing between them does not make any difference.

``` clojure
;; 100K numbers
(def N (repeatedly 1e6 #(rand-int 100)))

(time (reduce + N)) => 49503683
;; "Elapsed time: 311.496332 msecs"

(time (apply + N)) => 49503683
;; "Elapsed time: 339.624222 msecs"
```

Sometimes it does.

``` clojure
;; 10K strings
(def S (repeatedly 1e5 (fn [] "1")))

(time (count(apply str S))) => 100000
;; "Elapsed time: 35.027218 msecs"

(time (count(reduce str S))) => 100000
;; "Elapsed time: 24249.845948 msecs"
```

### 5. Refactor

Conditionals are horses of programming language.

``` clojure
(def status 500)
(cond (= status 403)    "login.html"
      (= status 404)    "not_found.html"
      (= status 500)    "server_fault.html"
      (= status 200)    "index.html"
      :else             "something_wrong.html")
```

Pretty straightforward, but a piece `(= status %)` look ugly.
Let's refactor it a bit and avoid repetition with `condp`

``` clojure
(def status 500)
(condp = status
       403    "login.html"
       404    "not_found.html"
       500    "server_fault.html"
       200    "index.html"
       :else  "something_wrong.html")
```

Much better. Except we broke previous functionality.
If pass invalid status (let's say 201) we get an exception:

> IllegalArgumentException No matching clause: 201

And that's right: sugared keyword `:else` in `cond` behaves like regular clause in `condp`
To make working solution, just remove `:else`.

``` clojure
(condp = status
       403    "login.html"
       404    "not_found.html"
       500    "server_fault.html"
       200    "index.html"
       "something_wrong.html")
```

### 6. Don't trust your eyes

Get this portable, pure, thread-safe, well-documented,
so beatiful and very awesome function:

``` clojure
(defn add [a b]
  "Performs addition operation.
   More about addition here: http://en.wikipedia.org/wiki/Addition"
  (+ a b))
```

What's wrong here? Is it really *well-documented*? 

If you have sources, then yes, you see all documentation.
But if you are library user you have no idea what function `add` might do. 

``` clojure
(doc add)
; -------------------------
; user/add
; ([a b])
;   nil
```

Misplaced docstring. Transpose it with arglist.

``` clojure
(defn add 
  "Performs addition operation.
  More about addition here: http://en.wikipedia.org/wiki/Addition"
  [a b]
  (+ a b))

(doc add)
; -------------------------
; user/add
; ([a b])
;   Performs addition operation.
;   More about addition here: http://en.wikipedia.org/wiki/Addition
```

Note: clojure lint tool [eastwood](https://github.com/jonase/eastwood)
is able to find this mistake.

### 7. It depends

There is less known, but useful feature *dynamic binding*

``` clojure
(def ^:dynamic *DEBUG* false)
```

If your code depends on `*DEBUG*` binding,
its behaviour can be modified without changing
function definition

``` clojure
(defn square [a]
  (when *DEBUG* (println "call square"))
  (* a a))
  
(square 10) => 100

(binding [*DEBUG* true]
  (square 10))
;; call square
=> 100
```

If you have another value, that depends on dynamic binding, it is not dynamic.

``` clojure
(def ^:dynamic *DEBUG_PREFIX*
  (if *DEBUG* "DEBUG=TRUE" "DEBUG=FALSE"))

(binding [*DEBUG* true]
  *DEBUG_PREFIX*)
=> "DEBUG=FALSE"
```

To fix that make it as a no-arg function. It will obtain
dynamic binding right after the call.

``` clojure
(defn- debug-prefix []
  (if *DEBUG* "DEBUG=TRUE" "DEBUG=FALSE"))

(binding [*DEBUG* true]
  (debug-prefix))
=> "DEBUG=TRUE"
```

### 8. MAP: Access Denied

There are three ways to access map value

``` clojure
(def m {:name "Quentin Tarantino"})

;; 1. By keyword
(:name m) => "Quentin Tarantino"

;; 2. By map as a function
(m :name) => "Quentin Tarantino"

;; 3. Using get
(get m :name) => "Quentin Tarantino"
```

What version to use is a matter of style,
because they are almost always interchangeble,
but keep in mind some caveats.

First approach is bad, when your keys are strings
or whatever-not-a-function.

``` clojure
(def m {"name" "Quentin Tarantino"})

("name" m) => ClassCastException java.lang.String cannot be cast to clojure.lang.IFn
```

Second one is able to handle that, but instead it can throw NPE, when map is nil

``` clojure
(def m nil)

(m :name) => NullPointerException
```

Using `get` we avoid these pros, and access become more obvious.

I prefer first version when using keyword explicitly, otherwise access via `get`

### 9. Thread Hack

Thread macro is a convenient way to implement sequential processing.

Thread First `->` inserts previous result
as a *first* argument for the next expression.

``` clojure
(-> (http/get URL)
    (validate-status)
    :body
    :data
    (first))
```

Thread Last `->>` does the same,
but insert previous result as a *last* argument 

``` clojure
(->> (range 100)
     (map square)
     (filter even?)
     (reduce +))
```

If you need to insert previous result into specific position,
wrap next expression to one-arg anonymous functions.

``` clojure
(->> (http/get URL)
     (#(validate-status % :throw-exception))
     :body
     :data
     (#(conj % [:guard])))
```

### 10. Just Words

This one is not related to clojure, but interesting. Consider problem:

> Find all words in a string

Pretty obvious, huh?

``` clojure
(re-seq #"\w+" "Hello, world!") =>
("Hello" "world")
```

We don't want numbers and underscore appear inside word

``` clojure
(re-seq #"\w+" "Hello, w_orld 1984!") =>
("Hello" "w_orld" "1984")
```

Okay, use just letters:

``` clojure
(re-seq #"[a-zA-Z]+" "Hello, w_orld 1984!") =>
("Hello" "w" "orld")
```

No, we don't want broken words. Use `\b` word boundary regex.

``` clojure
(re-seq #"\b[a-zA-Z]+\b" "Hello, w_orld 1984!") =>
("Hello")
```

Solved?

Not for ukrainian

``` clojure
(re-seq #"\b[a-zA-Z]+\b" "Привіт, світ 1984!") =>
nil
```

And for every language which contains non-ascii letters in alphabet.
Use unicode letter regexp `\p{L}` instead.

``` clojure
(re-seq #"\b\p{L}+\b" "Привіт, світ 1984!") =>
("Привіт" "світ")
``` 
