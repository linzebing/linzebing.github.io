---
layout: post
title:  "[En][Reading] _Effective Java_ item 10 - 19"
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
The _Clonable_ architecture is incompatible with normal use of final fields referring to mutable objects.
A better approach to object copying is to provide a copy constructor or copy factory.

## Consider implementing _Comparable_
Use the static compare methods in the boxed primitive classes or the comparator construction methods in the Comparator interface.
```
// Comparable with comparator construction methods
private static final Comparator<PhoneNumber> COMPARATOR = comparingInt((PhoneNumber pn) -> pn.areaCode)
.thenComparingInt(pn -> pn.prefix) .thenComparingInt(pn -> pn.lineNum);
public int compareTo(PhoneNumber pn) { return COMPARATOR.compare(this, pn);
}
```

## Minimize the accessibility of classes and members
Make each class or member as inaccessible as possible.

## In public classes,use accessor methods,not public fields
## Minimize mutability
You should provide a public mutable companion class for your immutable class only once you've confirmed that it's necessary to achieve satisfactory performance. e.g. _StringBuilder_.

Declare every field _private final_ unless there's a good reason to do otherwise.

## Favor composition over inheritance
If you are tempted to have a class B extend a class A, ask yourself the question: Is every B really an A?

## Design and document for inheritance or else prohibit it
A class to allow inheritance, constructors must not invoke overridable methods. Neither clone nor readObject may invoke an overridable method, directly or indirectly.
The best solution to this problem is to prohibit subclassing in classes that are not designed and documented to be safely subclassed. We can either make the class final or make all the constructors private or package-private and to add public static factories in place of the constructors.
