---
layout: post
title: "Littlepot: Autofilled Cache"
date: 2015-10-04 14:22
comments: true
categories: [programming, clojure, library]
sharing: true
published: true
---

> Cook, little pot, cook!
>
>  -- The Magic Porridge Pot, Brothers Grimm

<!-- more -->

I've just released [littlepot](https://github.com/mishadoff/littlepot), tiny library devoted to transform batched request into single element request.

# The Problem

Imagine you develop a service which return one random image and some witty text below. As an image provider, you have chosen some `http://image.provider.com`, because they have a free limited API. Their endpoint `http://image.provider.com/api/random` is exactly what you need.

You wrote a function to get batched data.

``` clojure
(def get-batch []
  (-> api-endpoint
      http/get
      json/parse))
```

But your service need to return one image `(get-image)`

# Wasting Requests

You lazy enough, so you just return random image from fresh batch.

``` clojure
(defn get-image []
  (rand-nth (get-batch)))
```

Simple enough, unless you'll encounter `RateLimitException` because guys from `image.provider.com` too greedy and allow only 100 requests per hour, but you just utilize 2% of all images.

## Storage

Obvious solution: save images somewhere and return from there.

Congratulations, you discovered *state*, your life will never be the same.

Assume you introduced atom over list to save the data from the batch. Now every time you retrieve image, you need to remove it from cached batch.

``` clojure
(defn get-image []
  (let [element (first @batch)]
    (swap! batch rest)
    element))
```

## Refill

Good enough, but the cache is not infinite, you need to refill it when data is over.

``` clojure
(defn get-image []
  (when (empty? @batch)
        (fill batch))
  (let [element (first @batch)]
    (swap! batch rest)
    element))
```

All clients are blocked until you cache is filled
and see little latency.

*What if we could send request in background, allowing clients still retrieve the data?*  

## Littlepot

Meet _littlepot_, solutions to some of these problems.

**Storage.** It is backed by `clojure.lang.PersistentQueue`, clojure queue implementation, so you don't need to care about efficient storage.

**Autofill.** It sends request for next batch in a background, when your cached data is close to exhaustion, so the process of filling cache goes automatically and silently.

**Non-blocking.** You do not need to wait when data appears in cache; if something there, return it, if not, return :no-data.

**Composable.** Having function to retrieve single element (get-one) you can easily get fifty elements by calling (take 50 (repeatedly get-one)).

**Concurrency.** It encapsulates whole state in ref and uses STM, so multiple consumers allowed. Also, guaranteed that at most one batch will be in progress.

[Getting Started Guide](https://github.com/mishadoff/littlepot#usage)
or some
[Advanced Usages](https://github.com/mishadoff/littlepot/blob/master/doc/tutorial.md)
could be found on github.
