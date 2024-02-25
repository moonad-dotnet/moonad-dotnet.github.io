---
title: Choice<T1, T2, T3>
weight: 2
type: docs
prev: docs/choice/choiceT2
next: docs/choice/choiceT4
---

This is a type which acts as a bag for one of three types.

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

##### Implicit T3
Allows the client to get the value of T2 when it was chosen.

```c#
Choice<int, string, bool> choice = false;
Console.WriteLine(choice);
//false
```