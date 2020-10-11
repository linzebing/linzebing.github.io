---
layout: post
title:  "[En][Reading] _Effective Java_ item 10"
date:   2020-10-02 21:03:36 +0530
---

## Obey the general contract when overriding _equals_
Must satisfy: reflexive, symmetric, transitive, consistent.
**There is no way to extend an instantiable class and add a value component while preserving the _equals_ contract**.
1. Use the == operator to check if the argument is a reference to this object.
2. Use the instanceof operator to check if the argument has the correct type.
3. Cast the argument to the correct type.
4. For each “significant” field in the class, check if that field of the argument matches the corresponding field of this object.

## Always override _hashCode_ when you override _equals_
## Always override _toString_
Provide programmatic access to the information contained in the value returned by _toString_.

## Override _clone_ judiciously
Immutable classes should never provide a clone method.