---
title: Option
type: docs
weight: 5
sidebar:
  open: false
---

`Option<T>` is a *sui generis* monad for representing the presence or absence of a value. When it wraps a value it becomes a `Some` type and when not a `None` type.

It allows `Option<T>` to deal with the `NullReferenceException` and it's fundamental to avoid this kind of exception comming from our own code. `NullReferenceException` is a so serious issue that Microsoft included the concept of `Nullable Reference Types` to deal with it.

Let's see how does it works. When dealing with a 3rd party library a given method can return null, e.g. an `int?`. To avoid any concern about a potential null and return the following code could be implemented:

```c#
public Option<T> MyMethod(Whatever obj)
{
    Option<int> value = obj.Method().ToOption(); //returns null
    //value is Option<int>.None
    return value;
}

public Option<T> MyMethod(Whatever obj)
{
    Option<int> value = obj.Method().ToOption(); //returns 10
    //value is Option<int>.Some (10)
    return value;
}
```

> **Info:** there is a value type version of `Option<T>` called [`ValueOption<T>`]({{< relref "/docs/value-option" >}}). It's specially usefull when dealing with hi-performance scenarios.

[Option&lt;T&gt;]({{< relref "option" >}})
