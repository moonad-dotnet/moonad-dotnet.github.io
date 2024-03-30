---
title: Introduction
---

### Installing
The project's package can be found on [Nuget](https://nuget.org/packages/moonad) and installed by your IDE or shell as following:

```shell
dotnet add package Moonad
```

or

```shell
PM> Install-Package Moonad
```

#### A Note on Null Reference Types

Since our main goal is to protect the user from `NullReferenceException` we strongly recommend the use of [Nullable Reference Types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types) on any project which uses this lib.

### The Monads

F# offers in it's core library four monads to help you to have more flexibility when working with primitives and also potential null occurrences. So this library do the same.

#### [Choice]({{< relref "choice" >}})
Also known as `Either` in some languages this monad offers you the possibility to choose one of N types to be hold by its instance.

```c#
public Choice<int, string> Choose(bool returnInt)
{
    if(returnInt)
        return 1;

    return "This is a Choice!";
}
```

####  [Result]({{< relref "result" >}})

A type to express the final state of a given processing revealing its success of failure and optionally carrying a value or an error.

Example: Success indicator

```c#
public Result Send(Message message)
{
    try
    {
       ... 
       return Result.Sucess();
    }
    catch(Exception exc)
    {
        ...
        return Result.Failure();
    }
}
```

Example: Value and error returning

```c#
public Result<User, IError> Create(...)
{
    //When a guard clause is actioned
    return new EmptyUserNameError();

    //When all is valid
    return new User(...);
}
```

#### [Option]({{< relref "option" >}})

This monad, also known as `Maybe`, has as its goal preventing the `NullReferenceException` by notifying the existence or absense of a value. Once a potentially null, or simply absent, value is converted to Option it's evaluated to a `Some` instance, which carry the value, or a `None` instance, which replaces the `null` and let the client works as `null` doesn't exists.

Example: Preventing null from a 3rd party lib
```c#
//lib.Method returns a string

var option = lib.Method().ToOption();
//The ToOption method will turn a null value into a None instance.

if(option.IsSome)
    Console.WriteLine($"Returned value: {option}");
if(option.IsNone)
    Console.WriteLine($"No returned value.");
```

Example: Creating an Option explicitly
```c#
public Option<int> ReturnWhenGreaterThanZero(int input) =>
    input > 0 ? input : Option.None<int>();
```

#### [ValueOption]({{< relref "value-option" >}})
It has the very same concept as Option but is intended to use with value types to be faster in performance critical scenarios.

Example: Preventing null from a 3rd party lib
```c#
//lib.Method returns a nullable int

var option = lib.Method().ToValueOption();
//The ToOption method will turn a null value into a None instance.

if(option.IsSome)
    Console.WriteLine($"Returned value: {option}");
if(option.IsNone)
    Console.WriteLine($"No returned value.");
```

Example: Creating an Option explicitly
```c#
public ValueOption<int> ReturnWhenGreaterThanZero(int input) =>
    input > 0 ? input : ValueOption<int>.None;
```
