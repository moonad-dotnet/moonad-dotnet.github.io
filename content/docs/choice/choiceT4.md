---
title: Choice<T1, T2, T3, T4>
weight: 3
type: docs
prev: /docs/choice/choiceT3
next: docs/choice/choiceT5
---

##### Also known as Either in some languages this monad offers you the possibility to choose one of two types to be hold by its instance.

Example:

```c# {filename="choice.cs"}
public Choice<int, string> Choose(bool returnInt)
{
    if(returnInt)
        return 1;

    return "This is a Choice!";
}
```
