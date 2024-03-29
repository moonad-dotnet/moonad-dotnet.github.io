---
title: ValueOption<T>
weight: 1
type: docs
---

This a type to represent the presence or absence of a `T` when it is a value type.

#### Operators

##### Implicit ValueOption&lt;T&gt; (T?)
Allows the client to set the T value to a ValueOption.

```c#
ValueOption<int> option = 1;
//option.IsSome = true

ValueOption<int> option = null;
//option.IsSome = false
```

##### Implicit T
Allows the client to get the T value from a ValueOption.

```c#
ValueOption<int> option = 1;
Console.Write(option);
//1
```

##### Implicit bool
Allows the client to know if a ValueOption contains value.

```c#
Option<int> option = 1;
if(option) 
    Console.WriteLine("Some");
else
    Console.WriteLine("None");
//"Some"
```

#### Methods

##### static Some(T)

Returns an instance of ValueOption with `T` value.

```c#
var option = ValueOption.Some(10);
Console.Write(option);
//10
```

##### static None
Returns an instance of ValueOption with no Value.

```c#
var option = ValueOption.None();
Console.Write(option);
//throw a ValueOptionException
```

##### Bind&lt;TResult&gt; (Func&lt;T, ValueOption&lt;TResult&gt;&gt;)
Executes a given function over a ValueOption, taking its `T` value, and returning an `ValueOption<TResult>`.

```c#
ValueOption<int> option = 1;
ValueOption<bool> bound = option.Bind((r) => r > 0 ? true : false));
Console.Write(bound);
//true
```

##### Contains (T)
Verify if a ValueOption is `Some` and it's `T` is equal to a given `T`. If so returns `true` otherwise returns `false`.

```c#
ValueOption<int> option1 = 1;
ValueOption<int> option2 = ValueOption<int>.None();
Console.Write(option1.Contains(option2));
//false
```

##### Count
Verify if an Option is `Some`. If so returns `1` otherwise returns `0`.

```c#
ValueOption<bool> option = true;
Console.Write(option.Count());
//1
```

##### DefaultValue (T)

Returns `T` from a ValueOption if it is `Some`, otherwise returns the given default value.

```c#
var option = ValueOption<int>.None();
Console.Write(option.DefaultValue(25));
//25
```

##### DefaultWith (Func&lt;T&gt;)

Returns `T` from a ValueOption if it is `Some`, otherwise returns the result of the given function.

```c#
ValueOption<int> option = ValueOption<int>.None();
Console.Write(option.DefaultWith(() => 25));
//25
```

##### Exists (Func&lt;T, bool&gt;)

Verify if `T` matches a given predicate and returns `true` if so or `false` if not or ValueOption is `None`.

```c#
ValueOption<int> option = 1;
Console.Write(option.Exists((v) => v > 0));
//true
```

##### Filter (Func&lt;ValueOption&lt;T&gt;, bool&gt;)

Return the ValueOption itself if its `None` or the predicate is `true`, otherwise returns `None`.

```c#
ValueOption<int> option = 1;
var result = option.Filter((o) => o > 0));
//1
```

##### Flaten (ValueOption&lt;ValueOption&lt;T&gt;&gt;)

Returns `T` of the inner ValueOption instance if it is `Some`, otherwise `None`.

```c#
ValueOption<ValueOption<bool>> ValueOption<ValueOption<bool>>.Some(true);
Console.Write(option.Flaten());
//true
```

##### Fold (Func&lt;TState, T, TState&gt;, TState)

Updates and returns `TState` by the given function if ValueOption is `Some`, otherwise returns `TState`

```c#
ValueOption<int> option = 1;
Console.Write(option.Fold((s, t) => s + t), 10);
//11
```

##### FoldBack (Func&lt;T, TState, TState&gt;, TState)

As Fold but inverting `T` and `TState` as the given function parameters order.

```c#
ValueOption<int> option = 1;
Console.Write(option.FoldBack((r, s) => s + r), 10);
//11
```

##### ForAll (Func&lt;T, bool&gt;)

Verifies if ValueOption is `None`. If so returns `true`, otherwise applies the given function to `T`.

```c#
ValueOption<int> option = 1;
Console.Write(option.ForAll((r) => r > 10));
//false
```

##### Get

Returns the `T` if `Some` or throws an exception if `None`.

```c#
ValueOption<int> option = ValueOption<int>.None());
Console.Write(option.Get());
//throws ValueOptionException;
```

##### Iter (Action&lt;T&gt;)

Executes the given action if ValueOption is `Some`.

```c#
ValueOption<int> option = 1;
option.Iter(() => Console.Write("It's ok!"));
//"It's ok!"
```

##### Map (Func&lt;T, ValueOption&lt;T&gt;&gt;)

Returns the ValueOption itself if `None` or the result of the given function applied to `T`.

```c#
ValueOption<int> option = 1;
Console.Write(option.Map((r) => r > 0 ? true : false));
//true
```

##### Map2 (Func&lt;ValueOption&lt;T&gt;&gt;, Func&lt;T, T, ValueOption&lt;T&gt;&gt;)

Returns `None` if the Option itself and the first parameter Option are `None`, otherwise returns the result of the given function with the Option itself and the first parameter Option.

```c#
ValueOption<int> option1 = 1;
ValueOption<int> option2 = 2;
Console.Write(option1.Map2((o1, o2) => o1 == o2 ? o1 : 0));
//0
```

##### Match (Action&lt;T&gt;, Action)

Executes the first action based on `T` if ValueOption is `Some` or the second action if `None`.

```c#
ValueOption<int> option = 1;
option.Match((t) => Console.Write(t)), () => Console.Write("None"));
//1
```

##### OrElse (Option&lt;T&gt;)

Returns the ValueOption itself if `Some` or the first parameter.

```c#
var option = ValueOption<T>.None();
Console.Write(option.OrElse(2));
//2
```

##### OrElseWith (Func&lt;Option&lt;T&gt;&gt;)

Returns the Option itself if `Some` or the result of the given function.

```c#
var option = ValueOption<T>.None();
Console.Write(option.OrElseWith(() => 25)));
//25
```

##### ToArray

Returns an array with `T` if Option is `Some`, otherwise an empty one.

```c#
ValueOption<int> option = 1;
Console.Write(option.ToArray().Length);
//1
```

##### ToList

As ToArray but returns a generic list with `T`.

```c#
ValueOption<int> option = 1;
Console.Write(option.ToList().Count);
//1
```

##### ToValueOption

Returns `Some` if `T` is not `null`, or a `None` if so.

```c#
var myString = "Option!";
var option = myString.ToOption();
//Some ("Option!")
```
