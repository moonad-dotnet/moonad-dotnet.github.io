---
title: Result
weight: 1
type: docs
next: docs/result/resultTE
---

Result is a simple indicator that a process ended and can indicate success or error. It's a an alternative to boolean returns to avoid primitive obsession.

#### Operators

##### Implicit bool
Indicates the success of failure by a boolean value.

```c#
Result result = false;
Console.WriteLine(result);
//false
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

#### Methods

##### Ok
Creates a Result instance representing a sucess.

```c#
var result = Result.Ok();
Console.WriteLine(result);
//true
```

##### Error
Creates a Result instance representing an error.

```c#
var result = Result.Error();
Console.WriteLine(result);
//false
```
