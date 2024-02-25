---
title: Option
type: docs
weight: 3
sidebar:
  open: true
---

This monad, also known as `Maybe`, has as its goal preventing the `NullReferenceException` by notifying the existence or absense of a value. Once a potentially null, or simply absent, value is converted to Option it's evaluated to a `Some` instance, which carry the value, or a `None` instance, which replaces the `null` and let the client works as `null` doesn't exists.

[Option&lt;T&gt;]({{< relref "option" >}})
