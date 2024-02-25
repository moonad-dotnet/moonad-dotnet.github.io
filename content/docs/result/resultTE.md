---
title: Result<TResult, TError>
weight: 2
type: docs
prev: /docs/result/result
---

##### This monad, also known as Maybe, has as its goal preventing the NullReferenceException by notifying the existence or absence of a value. Once a potentially null, or simply absent, value is converted to Option it's evaluated to a Some instance, which carry the value, or a None instance, which replaces the null and let the client works as null doesn't exists.

Example 1 - Preventing null from a 3rd party lib:

```go {filename="main.go"}
//lib.Method returns a string
var option = lib.Method().ToOption();

//The ToOption method will turn a null value into a None instance.
if(option.IsSome)
    Console.WriteLine($"Returned value: {option}");
if(option.IsNone)
    Console.WriteLine($"No returned value.");
```
