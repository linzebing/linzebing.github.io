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

The main limitation of providing only static factory methods is that classes without public or protected constructors cannot be subclassed. A second shortcoming of static factory methods is that they are hard for programmers to find.


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

## Prefer dependency injection to hardwiring resources
Pass the resource into the constructor when creating a new instance.

## Avoid creating unnecessary objects
For instance, `String.matches` internally creates a `Pattern` instance for the regular expression. We should reuse such `Pattern` instance for every invocation.

Prefer primitives to boxed primitives, and watch out for unintentional autoboxing.

## Eliminate obsolete object references
Null out references once they become obsolete. Whenever a class manages its own memory, the programmer should be alert for memory leaks. Two other cases are caches and listeners with other callbacks.

## Avoid finalizers and cleaners
Never do anything time-critical in a finalizer or cleaner. Never depend on a finalizer or cleaner to update persistent state. There is _severe_ penalty for using finalizers and cleaners. Finalizers have a serious security problem: then open your class up to finalizer attacks.

Just have your class implement _AutoCloseable_.

## Prefer _try-with-resources_ to _try-finally_
```java
static void copy(String src, String dst) throws IOException {
  try (InputStream in = new FileInputStream(src);
       OutputStream out = new FileOutputStream(dst)) {
         byte[] buf = new byte[BUFFER_SIZE];
         int n;
         while ((n = in.read(buf)) >= 0)
           out.write(buf, 0, n);
      }
}
```
