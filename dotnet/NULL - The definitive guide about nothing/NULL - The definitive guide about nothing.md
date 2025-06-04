
# NULL: The definitive guide about nothing

## Author
Tamás Sinku (sinkutamas@gmail.com)

## Things we already know

 In C#, we have
- Value-Types, allocated on "stack" and always have a defined value.
- Reference-Types are allocated on "heap". A reference is allocated on the stack that may or may not refer to an object on the heap.

`null`
In other programming languages: `nil` (eg. Delphi, Lua), `Nothing` (eg. Visual Basic), `None` (eg. Python). The C# keyword is `null`. `null` is the default value of any Reference-Type. It represents a state in which there’s no allocated object on the heap to be referenced.

Meaning of `null`:
- A property which is not applicable within a given context
- An unknown value:
  - An existing information which is not known
  - It is not known whether the information exists or not

Dereferencing: Accessing the allocated object on the heap. When a NULL-reference is tried to be dereferenced, a `NullReferenceException` is thrown at runtime.

NULL-reference can be checked as simply as:
```csharp
//Students and its items are not null, but SingleOrDefault
//may return null if no matching element found
Student s = Students.SingleOrDefault(x => x.Id == 123);

// Is this good enough?
if (s == null)
  return new FindStudentResult(FindStudentResult.Errors.StudentNotFound);

string studentName = s.Name;
```

## Checking for NULL-values

### `== null` is unsafe

Operators, including `==` and `!=`, can be overloaded, making it unsafe for NULL-checks:
```csharp
class Student {
  public static bool operator ==(Student a, Student b) => false;
  public static bool operator !=(Student a, Student b) => true;
}

Student s = Students.FirstOrDefault(x => x.Id == 123); //returns null
if (s == null) //condition is not met
  return new FindStudentResult(FindStudentResult.Errors.StudentNotFound);

string studentName = s.Name; //Unhandled NullReferenceException
```

### Also doesn't work: `Object.Equals`

```csharp
Student s = Students.FirstOrDefault(x => x.Id == 123);
if (s.Equals(null))
  return new FindStudentResult(FindStudentResult.Errors.StudentNotFound);
```

`Equals` requires dereferencing, therefore `NullReferenceException` is thrown. Also, `Equals` can be overridden, too.


### `Object.ReferenceEquals`

Usage example:
```csharp
Student s = Students.FirstOrDefault(x => x.Id == 123);
if (Object.ReferenceEquals(s, null))
  return new FindStudentResult(FindStudentResult.Errors.StudentNotFound);
```

`Object.ReferenceEquals` also uses the equality operator. Yet it somehow works?! Notice the implicit cast from `Student` to `System.Object` in the implementation below. Because of this, the equality operator of System.Object is being invoked instead of the overrides.

```csharp
public static bool ReferenceEquals(object objA, object objB)
  => objA == objB;
```


### The proper way of null-checking

So we can explicitly cast expressions to `System.Object`:

```csharp
Student s = Students.FirstOrDefault(x => x.Id == 123);
if ((object)s == null) {
  return new FindStudentResult(FindStudentResult.Errors.StudentNotFound);
}
```
The good way to check NULL-values up until C# 7.0


### New language features for a better null-safe code

#### Pattern matching
```csharp
Student s = Students.FirstOrDefault(x => x.Id == 123);
if (s is null) {
  return new FindStudentResult(FindStudentResult.Errors.StudentNotFound);
}
```

Lowered as:
```csharp
if ((object)s == null)
```

Checking for not null in C# 7.0, 7.1, 7.2, 7.3, 8:
```csharp
if (!(s is null))
if (s is object)
if (s is {})
```

Negated patterns are introduced in C# 9:
```csharp
if (s is not null)
```

#### Null-safe member checking and accessing
Before C# 6:
```csharp
var result = (object)a != null && (object)a.b != null ? a.b.c : null
```

C# 6 introduced **short-circuiting null-conditional member accessor** and indexer:
```csharp
var result = a?.b?.c
PropertyChanged?.Invoke(…);
var value = someDictionary?["key"]
```

Pattern matching can also be used for null-checking properties:
```csharp
if (student is { Gradebook: not null })
//equivalent to: if (student?.Gradebook is not null)
```

C# 7.0 introduced **throw expressions**:
```csharp
var a = (object)b == null ? throw new ArgumentNullException(nameof(b)) : b;
```

**Null-coalescing operators** are introduced in C# 8. `??` operator is a syntax sugar for the following code:
```csharp
a ?? b
//is equivalent to:
(object)a != null ? a : b
```

`??` operator with throw expressions:
```csharp
a ?? throw new ArgumentNullException(nameof(a));
```

`??=` is the **null-coalescing assignment operator**:
```csharp
b ??= a
//is equivalent to:
b = (object)b == null ? a : b
```

Upcoming in C# 14: **Null-conditional assignment**

```csharp
if (something is not null) something.SomeProperty = "something";
if (list is not null) list[index] = "something";
```
Equivalent code in C# 14:
```csharp
something?.SomeProperty = "something";
list?[index] = "something";
```
Null-conditional assignment:
- Can be used with compound assignment operators (`+=`, `-=`, …)
- Cannot be used with increment (`++`) and decrement (`--`) operators
- The right hand side expression is evaluated only if the left hand side is not `null`


## NULL-value related exceptions

### `System.NullReferenceException`
Thrown during an attempt to dereference a `null` value by accessing a member of a non-existing object.

The following methods of `Nullable<T>` does not throw `NullReferenceException`:
- `bool Equals(object)`
- `int GetHashCode()`
- `T GetValueOrDefault()`
- `T GetValueOrDefault(T)`
- `string ToString()`

### `System.ArgumentNullException`
Thrown when a method that does not accept `null` as an argument receives `null`.

```csharp
void Print(string message) {
  if (message is null) throw new ArgumentNullException(nameof(message));
  //.NET 7 or newer:
  ArgumentNullException.ThrowIfNull(message);
  
  Console.WriteLine(message);
}
```

There was a C# language proposal **Parameter NULL checking** to simplify throwing this exception which was rejected by the community:
```csharp
void Print(string message!!) { //bangbang operator checks for NULL and throws ArgumentNullException
  Console.WriteLine(message);
}
```

### `System.InvalidOperationException`
Although not exclusively related to `null`, this exception can be thrown when an operation is invalid due to the current state of the object, which may include a situation where the object or some part of it is `null`.

This exception is thrown in case of accessing `Nullable<T>.Value` when `HasValue` is `false`:
```csharp
public struct MyStruct {
  public string MyProp { get; set; }
} 

MyStruct? s = null;
Console.WriteLine(s.Value.MyProp);
```

### `System.Reflection.TargetInvocationException`
Thrown when an exception is thrown by a method invoked through reflection, including `NullReferenceException`. Check `InnerException` for details.
```csharp
public class SomeClass {
  public static void SomeMethod() {
    object o = null;
    string s = o.ToString(); //throws NullReferenceException 
  }
}

MethodInfo method = typeof(SomeClass).GetMethod("SomeMethod");
method.Invoke(null, null); //throws TargetInvocationException
```

### `Microsoft.CSharp.RuntimeBinder.RuntimeBinderException`
Can be expected when using `dynamic`. Thrown when the runtime binder fails to bind, including:
- Dereferencing a `null`-value
- Assigning a dynamic expression evaluating to `null` to a non-nullable type

```csharp
dynamic o = (string)null;
int a = o.Length; //throws RuntimeBinderException (dereferencing null-value)
int b = o?.Length; //throws RuntimeBinderException (assigning null-value to non-nullable type)
```


## Nullable value types

Introduced in C# 2.0. Based on struct `System.Nullable<T>`, enables value-types to have `null`-values.

Declaration syntax:
```csharp
Nullable<int> i = null;
int? i = null; //short syntax
```

`Nullable<T>` is actually never `null`, since it is also a value-type. This adds the following logic:
- `bool Nullable<T>.HasValue { get; }` property is `false` when `null`-value is assigned
- `T Nullable<T>.Value { get; }` property gets the assigned value when `HasValue` is `true`. Otherwise it throws `InvalidOperationException`.

### Null-safe value access

Use `HasValue` directly:
```csharp
if (!enableErrorReporting.HasValue || enableErrorReporting.Value)
  EnableErrorReporting(); //Feature enabled by default, if not disabled explicitly
```

Use `GetValueOrDefault`:
```csharp
if (enableErrorReporting.GetValueOrDefault())
  EnableErrorReporting(); //Feature disabled by default, if not enabled explicitly
```

Unlike reference types, you can safely use `==` and `!=` operators without casting even if overloaded in the underlying struct:
```csharp
if (enableErrorReporting == null) //checking against null is lowered to checking HasValue
```
Pattern matching works the same way as with reference-types.


### Lifted operators

Any unary and binary operators that struct `T` supports are also supported by `Nullable<T>`.
- Called "Lifted operators".
- Will produce `null` if one or both operands are `null`:

```csharp
int? a = 10;
int? b = null;
int? result = a + b; //null
```

As for comparison operators (`<`, `>`, `<=` and `>=`), if one or both operands are `null`, the result is `false`.


### `Nullable<bool>`

`Nullable<bool>` has some different behavior with `&` and `|` operators:
- The `&` operator produces `true` only if both its operands evaluate to `true`. If either `x` or `y` evaluates to false, `x & y` produces `false` (even if another operand evaluates to `null`). Otherwise, the result of `x & y` is `null`.
- The `|` operator produces false only if both its operands evaluate to false. If either `x` or `y` evaluates to `true`, `x | y` produces `true` (even if another operand evaluates to `null`). Otherwise, the result of `x | y` is `null`.

Truth table:

| x     | y     | x&y   | x\|y  |
|-------|-------|-------|-------|
| true  | true  | true  | true  |
| true  | false | false | true  |
| true  | null  | null  | true  |
| false | true  | false | true  |
| false | false | false | false |
| false | null  | false | null  |
| null  | true  | null  | true  |
| null  | false | false | null  |
| null  | null  | null  | null  |

### Boxing and unboxing
- An instance of a nullable value type `T?` is boxed as follows:
- If `HasValue` returns `false`, the `null` reference is produced.
- If `HasValue` returns `true`, the corresponding value of the underlying value type `T` is boxed, not the `Nullable<T>` itself.


### Identifying nullable value types by its value

`Object.GetType` will not work because of boxing.
```csharp
int? a = 17;
Type typeOfA = a.GetType();
Console.WriteLine(typeOfA.FullName); //System.Int32
```

`is` operator also doesn’t work.
```csharp
int? a = 14;
if (a is int)
    Console.WriteLine("int? instance is compatible with int");

int b = 17;
if (b is int?)
    Console.WriteLine("int instance is compatible with int?");

// Output:
// int? instance is compatible with int
// int instance is compatible with int?
```

One possible way:
```csharp
static bool IsNullableValueType<T>(T obj)
    => default(T) == null
       && typeof(T).BaseType != null
       && "ValueType".Equals(typeof(T).BaseType.Name);
```

Examples:
```csharp
int? a = null;
int? b = 5;
int c = 5;
object d = 5;

Console.WriteLine(IsNullableValueType(a)); //True
Console.WriteLine(IsNullableValueType(b)); //True
Console.WriteLine(IsNullableValueType(c)); //False
Console.WriteLine(IsNullableValueType(d)); //False
```

## Nullability analysis of reference types

Goals:
- Reduce `null` value related runtime exceptions
- Produce compile-time warnings on improper `null` value handling
- Simplify user code by avoiding unnecessary `null` checks
- Improved API definitions on required data, allowing the developers to express design intent more clearly


## Nullable reference types

- Introduced in C# 8
- Optional language feature
- Allows developers to express when a reference type can or cannot be `null`
- Refers to a group of features enabled in a nullable aware context that minimize the likelihood that user code causes the runtime to throw `NullReferenceException`:
  - Improved static flow analysis that determines if a variable might be null before dereferencing it.
  - Attributes that annotate APIs so that the flow analysis determines null-state.
  - Variable annotations that developers use to explicitly declare the intended null-state for a variable.

Nullable context is a combination of two separate features:
- **Nullable annotation context**: Consists of language syntax extensions and new attributes that allow the developer to specify the nullability of reference types
- **Nullable warning context**: Consists of a set of compiler warnings that are raised whenever a non-null-safe operation is being performed

Every type has one of three nullabilities:
- **Oblivious**: All reference types are nullable oblivious when the annotation context is disabled.
- **Nonnullable**: An unannotated reference type, `C` is nonnullable when the annotation context is enabled.
- **Nullable**: An annotated reference type, `C?`, is nullable, but a warning may be issued when the annotation context is disabled. Variables declared with var are nullable when the annotation context is enabled.

### Configuring the nullable context

Enabling warnings and annotations control how the compiler views reference types and nullability.

Nullable context can be configured using 4 values:
- `disable`: disables both nullable annotation and warning contexts
- `enable`: enables both nullable annotation and warning contexts
- `warnings`: enables only nullable warning context
- `annotations`: enables only nullable annotation context

`disable`: The code is nullable-oblivious.
- Nullable warnings are disabled.
- All reference type variables are nullable reference types.
- Use of the `?` suffix to declare a nullable reference type produces a warning.
- You can use the null forgiving operator, `!`, but it has no effect.

`enable`: The compiler enables all null reference analysis and all language features.
- All new nullable warnings are enabled.
- You can use the `?` suffix to declare a nullable reference type.
- Reference type variables without the `?` suffix are non-nullable reference types.
- The null forgiving operator `!` suppresses warnings for a possible dereference of `null`.

`warnings`: The compiler performs all null analysis and emits warnings when code might dereference `null`.
- All new nullable warnings are enabled.
- Use of the `?` suffix to declare a nullable reference type produces a warning.
- All reference type variables are allowed to be `null`. However, members have the null-state of not-null at the opening brace of all methods unless declared with the `?` suffix.
- You can use the null forgiving operator, `!`.

`annotations`: The compiler doesn't emit warnings when code might dereference null, or when you assign a maybe-null expression to a non-nullable variable.
- All new nullable warnings are disabled.
- You can use the `?` suffix to declare a nullable reference type.
- Reference type variables without the `?` suffix are non-nullable reference types.
- You can use the null forgiving operator, `!`, but it has no effect.

Which one to choose?

- `disable`: for legacy projects that you don't want to update.
- `warnings`: to determine where your code might throw `NullReferenceException`. You can address those warnings before modifying code to enable non-nullable reference types.
- `annotations`: to express your design intent before enabling warnings.
- `enable`: for new projects and active projects where you want to have protection against null reference exceptions.


#### Project-level configuration in .csproj file

```xml
<Nullable>enable|disable|annotations|warnings</Nullable>
```

#### In-code pre-processor directives

Configuring both nullable annotation and warning contexts:
```csharp
#nullable enable|disable|restore
```

Configuring nullable annotation context:
```csharp
#nullable enable|disable|restore annotations
```

Configuring nullable warning context:
```csharp
#nullable enable|disable|restore warnings
```


## Nullable annotation context

Use `?` operator to declare variables, parameters, fields, properties whether they accept `null` or not.
Use `!` operator in cases when the compiler fails to infer a nullable expression being guaranteed to be not `null`.

Further annotate the code’s behavior using nullability attributes:
- `AllowNull`
- `DisallowNull`
- `MaybeNull`
- `NotNull`
- `MaybeNullWhen`
- `NotNullWhen`
- `NotNullIfNotNull`
- `MemberNotNull`
- `MemberNotNullWhen`
- `DoesNotReturn`
- `DoesNotReturnIf`


### Generics

`T?` looks exactly the same in code for value-types and reference-types, but they work differently. The different behaviors of Nullable Value Types and Nullable Reference Types make generic constraints work differently for `T` and `T?`

If the type argument for `T` is a reference type, `T?` references the corresponding nullable reference type.
For example, if `T` is a string, then `T?` is a `string?`.

```csharp
#nullable enable

class Test<T> {
    public void DoSomething(T? value) { }
}

new Test<string>().DoSomething(null); // string?
```

If the type argument for `T` is a value type,`T?` references the same value type, `T`.
For example, if `T` is an int, the `T?` is also an `int`.

```csharp
#nullable enable

class Test<T> {
    public void DoSomething(T? value) { }
}

new Test<int>().DoSomething(null); // int + CS1503 error
```

If the type argument for `T` is a nullable reference type, `T?` references that same nullable reference type.
For example, if `T` is a string?, then `T?` is also a `string?`.

```csharp
#nullable enable

class Test<T> {
    public void DoSomething(T? value) { }
}

new Test<string?>().DoSomething(null); // string?
```

If the type argument for `T` is a nullable value type, `T?` references that same nullable value type.
For example, if `T` is an int?, then `T?` is also an `int?`.

```csharp
#nullable enable

class Test<T> {
    public void DoSomething(T? value) { }
}

new Test<int?>().DoSomething(null); // int?
```

### Generic constraints

- `class`
  - The `class` constraint means that `T` must be a non-nullable reference type (for example `string`).
  - The compiler produces a warning if you use a nullable reference type, such as `string?` for `T`.
- `class?`
  - The `class?` constraint means that `T` must be a reference type, either non-nullable (`string`) or a nullable reference type (for example `string?`). When the type parameter is a nullable reference type, such as `string?`, an expression of `T?` references that same nullable reference type, such as `string?`.
- `notnull`
  - The `notnull` constraint means that `T` must be a non-nullable reference type, or a non-nullable value type. Furthermore, when `T` is a value type, the return value is that value type, not the corresponding nullable value type.
  - If you use a nullable reference type or a nullable value type for the type parameter, the compiler produces a warning.

### Nullability attributes

#### `AllowNull`

Enables assigning `null` to a non-nullable type.

```csharp
#nullable enable

private string _screenName = GenerateRandomScreenName();

[AllowNull]
public string ScreenName {
    get => _screenName; //argument "value" is allowed to be null
    set => _screenName = value ?? GenerateRandomScreenName();
}
```


#### `DisallowNull`

Forbids assigning `null` to an otherwise nullable type.

```csharp
#nullable enable

private string? _comment;

[DisallowNull]
public string? ReviewComment {
    get => _comment;
    set => _comment = value ?? ArgumentNullException.ThrowIfNull(value);
}
```


#### `MaybeNull`

A non-nullable parameter, field, property, or return value may be `null`. Mostly used within generic types.

Use the MaybeNull attribute when your API should be a non-nullable type, typically a generic type parameter, but there may be instances where null would be returned.

```csharp
#nullable enable

[return: MaybeNull]
public T FirstOrDefault(this IEnumerable<T> source) {...}

[return: MaybeNull]
public T Find<T>(IEnumerable<T> sequence, Func<T, bool> predicate) {...}
```

`Find<T>` is expected to return a non-`null` value, but it may return `null` if there’s no matching element to `predicate` in `sequence`. Changing return type from `T` to `T?` may result in a different behavior for value-types and reference-types:
- If `T` is reference type, `T?` would mean the corresponding nullable reference type, `T?`
- If `T` is value-type, `T?` would mean the corresponding nullable value-type, `Nullable<T>`


Bad code example:

```csharp
#nullable enable

public T Find<T>(IEnumerable<T> sequence, Func<T, bool> predicate) {
    foreach (var item in sequence) {
        if (predicate(item))
            return item;
    }
    
    //Enforced suppression of a possible null return
    return default!;
}
```
Good code example:

```csharp
#nullable enable

[return: MaybeNull]
public T Find<T>(IEnumerable<T> sequence, Func<T, bool> predicate) {
    foreach (var item in sequence) {
        if (predicate(item))
            return item;
    }
    
    return default;
}
```


#### `NotNull`

A nullable parameter, field, property, or return value will never be `null`. When the method returns gracefully, it is ensured that the annotated value is not `null`.

```csharp
#nullable enable

public static void LogMessage(string? message) {
    ThrowWhenNull(message, $"{nameof(message)} must not be null");
    //Let the compiler know that message is not null beyond this point
    Console.WriteLine(message.Length);
}

public static void ThrowWhenNull([NotNull] object? value, string valueExpression = "") {
    _ = value ?? throw new ArgumentNullException(nameof(value), valueExpression);
    // other logic elided
}
```

#### `NotNullWhen`

A nullable argument won't be `null` when the method returns the specified `bool` value.

```csharp
#nullable enable

bool TryGetMessage(string key, [NotNullWhen(true)] out string? message) {
    if (_messageDictionary.ContainsKey(key))
        message = _messageDictionary[key];
    else
        message = null;
        
    return message is not null;
}

if (!TryGetMessage("sample", out string? msg)) {
    //mgs is null here
}
//msg is not null beyond this point
```


#### `MaybeNullWhen`

A non-nullable argument may be `null` when the method returns the specified `bool` value. Recommended to use it instead of `NotNullWhen` in cases where a generic type's nullability needs to be marked.

Using `T?` in the following code can have different behavior for reference-types and value-types.
```csharp
#nullable enable

public bool TryDoSomething<T>(string key, out T? result);
```

Resolve the issue using `MaybeNullWhen` attribute.
```csharp
#nullable enable

public bool TryDoSomething<T>(string key, [MaybeNullWhen(false)] out T result);
```

#### `NotNullIfNotNull`

Sometimes the null-state of a return value depends on the null-state of one or more arguments.
```csharp
#nullable enable

string? GetTopLevelDomainFromFullUrl(string? url)
```
The contract is that the return value would be `null` only when the argument `url` is `null`. To express that:

```csharp
#nullable enable

[return: NotNullIfNotNull("url")] // before C# 11 "nameof" cannot be used here
[return: NotNullIfNotNull(nameof(url))] // C# 11 and newer
string? GetTopLevelDomainFromFullUrl(string? url)
```

#### `MemberNotNull`

The listed member won't be `null` when the method returns. Useful for helper methods to initialize required fields and properties in constructor.

```csharp
#nullable enable

public class Container {
    private string _uniqueIdentifier;  // must be initialized
    private string? _optionalMessage;
    
    public Container() {
        Helper();
    }
    
    public Container(string message) {
        Helper();
        _optionalMessage = message;
    }
    
    [MemberNotNull(nameof(_uniqueIdentifier))]
    private void Helper() {
        _uniqueIdentifier = DateTime.Now.Ticks.ToString();
    }
}
```


#### `MemberNotNullWhen`

Marks a field or property to be not `null` when the method returns a given `bool` value.

```csharp
#nullable enable

public class UserSession {
    private string? _userName;
    
    public bool IsLoggedIn => _userName is not null;
    public string? Email { get; private set; }
    public string? DisplayName { get; private set; }
    
    [MemberNotNullWhen(true, nameof(DisplayName))]
    [MemberNotNullWhen(true, nameof(Email))]
    public virtual bool TryLogin(string user, string pass);
}
```

Attributes cannot be applied to tuple members. Eg. you want to create an async `TryDoSomethingAsync` method, there’s no way to properly annotate the result member. Use a custom `struct` instead.
```csharp
#nullable enable

//Compiler error
Task<([MemberNotNullWhen(true, nameof(Result))] bool Success, string? Result)> TryDoSomethingAsync();
```

#### `DoesNotReturn`

Marks a method that never returns gracefully, instead always throws an exception.
```csharp
#nullable enable

[DoesNotReturn]
private void FailFast() {
    throw new InvalidOperationException();
}

public void SetState(object containedField) {
    if (containedField is null)
        FailFast();

    // containedField can't be null:
    _field = containedField;
}
```


#### `DoesNotReturnIf`

Marks a method that does not return gracefully when a parameter has a given `bool` value.

```csharp
#nullable enable

private void Test(string? arg) {
    ThrowIf(arg is null);
    Console.WriteLine(arg.Length); //safe to dereference "arg"
}

private void ThrowIf([DoesNotReturnIf(true)] bool flag) {
    if (flag)
        throw new InvalidOperationException();
}
```

### Runtime guards

Do I still need to add guards for non-nullable reference types?
- Yes, you should – it is a runtime protection
- Nullable reference types are only compiler annotations, not strict restrictions.
- User code may still be able to inject `null` to a non-nullable reference type (eg. with null-forgiving operator `!`)

## Nullable reference types - Special cases

### `var`

- Introduced in C# 3
- Feature name: Implicitly typed local variables
- The actual underlying type of `var` is inferred at compile-time

`var` is always nullable in case of a reference-type when NRT is enabled:
```csharp
#nullable enable
var i = new object(); //inferred as "object?"
```

If you want to explicitly disallow assigning `null`, spell out the type instead of using `var`.
```csharp
#nullable enable
object i = new object();
```

`var` remains non-nullable for non-nullable value-types when NRT is enabled:
```csharp
#nullable enable
var i = 5; //inferred as "int"
```

Why not `var?`
https://github.com/dotnet/csharplang/blob/main/meetings/2019/LDM-2019-12-18.md#var

### `dynamic`

- Introduced in C# 4
- The `dynamic` type bypasses compile-time type checking
- Due to its nature, Nullable Reference Types has limited effect on `dynamic`
- Every member’s type of a `dynamic` expression is nullable-oblivious

```csharp
#nullable enable

//Non-nullable dynamic can be declared:
dynamic o = null; //warning CS8600: Cannot assign null to not-null type

//Nullable dynamic also can be declared:
dynamic? o = null; //no warning
int a = o.Length; //warning CS8602: Possible null-dereference
int b = o?.Length.NonExistingProp; //no warning (o.Length is nullable-oblivious)
```


## Migrating to Nullable Reference Types

 Setting the project defaults:
- Nullable disable as the default
  - Use when you are not adding new files to the project
  - You add a nullable preprocessor directive to each file as you update its code
- Nullable enable as the default
  - Set this default when you're actively developing new features, so every new file will be nullable-aware
  - You must add a `#nullable disable` to the top of each file
  - Remove these preprocessor directives as you address the warnings in each file
- Nullable warnings as the default
  - Two-phase migration
  - In the first phase, address warnings
  - In the second phase, turn on annotations for declaring a variable's expected null-state
  - You must add a `#nullable disable` to the top of each file
- Nullable annotations as the default
  - Annotate code before addressing warnings

Which of these strategies you pick depends on how much active development is taking place in your project.
- The more mature and stable your project, the better the first strategy.
- The more features being developed, the better the second strategy.

### Source generators
The global nullable context does not apply for generated code files. Under either strategy, the nullable context is disabled for any source file marked as generated, such as:
- In the .editorconfig, specify `generated_code = true` in a section that applies to that file.
- Put `<auto-generated>` or `<auto-generated/>` in a comment at the top of the file. It can be on any line in that comment, but the comment block must be the first element in the file.
- Start the file name with TemporaryGeneratedFile_
- End the file name with .designer.cs, .generated.cs, .g.cs, or .g.i.cs.

Generators can opt-in using the `#nullable` preprocessor directive

Techniques to resolve Nullable-related compiler warnings:
https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-messages/nullable-warnings


## Entity Framework Core
### Required and optional properties

**Having NRT disabled**: use Data Annotations to specify required and optional properties in entities.

```csharp
#nullable disable

public class CustomerWithoutNullableReferenceTypes {
    public int Id { get; set; }

    [Required] // Data annotations needed to configure as required
    public string FirstName { get; set; }

    [Required] // Data annotations needed to configure as required
    public string LastName { get; set; }

    public string MiddleName { get; set; } // Optional by convention
}
```

**With NRT enabled, use nullable properties (before C# 11)**: Mark optional properties nullable and use constructor binding.

```csharp
#nullable enable

public class Customer {
    public int Id { get; set; } 
    public string FirstName { get; set; } // Required by convention 
    public string LastName { get; set; } // Required by convention 
    public Address Address { get; set; } = null! // Required navigation properties cannot be initialized with constructor binding
    public string? MiddleName { get; set; } // Optional by convention 
    
    // Note the following use of constructor binding, which avoids compiled warnings (CS8618) 
    // for uninitialized non-nullable properties. 
    public Customer(string firstName, string lastName, string? middleName = null) {
        FirstName = firstName;
        LastName = lastName;
        MiddleName = middleName;
    }
}
```

**NRT enabled with C# 11 or newer**: use `required` keyword to avoid constructor binding.

```csharp
#nullable enable

public class Customer {
    public int Id { get; set; }
    public required string FirstName { get; set; } // Required by convention
    public required string LastName { get; set; } // Required by convention
    public string? MiddleName { get; set; } // Optional by convention
}
```

### Required navigation properties

Although a dependent will always exist for a given principal, it may or may not be loaded by a particular query, depending on the needs at that point in the program. At the same time, it may be undesirable to make these properties nullable, since that would force all access to them to check for `null`, even when the navigation is known to be loaded and therefore cannot be `null`.

Required navigations from the dependent (identified with foreign key) to the principal (identified with primary key):
- Should be non-nullable if it is considered a programmer error to access a navigation when it is not loaded.
- Should be nullable if it acceptable for application code to check the navigation to determine whether or not the relationship is loaded.
- Collection navigations (1:N relations), which contain references to multiple related entities, should always be non-nullable.
  - An empty collection means that no related entities exist, but the list itself should never be `null`.

If you'd like a stricter approach, you can have a non-nullable property with a nullable backing field. As long as the navigation is properly loaded, the dependent will be accessible via the property. If, however, the property is accessed without first properly loading the related entity, an `InvalidOperationException` is thrown, since the API contract has been used incorrectly.

```csharp
#nullable enable
private Address? _shippingAddress;

public Address ShippingAddress {
    set => _shippingAddress = value;
    get => _shippingAddress ?? throw new InvalidOperationException();
}
```

### `DbContext` and `DbSet<T>`

`DbSet<T>` in `DbContext` is typically uninitialized. EF Core 7.0 and above suppress compiler warnings, since EF automatically initializes these properties via reflection.

```csharp
#nullable enable
public DbSet<Customer> Customers { get; set; }
```

Workarounds for EF Core 6 and earlier:

```csharp
#nullable enable
public DbSet<Customer> Customers => Set<Customer>();
public DbSet<Customer> Customers { get; set; } = null!
```

### Navigating and including nullable relationships

With optional relationships, it's possible to encounter compiler warnings where an actual null reference exception would be impossible. EF Core guarantees that if an optional related entity does not exist, any navigation to it will simply be ignored, rather than throwing.

It is necessary to use the null-forgiving operator `!` to inform the compiler that an actual null value isn't possible.

```csharp
#nullable enable

var order = await context.Orders
    .Where(o => o.OptionalInfo!.SomeProperty == "foo")
    .ToListAsync();

//A similar issue occurs when including multiple levels of relationships across optional navigations:
var order = await context.Orders
    .Include(o => o.OptionalInfo!)
    .ThenInclude(op => op.ExtraAdditionalInfo)
    .SingleAsync();
```

### Limitations in older versions

Prior to EF Core 6.0, the following limitations applied:

- The public API was "null-oblivious", making it sometimes awkward to use when the NRT feature is turned on.
  - For example: `FirstOrDefaultAsync`
- Scaffolding (DB-First, reverse engineering a live database into EF Core code):
  - The reverse engineered code assumes that NRT is turned off
  - Manual editing of scaffolded files are necessary


## Dealing with required and optional data

The problems we face:
- Development context and business rules determine whether something is required or optional – no standardized way to express this in code
- Any reference type can technically be null, even required data
- Some tools are available that helps in code/API annotation, but they are optional to use

Different application layers require different ways of checking and enforcing the proper presence of required data.
- Business logic
  - Use strong enforcement of required data in code with constructors and methods that ensure an always valid state and proper object integrity.
  - Omitting required data should produce compiler errors or at least runtime exceptions.
- Presentation layer
  - Use weak or no enforcement. Allow invalid state, but validate user input before sending to business logic layer. Provide clear error messages to the user.
  - Some presentation and serialization frameworks may use constrained constructor antipattern or reflection to create presentation layer objects.
    - **Constrained constructor**: A framework requires user code to declare (only) one constructor with a specific signature. Typically parameterless ctor is required. 
- Persistence layer
  - **RDBMS**: Use strong enforcement at schema level in the RDMBS. In case of ORM, use proper configuration of the schema representation.
  - **NoSQL**: Data validation is done in the application in most cases.


## Monads

To keep it simple, a monad is:
- A not so easy topic in category theory
- In programming, it originates from functional programming
- "Wrapper around some T type"
- Chains multiple operations into a single instruction


### Example with null-values

Imagine these models:

```csharp
#nullable enable
class Person {
    public string FirstName { get; }
    public string ? LastName { get; }

    public Person(string fn) {
        FirstName = fn;
    }

    public Person(string fn, string ln) {
        FirstName = fn;
        LastName = ln;
    }
}

class Book {
    public string Title { get; }
    public Person ? Author { get; }
    
    public Book(string title) {
        Title = title;
    }
    
    public Book(string title, Person author) {
        Title = title;
        Author = author;
    }
}
```

Display information to the user of the following books:
```csharp
#nullable enable
string GetPersonDisplayName(Person p) {
    return p.LastName is not null
        ? $ "{p.FirstName} {p.LastName}" 
        : p.FirstName;
}

string GetBookInfo(Book book) {
    return book.Author is not null 
        ? $ "{book.Title} by {GetPersonDisplayName(book.Author)}" 
        : book.Title;
}

Person a1 = new Person("John");
Person a2 = new Person("John", "Doe");

Book b1 = new Book("C# in Depth");
Book b2 = new Book("C# in Depth", a1);
Book b3 = new Book("C# in Depth", a2);

var a = GetBookInfo(b1); // C# in Depth
var b = GetBookInfo(b2); // C# in Depth by John
var c = GetBookInfo(b3); // C# in Depth by John Doe
```


### With the `Maybe<T>` monad
We can use this monad to represent optional data in code. Also known as `Option<T>`. An example implementation is shown below, however, properly tested NuGet packages are also available.

```csharp
#nullable enable

public readonly struct Maybe<T> where T: class {
    private readonly T? _value;
    
    [MemberNotNullWhen(true, nameof(_value))]
    public bool HasValue { get; }
    
    private Maybe(T? value) {
        _value = value;
        HasValue = value is not null;
    }
    
    public static Maybe<T> Some(T value) => new(value);
    public static Maybe<T> None => new();
    
    public TResult Match<TResult> (Func<T, TResult> some, Func<TResult> none)
        => HasValue ? some(_value) : none();
        
    public Maybe< TResult> Bind <TResult> (Func<T, Maybe<TResult>> func) where TResult: class
        => HasValue ? func(_value) : Maybe<TResult>.None;
    public Maybe<TResult> Bind <TResult> (Func<T, TResult> func) where TResult: class
        => HasValue ? Maybe<TResult>.Some(func(_value)) : Maybe<TResult>.None;
        
    public T GetValueOrDefault(T defaultValue)
        => HasValue ? _value : defaultValue;
        
    public static implicit operator Maybe<T> (T? value)
        => value is null ? None : Some(value);
}
```

Use `Maybe<T>` to represent optional data in model classes:

```csharp
#nullable enable

class Person {
    public string FirstName { get; }
    public Maybe<string> LastName { get; }
    
    public Person(string fn) {
        FirstName = fn;
        LastName = Maybe<string>.None;
    }
    
    public Person(string fn, string ln) {
        FirstName = fn;
        LastName = Maybe<string>.Some(ln);
    }
}

class Book {
    public string Title { get; }
    public Maybe<Person> Author { get; }
    
    public Book(string title) {
        Title = title;
        Author = Maybe<Person>.None;
    }
    
    public Book(string title, Person author) {
        Title = title;
        Author = Maybe<Person>.Some(author);
    }
}
```

Make use of the `Maybe<T>`:

```csharp
#nullable enable

Person a1 = new Person("John");
Person a2 = new Person("John", "Doe");

Book b1 = new Book("C# in Depth");
Book b2 = new Book("C# in Depth", a1);
Book b3 = new Book("C# in Depth", a2);

string GetPersonDisplayName(Person p) {
    return p.LastName
        .Bind(lastName => $"{p.FirstName} {lastName}")
        .GetValueOrDefault(p.FirstName);
}

string GetBookInfo(Book book) {
    return book.Author
        .Bind(GetPersonDisplayName)
        .Bind(author => $"{book.Title} by {author}")
        .GetValueOrDefault(book.Title);
}

var a = GetBookInfo(b1); // C# in Depth
var b = GetBookInfo(b2); // C# in Depth by John
var c = GetBookInfo(b3); // C# in Depth by John Doe
```

## P/Invoke

The value-types `IntPtr` and `UIntPtr` are integers with the exact same bit length of an unmanaged pointer.

C# 9 introduced `nint` and `nuint`:
- Contextual keywords
  - Only available if no other type is defined by the dev with the same name
- Internal implementation is represented with `IntPtr` and `UIntPtr`
- Provides operations that are appropriate for integer types

Starting from C# 11 targeting .NET 7 or newer `IntPtr` and `UIntPtr` are:
- Still contextual keywords
- `nint` is alias for `IntPtr`, `nuint` is alias for `UIntPtr`

`IntPtr.Zero` and `UIntPtr.Zero` are typically used to represent null-pointers in unmanaged code and native calls. Not equivalent to null – check for `(U)IntPtr.Zero` instead.
