---
title: Result<TResult, TError>
weight: 2
type: docs
prev: /docs/result/result
---

This is a type that indicates success or error in a given operation, it also can hold the expected result for the operation or an error describing what was wrong.

#### Operators

##### Implicit Result<TResult, TError> (TResult)
Allows the client to set the TResult value to a Result.

```c#
Result<int, IError> result = 1;
Console.WriteLine(result);
//1
```

##### Implicit Result<TResult, TError> (TError)
Allows the client to set the TError value to a Result.

```c#
Result<int, IError> result = new CustomError("Error");
Console.WriteLine(result);
//"Error"
```

##### Implicit TResult
Allows the client to get the TResult value from a Result.

```c#
Result<int, IError> result = 1;
Console.WriteLine(result);
//1
```

##### Implicit TError
Allows the client to get the TError value from a Result.

```c#
Result<int, IError> result = new CustomError("Error");
Console.WriteLine(result);
//"Error"
```

##### Implicit bool
Allows the client to know if a Result is Ok or an Error.

```c#
Result<int, IError> result = new CustomError("Error");
if(result) 
    Console.WriteLine("Ok");
else
    Console.WriteLine("Error");
//"Error"
```

#### Properties

##### IsOk
Indicates if the operation was executed successfully.

```c#
var result = obj.Method(); //returns Ok
Console.WriteLine(result.IsOk);
//true
```

##### IsError
Indicates if the operation was not executed successfully.

```c#
var result = obj.Method(); //returns Error
Console.WriteLine(result.IsError);
//true
```

##### ResultValue
Returns `TResult` if Ok or throws a `ResultValueException` if Error.

```c#
Result<int, IError> result = obj.Method(); //returns 1
Console.WriteLine(result.ResultValue);
//1
```

##### ErrorValue
Returns `TError` if Error or throws a `ErrorValueException` if OK.

```c#
Result<int, IError> result = obj.Method(); //returns new CustomError("Error")
Console.WriteLine(result.ErrorValue);
//"Error"
```

#### Methods

##### Bind&lt;U&gt; (Func<TResult, Result&lt;U, TError&gt;>)
Executes a given function over a Result, taking its `TResult`, to return a `Result<U, TError>`.

```c#
Result<int, IError> result = 1;
Result<string, IError> bound = result.Bind((r) => r > 0 ? "Higher" : "Equal or Lower"));
Console.Write(bound);
//"Higher"
```

##### Contains (TResult)
Verify if another Result is Ok and equals a given Result. If so returns `true` otherwise returns `false`.

```c#
Result<int, IError> result1 = 1;
Result<int, IError> result2 = 1;
Console.Write(result1.Contains(result2));
//"true"
```

##### Count
Verify if a Result is Ok. If so returns `1` otherwise returns `0`.

```c#
Result<bool, IError> result = true;
Console.Write(result.Count(result));
//1
```

##### DefaultValue (TResult)

Returns `TResult` from a Result if it is Ok, otherwise returns the given default value.

```c#
Result<int, IError> result = new CustomError("Error");
Console.Write(result.DefaultValue(25));
//25
```

##### DefaultWith (Func&lt;TError, TResult&gt;)

Returns `TResult` from a Result if it is Ok, otherwise returns the result of the given function that takes `TError`.

```c#
Result<int, IError> result = new CustomError("Error");
Console.Write(result.DefaultWith((e) => $"Result: {e}"));
//Result: Error
```

##### Exists (Func&lt;TResult, bool&gt;)

Verify if `TResult` matches a given predicate and returns its result if so, or `false` if Result is not Ok.

```c#
Result<int, IError> result = 1;
Console.Write(result.Exists((r) => r > 0));
//true
```

##### Fold (Func&lt;TState, TResult, TState&gt;, TState)

Updates and returns `TState` by the given function if Result is Ok, otherwise returns `TState`

```c#
Result<int, IError> result = 1;
Console.Write(result.Fold((s, r) => s + r), 10);
//11
```

##### FoldBack (Func&lt;TResult, TState, TState&gt;, TState)

As Fold but inverting `TResult` and `TState` as the given function parameters order.

```c#
Result<int, IError> result = 1;
Console.Write(result.FoldBack((r, s) => s + r), 10);
//11
```

##### ForAll (Func&lt;TResult, bool&gt;)

Verifies if Result is `TError`. If so returns `true`, otherwise applies the given function to `TResult`.

```c#
Result<int, IError> result = 1;
Console.Write(result.ForAll((r) => r > 10));
//false
```

##### Iter (Action&lt;TResult&gt;)

Executes the given action if Result is Ok.

```c#
Result<int, IError> result = 1;
result.Iter(() => Console.Write("It's ok!"));
//It's ok!
```

##### Map&ltU&gt; (Func&lt;TResult, Result&lt;U, TError&gt;&gt;)

Produces a `Result<U, TError>`. If Result is OK returns the result of the given function, otherwise an instance of `Result<U, TError>` holding the original `TError`.

```c#
Result<int, IError> result = 1;
Console.Write(result.Map((r) => r > 0 ? true : false));
//true
```

##### MapError&ltU&gt; (Func&lt;TError, Result&lt;TResult, U&gt;&gt;)

Produces a `Result<TResult, U>`. If Result is OK returns an instance of `Result<U, TError>` holding the original `TResult`, otherwise the result of the given function.

```c#
Result<int, IError> result = new CustomError("Error");
Console.Write(result.Map((e) => e == "Error" ? "Not good!" : "It's ok!"));
//"Not good!"
```

##### ToArray

Returns an array with `TResult` if Result is Ok, otherwise returns an empty one.

```c#
Result<int, IError> result = 1;
Console.Write(result.ToArray().Length);
//1
```

##### ToList

As ToArray but returns a generic list with `TResult`.

```c#
Result<int, IError> result = 1;
Console.Write(result.ToList().Count);
//1
```

##### ToOption

Returns an `Option<TResult>` with `TResult` if Result is Ok or a `Option.None<TResult>` if not.

```c#
Result<int, IError> result = new CustomError("Error");
Console.Write(result.ToOption());
//Option<TResult>.None
```

##### ToValueOption

As `ToOption` but returning an instance of `ValueOption<TResult>`

```c#
Result<int, IError> result = new CustomError("Error");
Console.Write(result.ToValueOption());
//ValueOption<TResult>.None
```
