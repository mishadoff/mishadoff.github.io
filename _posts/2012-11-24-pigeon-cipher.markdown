---
layout: post
title: "Pigeon Cipher"
date: 2012-11-24 14:52
comments: true
sharing: true
categories: [news, cryptography]
---

The skeleton of the pigeon was found in England.
There is small red box tied to its leg with the handwritten ciphered message.
Britain cryptographers failed the attempt to decipher the message and it was released in public.
It can be a good puzzle for a weekend.

<!-- more -->

### Input

![](http://news.bbcimg.co.uk/media/images/64345000/jpg/_64345866_masons_pigeon_code_07.jpg)

The paper consists of some parts [Full PDF scan](http://i2.cdn.turner.com/cnn/2012/images/11/23/large-pigeon-message-1.pdf):

* **Title:** "Pigeon Service"
* **TO:** XO2
* **Ciphered Message**

```
    AOAKN HVPKD FNFJW YIDDC
    RQXSR DJHFP GOVFN MIAPX
    PABUZ WYYNP CMPNW HJRZH
    NLXKG MEMKK ONOIB AKEEQ
    WAOTA RBQRH DJOFM TPZEH
    LKXGH RGGHT JRZCQ FNKTQ
    KLDTS FQIRW AOAKN 27 1525/6
```

* **Pigeon IDs:** `NURP 40 TW 194` and `NURP 37 OK 76`.
There are was two pigeons as we can see from the field *Number of copies sent*.
* **LIB 1625**
* **Time of origin:** 1522
* **Sender's signature:** W Stit Sjt

### Output

The most interesting part of this paper is the ciphered message. It consists of 27 5-letter blocks.
At the end of the message we can see number 27, probably some sort of control sum of blocks.
First block `AOAKN` equals to the last one, so it can be some sort of indicator of the cipher or key used.

From pigeon ids we can know that they were born in `1940` and `1937` years respectively, so this cipher from World War II.

Probably, the message was decoded with [one time pad](http://en.wikipedia.org/wiki/One-time_pad) cipher, that
has a perfect secrecy. So, maybe this cipher will never be decrypted.

*Note:* It can be decrypted with bruteforce algorithm, but in that case we can obtain any message.

Anyway, if you interested in this topic, try to spend a couple of hours for breaking the cipher,
it is a good workout for your brain.

### Links

1. [BBC](http://www.bbc.co.uk/news/uk-20458792)
2. [CNN](http://edition.cnn.com/2012/11/23/world/europe/uk-wwii-pigeon-mystery/index.html)

**P.S.** Don't be addicted, you have a lot things to do instead of breaking this stupid cipher.
Very unlikely, but it can be some sort of a joke.
