---
title: Choice<T1, T2, T3, T4>
weight: 3
type: docs
prev: /docs/choice/choiceT3
next: docs/choice/choiceT5
---

#### Operators

##### Implicit T1
Allows the client to get the value of T1 when it was chosen.

```c#
Choice<Circle, Square, Triangle, Rectangle> choice = new Circle(10);
Console.WriteLine(choice);
//This is a Circle with radius 10
```

##### Implicit T2
Allows the client to get the value of T2 when it was chosen.

```c#
Choice<Circle, Square, Triangle, Rectangle> choice = new Square(20);
Console.WriteLine(choice);
//This is a Square with side 20
```

##### Implicit T3
Allows the client to get the value of T3 when it was chosen.

```c#
Choice<Circle, Square, Triangle, Rectangle> choice = new Triangle(10, 10, 10);
Console.WriteLine(choice);
//This is a Triangle with sides 10, 10, 10
```

##### Implicit T4
Allows the client to get the value of T4 when it was chosen.

```c#
Choice<Circle, Square, Triangle, Rectangle> choice = new Rectangle(10, 20);
Console.WriteLine(choice);
//This is a Rectangle with sides 10, 20
```