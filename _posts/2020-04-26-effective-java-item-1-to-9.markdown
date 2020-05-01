---
layout: post
title:  "[En][Reading] _Effective Java_ item 1 - 9"
date:   2020-04-26 21:03:36 +0530
---

## Consider static factory methods instead of constructors
One advantage of static factory methods is that, unlike constructors, they have names. Also static factory methods can avoid the restriction that a class can have only a single constructor with a given signature.

A second advantage of static factory methods is that, unlike constructors, they are not required to create a new object each time they're invoked. The ability of static factory methods to return the same object from repeated invocations allows classes to maintain strict control over what instances exist at any time.

A third advantage of static factory methods is that, unlike constructors, they can return an object of any subtype of their return type. One application of this flexibility is that an API can return objects without making their classes public.

A fourth advantage of static factories is that the class of the returned object can vary from call to call as a function of the input parameters.

A fifth advantage of static factories is that the class of the returned object need not exist when the class containing the method is written.

The main limitation of providing only static factory methods is that classes without public or protected constructors cannot be subclassed.

A second shortcoming of static factory methods is that they are hard for programmers to find.

- from—A type-conversion method that takes a single parameter and returns a corresponding instance of this type, for example:
```java
Date d = Date.from(instant);
```
- of—An aggregation method that takes multiple parameters and returns an in- stance of this type that incorporates them, for example:
```java
Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
```
- valueOf—A more verbose alternative to from and of, for example:
```java
BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
```
- instance or getInstance—Returns an instance that is described by its parameters (if any) but cannot be said to have the same value, for example:
```java
StackWalker luke = StackWalker.getInstance(options);
```
- create or newInstance—Like instance or getInstance, except that the
method guarantees that each call returns a new instance, for example:
```java
Object newArray = Array.newInstance(classObject, arrayLen);
```
- getType—Like getInstance, but used if the factory method is in a different
class. Type is the type of object returned by the factory method, for example:
```java
FileStore fs = Files.getFileStore(path);
```
- newType—Like newInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:
```java
BufferedReader br = Files.newBufferedReader(path);
```
- type—A concise alternative to getType and newType, for example:
```java
List<Complaint> litany = Collections.list(legacyLitany);
```

## Consider a builder when faced with many constructor parameters
```java
public static class Builder {
        // Required parameters
        private final int servingSize;
        private final int servings;
// Optional parameters - initialized to default values
private int calories
private int fat
private int sodium
private int carbohydrate = 0;
public Builder(int servingSize, int servings) { this.servingSize = servingSize; this.servings = servings;
}
public Builder calories(int val)
{ calories = val; return this; }
public Builder fat(int val)
{ fat = val; return this; }
public Builder sodium(int val)
{ sodium = val; return this; }
public Builder carbohydrate(int val)
{ carbohydrate = val; return this; }
    public NutritionFacts build() {
        return new NutritionFacts(this);
} }
```

In summary, the Builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters, especially if many of the parameters are optional or of identical type.

## Enforce the singleton property with a private constructor or an enum type
Making a class a singleton can make it difficult to test its clients because it’s impossible to substitute a mock implementation for a singleton unless it implements an interface that serves as its type.
```java
// Singleton with static factory
public class Elvis {
private static final Elvis INSTANCE = new Elvis(); private Elvis() { ... }
public static Elvis getInstance() { return INSTANCE; }
public void leaveTheBuilding() { ... } }
```

## Enforce noninstantiability with a private constructor
