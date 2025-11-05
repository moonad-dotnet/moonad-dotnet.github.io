---
title: What are Monads?
type: docs
weight: 1
---

A monad is a wrapper to a generic type `T` which allows its manipulation but always returning a new wrapper.

Confusing? Let's explain it.

In .NET we have nullable types. Once we want to admit a null value in a value type we can indicate that as follows:
```c#
int? x = null;
```

Simple. Right?

When we dig a bit to understand the code above we can see something interesting: a type called `Nullable<T>` with the followig anathomy:

```c# 
Nullable<int> x = null;
Console.WriteLine(x.HasValue);
//false
...
int? x = 10;
int? y = 20;
Console.WriteLine(x + y); //Same as x.Value + y.Value
//30
```

So we have a type acting as a wrapper to `int` allowinfg operations as it was really one and also admiting null as a value and returning another `Nullable<T>`.

It means that `Nullable<T>` is a monad? Actually not, but let's explore a little more.

### More than a wrapper
Despite the fact that being a wrapper is a fundamental trait of a monad, it doesn’t completely describe what a monad is. Besides allowing the wrapping of some value a monad needs to be able to perform transformations which require a special function to that, the `Bind` method in our case.

Let's see `Bind` in action with one of the most known monad, the `Option<T>` type (a.k.a. `Maybe` in some other languages). Let's see the code:

```c#
var value = obj.Method() //returns 50
               .ToOption<int>()
               .Bind((v) => r > 0 ? "Higher than zero!" : "Lower os equals zero!")
               .Contains("Higher than zero!");
Console.WriteLine(value);
//true
```

Notice the function `Bind`. It operates on `Option<int>`, turning it on an `Option<string>` which in turn once chainned to `Contains` returns a bool `bool`.

Although `Bind` is a requirement to define a monad it is totally possible to have several others to manipulate the wrapped value simplifying its use (as `Contains` above do).

### Flattening
[Mark Seemann](https://blog.ploeh.dk/2022/03/28/monads/#a342785804ef4d5ebb6e88cef8140432), on his series about monads says a monad is a functor you can flatten.

That means that every monad needs to be able to return a wrapped monad by a method called `Flatten`.

Let's see another simples sample also with `Option<T>`:

```c#
var option = Option.Some<Option<int>>(obj.Method()); //returns Option<int> (10)
Console.Write(option.Flatten());
//10
```

The code above represents an `Option<T>` which could wrap another `Option<T>`, the `obj.Method()` return and to get it the `Flatten` method must be called, allowing so the inner value to be accessed (10 in this case).

Once we have this understanding, let's install Moonad and know a bit more of each available monad.
