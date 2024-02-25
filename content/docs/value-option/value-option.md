---
title: ValueOption<T>
weight: 1
type: docs
---

##### Also known as Either in some languages this monad offers you the possibility to choose one of two types to be hold by its instance.

Example

```c# {filename="choice.cs"}
public Choice<int, string> Choose(bool returnInt)
{
    if(returnInt)
        return 1;

    return "This is a Choice!";
}
```
