---
title: Installing Moonad
type: docs
weight: 2
---

The project's package can be found on [Nuget](https://nuget.org/packages/moonad) and installed by your IDE or shell as following:

```shell
dotnet add package Moonad
```

or

```shell
PM> Install-Package Moonad
```

#### A Note on Null Reference Types

Since our main goal is to protect the user from `NullReferenceException` we strongly recommend the use of [Nullable Reference Types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types) on any project which uses this lib.