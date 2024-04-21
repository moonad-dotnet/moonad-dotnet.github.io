---
title: Result
type: docs
weight: 4
sidebar:
  open: false
---

This type represents a result from an operation. It's usually confused with `Choice<T1, T2>` because both can return two types in some occasions but `Result` has distinct traits to 
indicate if an operation was successfull (`IsOk`) or not (`IsError`), something that would demand a pattern matching from choice.

Result is advisable when a givet method needs to indicate if its operation was sucessfull, as calculus, transformations or persisting. If the method must carry some object to detail the result of the operation (as the number of lines affected by a SQL instruction) `Result<TResult, TError>` is the right tool for the job, otherwise just `Result` is needed.

An interesting detail is that `Result` can be used to avoid exceptions on `void` methods. So in case an exception is thrown in a given method, as an attempt to persist some data, it can be replaced by a `Result` instance and let the exception to be handled by the method itself preventing the stack returning.

At the same time `Result<TResult, TError>` can be used to return domain meaningful error types avoiding application exceptions.

Let's se some samples:

Sample 1 - Trying to persist an user in the database:

```c#
public Result PersistUser(User)
{
    try
    {
        //SQL...
        return true;
    }
    catch(Exception exc)
    {
        //Logs, metrics etc...
        return false;
    }
}
```

Sample 2 - Trying to create an `User` instance:

```c#
public interface IUserError {}
public struct AlreadyExistingEmailError : IUserError {}
public struct AleradyExistingIdentifierError : IUserError {}
...
public Result<User, IUserError> Create(Email email, Identifier identifier, Password password)
{
    //If Email is already in use
    return new AlreadyExistingEmailError();
    //If Identifier is already in use
    return new AlreadyExistingIdentifierError();
}
...
public IActionResult Create(CreateUserRequest request)
{
    var result = UserService.Create(email, cpf, password);
    if(result)
        return Created(string.Empty, result);

    var conflict = result switch
    {
        AlreadyExistingEmailError => "E-mail already in use.",
        AlreadyExistingIdentifierError => "Identifier already in use."
    }
    
    return Conflict(conflict);
}
```

You can in details the `Result` and `Result<TResult, TError>` capabilities bellow:

[Result]({{< relref "result" >}})\
[Result<TResult, TError>]({{< relref "resultTE" >}})