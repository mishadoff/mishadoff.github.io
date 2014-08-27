---
layout: post
title: "Software Acronyms"
date: 2014-08-11 18:55
comments: true
categories: [programming, fun]
published: false
---

Everyone loves acronyms.

No matter what are you talking about,
using acronyms is important and makes you sound clever.

Even if you talk about chickens.

<!-- more -->

## OOP

**O**bject-**O**riented **P**rogramming.

Everything is an object. Chicken is an object.

Chicken has *state* (`color`, `numberOfLegs`) and *behaviour* (`move(int)`, `cluck()`)

Main principles of OOP:

- Inheritance
  `Chicken` inherits `Animal` state and behaviour
- Encapsulation
  You don't know how `cluck()` works, but if you tell `cluck()`, chicken clucks
- Polymorphism
  Why `chicken1.cluck()` and `chicken2.cluck()` cause different sounds?

## GRASP

**G**eneral **R**esponsibility **A**ssignment **S**oftware **P**atterns

Related to OOP, and therefore to chickens.

Patterns include:

- Low Coupling
- High Cohesion
- Indirection
- Information Expert
- Controller
- Creator
- Polymorphism
- Protected Variations
- Pure Fabrication

;; TODO

## GOF

**G**ang **O**f **F**our (Design Patterns)

;; TODO

## KISS

**K**eep **I**t **S**imple, **S**tupid

What can be simpler than chicken?

## SOLID

Meta-acronym stands for

## DRY

**D**on't **R**epeat **Y**ourself

Chicken, chicken, chicken, chicken

## YAGNI

**Y**ou **A**ren't **G**onna **N**eed **I**t

## TDD

**T**est **D**riven **D**evelopment

``` java
Chicken chicken = new Chicken();
```

```
TEST FAILED: Cound
```

``` java
Chicken chicken = new Chicken();
chicken.cluck();
```
