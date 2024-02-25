---
title: Choice<T1, T2>
weight: 1
type: docs
prev: /docs/choice
next: docs/choice/choiceT3
---

Choice is a type which acts as a bag for one of two types. It's not specialized to indicate an operation result, for this purpose please refer to Result<TResult, TError>.

#### Operators

##### Implicit T1
Allows the client to get the value of T1 when it was chosen.

```c#
Choice<int, string> choice = 12;
Console.WriteLine(choice);
//12
```

##### Implicit T2
Allows the client to get the value of T2 when it was chosen.

```c#
Choice<int, string> choice = "Twelve";
Console.WriteLine(choice);
//Twelve
```