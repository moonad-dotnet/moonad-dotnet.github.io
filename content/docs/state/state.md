---
title: State<T,S>
weight: 1
type: docs
---

The `State<T, T>` monad represents a stateful computation that takes an initial state of type `S`, performs some computation, and returns a value of type `T` along with a new state of type `S`.

#### Operators

##### Implicit State<T, S>(S)

Implicitly converts a value to a State that returns that value without changing the state.

```csharp
// Implicit conversion
State<int, string> state = "hello";
var (value, newState) = state.Run(42);
// value: "hello"
// newState: 42 (unchanged)

// Also works with value types
State<string, int> numState = 100;
var (num, strState) = numState.Run("initial");
// num: 100
// strState: "initial"
```

#### Properties

##### Function

```csharp
public Func<S, (T Value, S State)> Function { get; private set; }
```

The underlying function that performs the stateful computation. Takes an initial state and returns a tuple containing:
- `Value` - The computed result of type `T`
- `State` - The new state of type `S`

```csharp
var state = new State<int, string>(s => ($"value:{s}", s + 1));
var result = state.Function(10);
// result.Value: "value:10"
// result.State: 11
```

#### Methods

##### Eval

```csharp
public T Eval(S initial)
```

Executes the computation and returns only the value, discarding the final state.

**Parameters:**
- `initial` - The initial state to pass to the computation

**Returns:** The computed value of type `T`

**Example:**

```csharp
var state = new State<int, int>(s => (s * 2, s + 1));
var value = state.Eval(5);
// value: 10
// Final state (6) is discarded
```

##### Exec

```csharp
public S Exec(S initial)
```

Executes the computation and returns only the final state, discarding the value.

**Parameters:**
- `initial` - The initial state to pass to the computation

**Returns:** The final state of type `S`

**Example:**

```csharp
var state = new State<int, int>(s => (s * 2, s + 1));
var finalState = state.Exec(5);
// finalState: 6
// Value (10) is discarded
```

##### Run

```csharp
public (T, S) Run(S initial)
```

Executes the stateful computation with the given initial state and returns both the value and the new state.

**Parameters:**
- `initial` - The initial state to pass to the computation

**Returns:** A tuple containing the computed value and the new state

**Example:**

```csharp
var state = new State<int, int>(s => (s * 2, s + 1));
var (value, newState) = state.Run(5);
// value: 10 (5 * 2)
// newState: 6 (5 + 1)
```

#### Manipulation Methods

##### Get

```csharp
public State<S, S> Get()
```

Returns a State computation that produces the current state as its value without modifying the state.

**Returns:** A State that returns the current state as both value and state

**Example:**

```csharp
var getState = new State<int, int>(s => (s, s)).Get();
var (value, newState) = getState.Run(10);
// value: 10
// newState: 10
```

##### Gets

```csharp
public State<S, T> Gets(Func<S, T> f)
```

Returns a State that applies a function to the current state to produce a value, without modifying the state.

**Parameters:**
- `f` - A function that extracts or transforms the state into a value

**Returns:** A State that produces the transformed value

**Example:**

```csharp
var state = new State<int, int>(s => (s, s)).Gets(s => s * 2);
var (value, newState) = state.Run(5);
// value: 10 (5 * 2)
// newState: 5 (unchanged)

// Extracting a property from state
record GameState(int Score, int Lives);
var getScore = new State<GameState, int>(s => (0, s))
    .Gets(s => s.Score);
```

##### Modify

```csharp
public State<S, Unit> Modify(Func<S, S> f)
```

Returns a State that modifies the current state using the provided function, returning `Unit.Value`.

**Parameters:**
- `f` - A function that transforms the state

**Returns:** A State that produces `Unit.Value` and the modified state

**Example:**

```csharp
var modifyState = new State<int, int>(s => (s, s)).Modify(s => s + 5);
var (value, newState) = modifyState.Run(10);
// value: Unit.Value
// newState: 15 (10 + 5)

// Modifying complex state
record Counter(int Count);
var increment = new State<Counter, Unit>(s => (Unit.Value, s))
    .Modify(c => c with { Count = c.Count + 1 });
```

##### Put

```csharp
public State<S, Unit> Put(S newState)
```

Returns a State that replaces the current state with a new state, returning `Unit.Value`.

**Parameters:**
- `newState` - The new state to set

**Returns:** A State that produces `Unit.Value` and sets the new state

**Example:**

```csharp
var setState = new State<int, int>(s => (s, s)).Put(99);
var (value, newState) = setState.Run(10);
// value: Unit.Value
// newState: 99 (replaced from 10)
```

#### Transformation Methods

##### Lift

```csharp
public State<S, U> Lift<U>(Func<T, U> f)
```

Lifts a pure function into the State context. Equivalent to `Map(f, this)`.

**Parameters:**
- `f` - A pure function to lift

**Returns:** A new State with the lifted function applied

**Example:**

```csharp
var state = new State<int, int>(s => (s + 2, s + 3));
var lifted = state.Lift(x => x * 2);
var (value, newState) = lifted.Run(1);
// value: 6 ((1 + 2) * 2)
// newState: 4 (1 + 3)

// Lifting string operations
var strState = new State<int, string>(s => ($"Count: {s}", s + 1));
var upper = strState.Lift(s => s.ToUpper());
var (result, _) = upper.Run(5);
// result: "COUNT: 5"
```

##### Lift2

```csharp
public State<S, V> Lift2<U, V>(Func<T, U, V> f, State<S, T> x, State<S, U> y)
```

Lifts a binary function into the State context. Equivalent to `Map2(f, x, y)`.

**Parameters:**
- `f` - A binary function to lift
- `x` - The first State computation
- `y` - The second State computation

**Returns:** A State with the lifted function applied to both values

**Example:**

```csharp
var x = new State<int, int>(s => (s + 1, s + 2));
var y = new State<int, int>(s => (s * 2, s + 3));
var result = x.Lift2((a, b) => a * b, x, y);
var (value, newState) = result.Run(3);
// x: value=4, state=5
// y: value=10 (5*2), state=8 (5+3)
// final value: 40 (4*10), state: 8
```

##### Map

```csharp
public State<S, U> Map<U>(Func<T, U> f, State<S, T> state)
```

Transforms the value produced by a State computation while preserving the state threading.

**Parameters:**
- `f` - A function to transform the value
- `state` - The State computation to transform

**Returns:** A new State with the transformed value

**Example:**

```csharp
var state = new State<int, int>(s => (s + 1, s + 2));
var mapped = state.Map(x => x * 2, state);
var (value, newState) = mapped.Run(3);
// value: 8 ((3 + 1) * 2)
// newState: 5 (3 + 2)
```

##### Map2

```csharp
public State<S, V> Map2<U, V>(Func<T, U, V> f, State<S, T> x, State<S, U> y)
```

Combines two State computations using a binary function.

**Parameters:**
- `f` - A function that combines two values
- `x` - The first State computation
- `y` - The second State computation

**Returns:** A State that produces the combined value

**Example:**

```csharp
var x = new State<int, int>(s => (s + 1, s + 2));
var y = new State<int, int>(s => (s * 2, s + 3));
var result = x.Map2((a, b) => a + b, x, y);
var (value, newState) = result.Run(2);
// x: value=3, state=4
// y: value=8 (4*2), state=7 (4+3)
// final value: 11 (3+8), state: 7
```

##### Map3

```csharp
public State<S, W> Map3<U, V, W>(Func<T, U, V, W> f, State<S, T> x, State<S, U> y, State<S, V> z)
```

Combines three State computations using a ternary function.

**Parameters:**
- `f` - A function that combines three values
- `x` - The first State computation
- `y` - The second State computation
- `z` - The third State computation

**Returns:** A State that produces the combined value

**Example:**

```csharp
var x = new State<int, int>(s => (s + 1, s + 2));
var y = new State<int, int>(s => (s * 2, s + 3));
var z = new State<int, int>(s => (s - 1, s + 4));
var result = x.Map3((a, b, c) => a + b + c, x, y, z);
var (value, newState) = result.Run(2);
// x: value=3, state=4
// y: value=8, state=7
// z: value=6, state=11
// final value: 17 (3+8+6), state: 11
```

#### Monadic Operations

##### Apply

```csharp
public State<S, U> Apply<U>(State<S, Func<T, U>> sf)
```

Applies a function wrapped in a State to a value wrapped in a State, threading the state through both computations. This is the applicative functor operation.

**Parameters:**
- `sf` - A State containing a function

**Returns:** A State with the function applied to the value

**Example:**

```csharp
var sf = new State<int, Func<int, int>>(s => (x => x + s, s + 1));
var xv = new State<int, int>(s => (s * 2, s + 1));
var result = xv.Apply(sf);
var (value, newState) = result.Run(3);
// sf: func=(x => x+3), state=4
// xv: value=8 (4*2), state=5 (4+1)
// apply: value=11 (8+3), state=5
```

##### Bind

```csharp
public State<S, U> Bind<U>(Func<T, State<S, U>> f, State<S, T> state)
```

Chains stateful computations, threading the state through each computation. This is the fundamental monadic operation for State.

**Parameters:**
- `f` - A function that takes a value and returns a new State computation
- `state` - The initial State computation

**Returns:** A State that sequences both computations

**Example:**

```csharp
var state = new State<int, int>(s => (s + 1, s + 2));
State<int, int> next(int v) => new State<int, int>(s => (v * 3, s + v));
var bound = state.Bind(next, state);
var (value, newState) = bound.Run(1);
// state: value=2, state=3
// next: value=6 (2*3), state=5 (3+2)
```

##### Kleisli

```csharp
public Func<T, State<S, V>> Kleisli<U, V>(
    Func<T, State<S, U>> f, 
    Func<U, State<S, V>> g)
```

Composes two State-producing functions using Kleisli composition. This creates a pipeline of stateful computations.

**Parameters:**
- `f` - The first State-producing function
- `g` - The second State-producing function

**Returns:** A composed function that applies both transformations

**Example:**

```csharp
State<int, int> f(int t) => new State<int, int>(s => (t + s, s + 1));
State<int, string> g(int u) => new State<int, string>(s => ($"{u}-{s}", s + u));
var k = new State<int, int>(s => (s, s)).Kleisli(f, g);
var result = k(5);
var (value, newState) = result.Run(3);
// f(5): value=8 (5+3), state=4 (3+1)
// g(8): value="8-4", state=12 (4+8)
```

#### Sequencing Methods

##### SequenceLeft

```csharp
public State<S, T> SequenceLeft<U>(State<S, T> x, State<S, U> y)
```

Executes two State computations in sequence, returning the result of the first computation with the final state from the second. Often represented as `<*` in functional languages.

**Parameters:**
- `x` - The first State computation (result returned)
- `y` - The second State computation (result discarded)

**Returns:** A State that produces the first computation's value with the second's state

**Example:**

```csharp
var x = new State<int, string>(s => ($"x{s}", s + 1));
var y = new State<int, int>(s => (s * 3, s + 2));
var result = x.SequenceLeft(x, y);
var (value, newState) = result.Run(2);
// x: value="x2", state=3
// y: value=9 (discarded), state=5 (3+2)
// final: value="x2", state=5
```

##### SequenceRight

```csharp
public State<S, U> SequenceRight<U>(State<S, T> x, State<S, U> y)
```

Executes two State computations in sequence, returning the result of the second computation. Often represented as `*>` in functional languages.

**Parameters:**
- `x` - The first State computation (result discarded)
- `y` - The second State computation (result returned)

**Returns:** A State that produces the second computation's value

**Example:**

```csharp
var x = new State<int, int>(s => (s, s + 1));
var y = new State<int, int>(s => (s * 3, s + 2));
var result = y.SequenceRight(x, y);
var (value, newState) = result.Run(2);
// x: value=2 (discarded), state=3
// y: value=9 (3*3), state=5 (3+2)
```

##### Zip

```csharp
public State<S, (T, U)> Zip<U>(State<S, T> x, State<S, U> y)
```

Runs two State computations in sequence and returns their results as a tuple.

**Parameters:**
- `x` - The first State computation
- `y` - The second State computation

**Returns:** A State that produces a tuple of both values

**Example:**

```csharp
var x = new State<int, string>(s => ($"x{s}", s + 1));
var y = new State<int, int>(s => (s * 2, s + 3));
var result = x.Zip(x, y);
var ((a, b), newState) = result.Run(2);
// x: a="x2", state=3
// y: b=6 (3*2), state=6 (3+3)
// final: (a,b)=("x2",6), state=6
```


#### Control Flow Methods

##### Delay

```csharp
public State<S, T> Delay(Func<State<S, T>> body)
```

Defers the execution of a State computation until it is run. Useful for lazy evaluation or recursive computations.

**Parameters:**
- `body` - A function that produces the State computation

**Returns:** A State that will execute the body when run

**Example:**

```csharp
var executed = false;
State<int, int> body() => new State<int, int>(s => {
    executed = true;
    return (s + 10, s + 1);
});
var delayed = new State<int, int>(s => (s, s)).Delay(body);
// executed is still false here
var (value, newState) = delayed.Run(0);
// now executed is true
// value: 10, newState: 1
```

##### Throw

```csharp
public State<S, T> Throw(Exception ex)
```

Returns a State that throws the specified exception when executed.

**Parameters:**
- `ex` - The exception to throw

**Returns:** A State that throws the exception

**Example:**

```csharp
var ex = new InvalidOperationException("Something went wrong");
var state = new State<int, int>(s => (s, s)).Throw(ex);
// Throws InvalidOperationException when Run is called
try {
    state.Run(0);
} catch (InvalidOperationException e) {
    Console.WriteLine(e.Message); // "Something went wrong"
}
```

##### TryFinally

```csharp
public State<S, T> TryFinally(State<S, T> body, Action compensation)
```

Executes a State computation and ensures the compensation action runs afterward, similar to a `finally` block.

**Parameters:**
- `body` - The State computation to execute
- `compensation` - An action to run after the computation (success or failure)

**Returns:** A State that ensures cleanup

**Example:**

```csharp
var called = false;
var body = new State<int, int>(s => (s + 1, s + 2));
var wrapped = body.TryFinally(body, () => called = true);
var (value, newState) = wrapped.Run(1);
// value: 2, newState: 3
// called is now true
```

##### TryWith

```csharp
public State<S, T> TryWith(State<S, T> body, Func<Exception, State<S, T>> handler)
```

Executes a State computation and handles any exceptions with the provided handler function.

**Parameters:**
- `body` - The State computation to execute
- `handler` - A function that handles exceptions and returns a recovery State

**Returns:** A State that handles exceptions

**Example:**

```csharp
var exBody = new State<int, int>(_ => throw new InvalidOperationException("Error"));
var handled = exBody.TryWith(
    exBody, 
    ex => new State<int, int>(s => (999, s + 1))
);
var (value, newState) = handled.Run(0);
// value: 999 (from handler)
// newState: 1
```

##### Using

```csharp
public State<S, T> Using<R>(R resource, Func<R, State<S, T>> body) 
    where R : IDisposable
```

Executes a State computation with a disposable resource and ensures the resource is disposed afterward.

**Parameters:**
- `resource` - The disposable resource to use
- `body` - A function that uses the resource to produce a State

**Returns:** A State that ensures resource disposal

**Example:**

```csharp
class FileResource : IDisposable {
    public bool Disposed { get; private set; }
    public void Dispose() => Disposed = true;
}

var resource = new FileResource();
var state = new State<int, int>(s => (s + 1, s + 2));
var wrapped = state.Using(resource, _ => state);
var (value, newState) = wrapped.Run(3);
// value: 4, newState: 5
// resource.Disposed is now true
```

#### LINQ Support

The State monad supports LINQ query syntax through extension methods, enabling declarative and readable stateful computations.

##### Select

```csharp
public static State<S, U> Select<S, T, U>(
    this State<S, T> state, 
    Func<T, U> selector)
```

Projects the value of a State computation. Enables `select` in LINQ queries.

**Parameters:**
- `state` - The State to transform
- `selector` - A function to transform the value

**Returns:** A State with the transformed value

**Example:**

```csharp
var state = new State<int, int>(s => (s + 1, s + 2));
var projected = state.Select(x => x * 3);
var (value, newState) = projected.Run(5);
// value: 18 ((5+1)*3)
// newState: 7 (5+2)

// Using LINQ syntax
var query = from x in state
            select x * 3;
```

##### SelectMany

```csharp
public static State<S, U> SelectMany<S, T, U>(
    this State<S, T> state, 
    Func<T, State<S, U>> binder)
```

Binds State computations. Enables `from ... from` in LINQ queries.

**Parameters:**
- `state` - The initial State
- `binder` - A function that produces the next State

**Returns:** A State that sequences both computations

**Example:**

```csharp
var state = new State<int, int>(s => (s + 2, s + 3));
State<int, string> binder(int x) => new(s => ($"{x}-{s}", s + x));
var bound = state.SelectMany(binder);
var (value, newState) = bound.Run(4);
// value: "6-7", newState: 13

// Using LINQ syntax
var query = from x in state
            from y in binder(x)
            select y;
```

##### SelectMany (with projection)

```csharp
public static State<S, V> SelectMany<S, T, U, V>(
    this State<S, T> state, 
    Func<T, State<S, U>> binder, 
    Func<T, U, V> projector)
```

Binds State computations with a final projection. Required for full LINQ query syntax support with multiple `from` clauses.

**Parameters:**
- `state` - The initial State
- `binder` - A function that produces the next State
- `projector` - A function that combines both values

**Returns:** A State with the projected result

**Example:**

```csharp
var state = new State<int, int>(s => (s + 1, s + 2));

var query = from x in state
            from y in new State<int, int>(s => (s * 2, s + 1))
            select x + y;

var (value, newState) = query.Run(3);
// x: 4, state: 5
// y: 10, state: 6
// value: 14 (4+10), newState: 6
```

##### Where

```csharp
public static State<S, T> Where<S, T>(
    this State<S, T> state, 
    Func<T, bool> predicate)
```

Filters the value of a State computation. Enables `where` in LINQ queries. Throws an `InvalidOperationException` if the predicate fails.

**Parameters:**
- `state` - The State to filter
- `predicate` - A function that tests the value

**Returns:** A State that validates the predicate

**Example:**

```csharp
var state = new State<int, int>(s => (s * 2, s + 1));
var filtered = state.Where(v => v % 2 == 0);
var (value, newState) = filtered.Run(3);
// value: 6, newState: 4

// This will throw
var failing = state.Where(v => v % 2 != 0);
// Throws InvalidOperationException when run with even input

// Using LINQ syntax
var query = from x in state
            where x > 10
            select x;
```

### Complete Examples

##### Example 1: Simple Counter

```csharp
using Moonad;

// Define counter operations
State<int, Unit> Increment() => 
    new State<int, Unit>(count => (Unit.Value, count + 1));

State<int, Unit> Decrement() => 
    new State<int, Unit>(count => (Unit.Value, count - 1));

State<int, int> GetCount() => 
    new State<int, int>(count => (count, count));

// Compose operations
var computation =
    from _ in Increment()
    from __ in Increment()
    from ___ in Increment()
    from count in GetCount()
    select count;

var (finalCount, _) = computation.Run(0);
Console.WriteLine(finalCount); // Output: 3
```

##### Example 2: Stack Operations

```csharp
using Moonad;
using System.Collections.Generic;

State<Stack<int>, Unit> Push(int value) =>
    new State<Stack<int>, Unit>(stack => {
        var newStack = new Stack<int>(stack);
        newStack.Push(value);
        return (Unit.Value, newStack);
    });

State<Stack<int>, int> Pop() =>
    new State<Stack<int>, int>(stack => {
        var newStack = new Stack<int>(stack);
        var value = newStack.Pop();
        return (value, newStack);
    });

State<Stack<int>, int> Peek() =>
    new State<Stack<int>, int>(stack => (stack.Peek(), stack));

// Use the stack
var computation =
    from _ in Push(10)
    from __ in Push(20)
    from ___ in Push(30)
    from top in Pop()
    from second in Peek()
    select (top, second);

var ((topValue, secondValue), finalStack) = computation.Run(new Stack<int>());
Console.WriteLine($"Popped: {topValue}, Peek: {secondValue}");
// Output: Popped: 30, Peek: 20
```

##### Example 3: Game State Management

```csharp
using Moonad;

record GameState(int Score, int Lives, int Level);

State<GameState, Unit> AddScore(int points) =>
    new State<GameState, Unit>(s => 
        (Unit.Value, s with { Score = s.Score + points }));

State<GameState, Unit> LoseLife() =>
    new State<GameState, Unit>(s => 
        (Unit.Value, s with { Lives = s.Lives - 1 }));

State<GameState, Unit> NextLevel() =>
    new State<GameState, Unit>(s => 
        (Unit.Value, s with { Level = s.Level + 1 }));

State<GameState, bool> IsGameOver() =>
    new State<GameState, bool>(s => (s.Lives <= 0, s));

State<GameState, GameState> GetState() =>
    new State<GameState, GameState>(s => (s, s));

// Simulate gameplay
var gameplay =
    from _ in AddScore(100)
    from __ in AddScore(50)
    from ___ in LoseLife()
    from ____ in NextLevel()
    from _____ in AddScore(200)
    from gameOver in IsGameOver()
    from finalState in GetState()
    select (gameOver, finalState);

var initialState = new GameState(0, 3, 1);
var ((isOver, final), _) = gameplay.Run(initialState);

Console.WriteLine($"Game Over: {isOver}");
Console.WriteLine($"Score: {final.Score}, Lives: {final.Lives}, Level: {final.Level}");
// Output:
// Game Over: False
// Score: 350, Lives: 2, Level: 2
```

##### Example 4: Random Number Generation

```csharp
using Moonad;
using System;

State<Random, int> NextInt(int max) =>
    new State<Random, int>(rng => (rng.Next(max), rng));

State<Random, double> NextDouble() =>
    new State<Random, double>(rng => (rng.NextDouble(), rng));

State<Random, T> Choose<T>(params T[] options) =>
    from index in NextInt(options.Length)
    select options[index];

// Generate random game data
var randomGameData =
    from damage in NextInt(100)
    from critChance in NextDouble()
    from weapon in Choose("Sword", "Axe", "Bow")
    select new { Damage = damage, CritChance = critChance, Weapon = weapon };

var (result, _) = randomGameData.Run(new Random(42));
Console.WriteLine($"Weapon: {result.Weapon}, Damage: {result.Damage}, Crit: {result.CritChance:P}");
```

##### Example 5: Configuration Builder

```csharp
using Moonad;
using System.Collections.Generic;

record Config(Dictionary<string, string> Settings);

State<Config, Unit> SetSetting(string key, string value) =>
    new State<Config, Unit>(config => {
        var newSettings = new Dictionary<string, string>(config.Settings) {
            [key] = value
        };
        return (Unit.Value, config with { Settings = newSettings });
    });

State<Config, string> GetSetting(string key) =>
    new State<Config, string>(config => 
        (config.Settings.TryGetValue(key, out var value) ? value : "", config));

State<Config, Config> GetConfig() =>
    new State<Config, Config>(config => (config, config));

// Build configuration
var buildConfig =
    from _ in SetSetting("Host", "localhost")
    from __ in SetSetting("Port", "8080")
    from ___ in SetSetting("Protocol", "https")
    from host in GetSetting("Host")
    from port in GetSetting("Port")
    from config in GetConfig()
    select (host, port, config);

var initialConfig = new Config(new Dictionary<string, string>());
var ((h, p, finalConfig), _) = buildConfig.Run(initialConfig);

Console.WriteLine($"Server: {h}:{p}");
Console.WriteLine($"Total settings: {finalConfig.Settings.Count}");
// Output:
// Server: localhost:8080
// Total settings: 3
```

##### Example 6: Parser State

```csharp
using Moonad;

record ParserState(string Input, int Position);

State<ParserState, char> ReadChar() =>
    new State<ParserState, char>(state => {
        if (state.Position >= state.Input.Length)
            throw new InvalidOperationException("End of input");
        
        var ch = state.Input[state.Position];
        var newState = state with { Position = state.Position + 1 };
        return (ch, newState);
    });

State<ParserState, string> ReadWhile(Func<char, bool> predicate) =>
    new State<ParserState, string>(state => {
        int start = state.Position;
        int pos = start;
        
        while (pos < state.Input.Length && predicate(state.Input[pos]))
            pos++;
        
        var result = state.Input.Substring(start, pos - start);
        var newState = state with { Position = pos };
        return (result, newState);
    });

State<ParserState, int> GetPosition() =>
    new State<ParserState, int>(state => (state.Position, state));

// Parse a simple expression
var parser =
    from word1 in ReadWhile(char.IsLetter)
    from _ in ReadWhile(char.IsWhiteSpace)
    from word2 in ReadWhile(char.IsLetter)
    from pos in GetPosition()
    select (word1, word2, pos);

var initialState = new ParserState("Hello World", 0);
var ((w1, w2, position), finalState) = parser.Run(initialState);

Console.WriteLine($"Parsed: '{w1}' and '{w2}' at position {position}");
// Output: Parsed: 'Hello' and 'World' at position 11
```

##### Example 7: Transaction Log

```csharp
using Moonad;
using System.Collections.Generic;

record Transaction(string Type, decimal Amount, DateTime Timestamp);
record Account(decimal Balance, List<Transaction> History);

State<Account, Unit> Deposit(decimal amount) =>
    new State<Account, Unit>(account => {
        var transaction = new Transaction("Deposit", amount, DateTime.Now);
        var newHistory = new List<Transaction>(account.History) { transaction };
        return (Unit.Value, account with { 
            Balance = account.Balance + amount,
            History = newHistory
        });
    });

State<Account, Unit> Withdraw(decimal amount) =>
    new State<Account, Unit>(account => {
        if (account.Balance < amount)
            throw new InvalidOperationException("Insufficient funds");
        
        var transaction = new Transaction("Withdrawal", amount, DateTime.Now);
        var newHistory = new List<Transaction>(account.History) { transaction };
        return (Unit.Value, account with { 
            Balance = account.Balance - amount,
            History = newHistory
        });
    });

State<Account, decimal> GetBalance() =>
    new State<Account, decimal>(account => (account.Balance, account));

State<Account, int> GetTransactionCount() =>
    new State<Account, int>(account => (account.History.Count, account));

// Perform transactions
var transactions =
    from _ in Deposit(1000m)
    from __ in Withdraw(200m)
    from ___ in Deposit(500m)
    from ____ in Withdraw(150m)
    from balance in GetBalance()
    from count in GetTransactionCount()
    select (balance, count);

var initialAccount = new Account(0m, new List<Transaction>());
var ((finalBalance, txCount), finalAccount) = transactions.Run(initialAccount);

Console.WriteLine($"Final Balance: ${finalBalance}");
Console.WriteLine($"Transactions: {txCount}");
// Output:
// Final Balance: $1150
// Transactions: 4
```

###### Example 8: LINQ Query

```csharp
var baseState = new State<int, int>(s => (s + 1, s + 2));

var query =
    from a in baseState
    from b in new State<int, int>(s => (s * 2, s + 3))
    where a % 2 == 0
    select $"{a}-{b}";

var (value, newState) = query.Run(3);
// baseState: a=4, state=5
// second: b=10 (5*2), state=8 (5+3)
// where: passes (4 % 2 == 0)
// select: value="4-10", state=8
```

#### Best Practices

##### 1. Keep State Pure and Immutable

Always use immutable state types and pure transformation functions:

```csharp
// Good: Immutable record
record GameState(int Score, int Level);

State<GameState, Unit> AddScore(int points) =>
    new State<GameState, Unit>(s => 
        (Unit.Value, s with { Score = s.Score + points }));

// Avoid: Mutable state
class MutableState {
    public int Score { get; set; }
}
```

##### 2. Use LINQ Query Syntax for Readability

LINQ syntax makes complex state computations more readable:

```csharp
// Good: Clear and declarative
var computation =
    from _ in Increment()
    from count in GetCount()
    where count > 0
    select count;

// Less readable: Nested Bind calls
var computation2 = Increment().Bind(
    _ => GetCount().Bind(
        count => ...));
```

##### 3. Compose Small Operations

Build complex operations from small, reusable pieces:

```csharp
// Small, focused operations
State<int, Unit> Increment() => /* ... */;
State<int, Unit> Double() => /* ... */;
State<int, int> GetValue() => /* ... */;

// Compose them
var complex =
    from _ in Increment()
    from __ in Double()
    from value in GetValue()
    select value;
```

##### 4. Handle Errors Gracefully

Use `TryWith` for error handling in stateful computations:

```csharp
State<Account, decimal> SafeWithdraw(decimal amount) =>
    new State<Account, Unit>(account => /* may throw */)
        .TryWith(
            Withdraw(amount),
            ex => new State<Account, decimal>(s => (s.Balance, s))
        );
```

##### 5. Minimize State Size

Keep state types focused and minimal:

```csharp
// Good: Focused state
record Counter(int Value);

// Avoid: Kitchen sink state
record EverythingState(
    int Counter, 
    string Name, 
    List<object> Items, 
    Dictionary<string, object> Cache,
    // ... too much
);
```

##### 6. Use Gets for Read-Only Access

Use `Gets` when you only need to read from state without modification:

```csharp
// Read state property
State<GameState, int> GetScore() =>
    new State<GameState, int>(s => (0, s)).Gets(s => s.Score);

// Better than
State<GameState, int> GetScoreBad() =>
    new State<GameState, int>(s => (s.Score, s));
```

##### 7. Leverage Type Safety

Use specific types instead of primitives:

```csharp
// Good: Type-safe
record PlayerId(int Value);
record GameState(PlayerId CurrentPlayer);

// Avoid: Primitive obsession
record GameState(int CurrentPlayer);
```

##### 8. Document State Transitions

Make state transitions explicit and well-documented:

```csharp
/// <summary>
/// Transitions from Playing to GameOver if lives reach 0
/// </summary>
State<GameState, Unit> CheckGameOver() =>
    from lives in GetLives()
    from _ in lives <= 0 
        ? SetGameStatus(GameStatus.Over) 
        : GetState().Lift(_ => Unit.Value)
    select Unit.Value;
```
