---
title: State
type: docs
weight: 7
sidebar:
  open: false
---

The `State<T, S>` is a functional abstraction that encapsulates computations that depend on and modify state. It allows you to work with state in an immutable and composable way, avoiding the explicit passing of state between functions.

```csharp
var state = new State<int, int>(s => (s + 1, s + 2));
var mapped = state.Map(x => x * 2, state);
var (value, newState) = mapped.Run(3);
// value: 8 ((3 + 1) * 2)
// newState: 5 (3 + 2)
```

[State&lt;T, S&gt;]({{< relref "state" >}})