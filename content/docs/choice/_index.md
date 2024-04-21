---
title: Choice
type: docs
weight: 3
sidebar:
  open: false
---

This monad, also known as `Either` in some other languages, allows to choose one of two (or more) inner types to return from a given method. It may sound weird at first but consider the following scenario: a given user can choose to log in by using an e-mail or a identifier. Whatever the choice once identified the user's password will be required.

Let's see how `Choice<T1, T2, T3>` can help.

```c#
public Option<User> GetUser(string input)
{
    Choice<Email, Identifier, InvalidKey> key = ExtractKey(input);
    return key switch
    {
        Email => users.GetByEmail(key),
        Identifier => users.GetByIdentifier(key),
        InvalidKey => Option.None<User>()
    }
}
```

The method `ExtractKey` will try to convert the input to one of the two valid identifiers and if it couldn't it will return a type to express it. Then once with a valid key it will try to get the user identifier and perform the search for it in the database.

> **Info:** different from the other monads, which have a `Flatten` method, `Choice` doesn't. Does it means `Choice` isn't a monad? Far from it! Here the `Flatten` method is replaced by an implict operator to allow accessing the inner value as so as `Flatten` would do.

Here the list of available `Choice` types:

[Choice<T1, T2>]({{< relref "choiceT2" >}})\
[Choice<T1, T2, T3>]({{< relref "choiceT3" >}})\
[Choice<T1, T2, T3, T4>]({{< relref "choiceT4" >}})\
[Choice<T1, T2, T3, T4, T5>]({{< relref "choiceT5" >}})
