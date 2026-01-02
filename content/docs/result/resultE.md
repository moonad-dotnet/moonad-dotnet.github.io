---
title: Result<TError>
weight: 3
type: docs
prev: docs/result/resultTE
---

This is a type that indicates success or error in a given operation, it also can hold an error describing what was wrong. It offers a way to emulate a void method that could return an Exception.

#### Operators

##### Implicit Result&lt;TError&gt; (TError)
Allows the client to set the TError value to a Result.

```c#
Result<IError> result = new CustomError("Error");
Console.WriteLine(result);
//"Error"
```

##### Implicit TError
Allows the client to get the TError value from a Result.

```c#
Result<IError> result = new CustomError("Error");
Console.WriteLine(result);
//"Error"
```

##### Implicit bool
Allows the client to know if a Result is Ok or an Error.

```c#
Result<IError> result = new CustomError("Error");
if(result) 
    Console.WriteLine("Ok");
else
    Console.WriteLine(result.ErrorValue);
//"Error"
```

#### Properties

##### IsOk
Indicates if the operation was executed successfully.

```c#
var result = obj.Method(); //returns Result<TError> (Ok)
Console.WriteLine(result.IsOk);
//true
```

##### IsError
Indicates if the operation was not executed successfully.

```c#
var result = obj.Method(); //returns Result<TError> (Error);
Console.WriteLine(result.IsError);
//true
```

##### ErrorValue
Returns `TError` if Error or throws a `ErrorValueException` if OK.

```c#
Result<IError> result = obj.Method(); //returns new CustomError("Error")
Console.WriteLine(result.ErrorValue);
//"Error"
```
