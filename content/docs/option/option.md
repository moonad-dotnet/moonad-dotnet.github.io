---
title: Option<T>
weight: 1
type: docs
---

This a type to represent the presence or absence of a `T` value. If `T` is null it would be transformed in `None` and, if not, will be returned as `Some<T>`.

#### Operators

##### Implicit Option&lt;T&gt; (T?)
Allows the client to set the T value to an Option.

```c#
Option<int> option = 1;
//Some<int> 1

Option<int> option = null;
//None
```

##### Implicit T
Allows the client to get the T value from an Option.

```c#
Option<int> option = 1;
Console.Write(option);
//1
```

##### Implicit bool
Allows the client to know if an Option is `Some` (`true`) or `None` (`false`).

```c#
Option<int> option = 1;
if(option) 
    Console.WriteLine("Some");
else
    Console.WriteLine("None");
//"Some"
```

#### Properties

##### IsSome
Indicates if an Option is Some, holding a T.

```c#
Option<int> option = 1;
Console.WriteLine(option.IsSome);
//true
```

##### IsNone
Indicates if an Option is Nome, holding no value.

```c#
Option<int> option = Option.None<int>();
Console.WriteLine(option.IsNome);
//true
```

#### Methods

##### Bind&lt;TResult&gt; (Func&lt;T, Option&lt;TResult&gt;&gt;)
Executes a given function over an Option, taking its `T` value, and returning an `Option<TResult>`.

```c#
Option<int> option = 1;
Option<string> bound = option.Bind((r) => r > 0 ? "Higher" : "Equal or Lower"));
//Some ("Higher")
```

##### Contains (T)
Verify if an Option is `Some` and it's `T` is equal to a given `T`. If so returns `true` otherwise returns `false`.

```c#
Option<int> option1 = 1;
Option<int> option2 = Option.None<int>();
Console.Write(option1.Contains(option2));
//false
```

##### Count
Verify if an Option is `Some`. If so returns `1` otherwise returns `0`.

```c#
Option<bool> option = true;
Console.Write(option.Count());
//1
```

##### DefaultValue (T)

Returns `T` from an Option if it is `Some`, otherwise returns the given default value.

```c#
var option = Option.None<int>();
Console.Write(option.DefaultValue(25));
//25
```

##### DefaultWith (Func&lt;T&gt;)

Returns `T` from an Option if it is `Some`, otherwise returns the result of the given function.

```c#
Option<int> option = Option.None<int>();
Console.Write(option.DefaultWith(() => 25));
//25
```

##### Exists (Func&lt;T, bool&gt;)

Verify if `T` matches a given predicate and returns `true` if so or `false` if not or Option is `None`.

```c#
Option<int> option = 1;
Console.Write(option.Exists((s) => s > 0));
//true
```

##### Filter (Func&lt;Option&lt;T&gt;, bool&gt;)

Return the Option itself if its `None` or the predicate is `true`, otherwise returns `None`.

```c#
Option<int> option = 1;
var result = option.Filter((o) => o > 0));
//Some (1)
```

##### Flaten (Option&lt;Option&lt;T&gt;&gt;)

Returns `T` of the inner Option instance if it is `Some`, otherwise `None`.

```c#
Option<Option<string>> Option.Some<Option<string>>("Option!");
Console.Write(option.Flaten());
//"Option!"
```

##### Fold (Func&lt;TState, T, TState&gt;, TState)

Updates and returns `TState` by the given function if Option is `Some`, otherwise returns `TState`

```c#
Option<int> option = 1;
Console.Write(option.Fold((s, t) => s + t), 10);
//11
```

##### FoldBack (Func&lt;T, TState, TState&gt;, TState)

As Fold but inverting `T` and `TState` as the given function parameters order.

```c#
Option<int> option = 1;
Console.Write(option.FoldBack((r, s) => s + r), 10);
//11
```

##### ForAll (Func&lt;T, bool&gt;)

Verifies if Option is `None`. If so returns `true`, otherwise applies the given function to `T`.

```c#
Option<int> option = 1;
Console.Write(option.ForAll((r) => r > 10));
//false
```

##### Get

Returns the `T` if `Some` or throws an exception if `None`.

```c#
Option<int> option = Option.None<int>;
Console.Write(option.Get());
//throws OptionException;
```

##### Iter (Action&lt;T&gt;)

Executes the given action if Option is `Some`.

```c#
Option<int> option = 1;
option.Iter(() => Console.Write("It's ok!"));
//"It's ok!"
```

##### Map (Func&lt;T, Option&lt;TResult&gt;&gt;)

Returns the Option itself if `None` or the result of the given function applied to `T` as an `Option<TResult>`.

```c#
Option<int> option = 1;
Console.Write(option.Map((r) => r > 0 ? true : false));
//true
```

##### Map2 (Func&lt;Option&lt;T&gt;&gt;, Func&lt;T, T, Option&lt;T&gt;&gt;)

Returns `None` if the Option itself and the first parameter are `None`, otherwise returns the result of the given function with the Option itself and the first parameter as an `Option<TResult>`.

```c#
Option<int> option1 = 1;
Option<int> option2 = 2;
Console.Write(option1.Map2((o1, o2) => o1 == o2 ? o1 : 0));
//0
```

##### Map3 (Func&lt;Option&lt;T&gt;&gt;, Func&lt;Option&lt;T&gt;&gt;, Func&lt;T, T, T, Option&lt;TResult&gt;&gt;)

Returns `None` if the Option itself and the first and second parameters are `None`, otherwise returns the result of the given function with the Option itself and the first and second parameters as an `Option<TResult>`.

```c#
Option<int> option1 = 1;
Option<int> option2 = 2;
Console.Write(option1.Map2((o1, o2) => o1 == o2 ? o1 : 0));
//0
```

##### Match (Action&lt;T&gt;, Action)

Executes the first action based on `T` if Option is `Some` or the second action if `None`.

```c#
Option<int> option = 1;
option.Match((t) => Console.Write(t)), () => Console.Write("None"));
//1
```

##### OrElse (Option&lt;T&gt;)

Returns the Option itself if `Some` or the first parameter.

```c#
var option = Option.None<T>();
option.OrElse(2);
//Some (2)
```

##### OrElseWith (Func&lt;Option&lt;T&gt;&gt;)

Returns the Option itself if `Some` or the result of the given function.

```c#
var option = Option.None<T>();
option.OrElseWith(() => 25));
//Some (25)
```

##### ToArray

Returns an array with `T` if Option is `Some`, otherwise an empty one.

```c#
Option<int> option = 1;
Console.Write(option.ToArray().Length);
//1
```

##### ToList

As ToArray but returns a generic list with `T`.

```c#
Option<int> option = 1;
Console.Write(option.ToList().Count);
//1
```

##### ToOption

Returns `Some` if `T` is not `null`, or a `None` if so.

```c#
var myString = "Option!";
var option = myString.ToOption();
//Some ("Option!")
```
