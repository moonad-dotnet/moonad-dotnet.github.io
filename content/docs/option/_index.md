---
title: Option
type: docs
weight: 3
sidebar:
  open: true
---

Also known as `Maybe`, this monad has as its goal preventing the `NullReferenceException` by notifying the existence or absense of a value. If the value of `T` is null it's converted to `None` and, if not, is just returned as `Some<T>` holding the `T` value.

[Option&lt;T&gt;]({{< relref "option" >}})
