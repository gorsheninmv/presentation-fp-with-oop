---
theme: default
layout: 'cover'
background: /cover-background.png
class: text-left
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
defaults:
  layout: 'default'
fonts:
  sans: 'arial'
  fallback: false
transition: slide-left
css: unocss
title: FP with OOP
---

<div class="relative">

<img class="w-1/4 absolute bottom-36" src="/directum-logo.png">

## Элементы функционального программирования
## в объектно-ориентированных приложениях
Секретный ингредиент безопасного и предсказуемого кода

</div>

<div class="pt-20 absolute bottom-8 left-8">
  <span class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Михаил Горшенин
  </span>

</div>


---

# Обо мне

- Последние 3 года работаю в компании Directum
- Платформа для интеллектуальной системы управления документооборотом и цифровыми процессами
- Основной стек: C# и Typescript
- Интересуюсь ФП

<div class="absolute bottom-15 right-15">
  <div>
    <img display="inline" class="h-8 m-2" src="/icons8-telegram.svg">
    <span>@gorsheninmv</span>
  </div>
  <div>
    <img display="inline" class="h-8 m-2" src="/icons8-twitter.svg">
    <span>@iamhexy</span>
  </div>
</div>

<style>
    .slidev-layout {
      font-size: 1.6rem;
    }
</style>

---

<img class="w-1/6 absolute right-15 top-15" src="/dont-panic.png">

<v-clicks>

<div>
  <div class="inline-grid grid-flow-col items-center gap-x-2">
    <img class="inline h-8" src="/fsharp.svg">
    <span>Немного кода на F#</span>
  </div>
</div>

<div>
  <div class="inline-grid grid-flow-col items-center gap-x-2">
    <img class="inline h-8" src="/csharp.svg">
    <span>Чуть больше кода на C#</span>
  </div>
</div>
<div>
  <div class="inline-grid grid-flow-col items-center gap-x-2">
    <img class="inline h-8" src="/panic.svg">
    <span>Функторы, монады</span>
  </div>
</div>

</v-clicks>

<style>
    .slidev-layout {
      display: flex;
      flex-direction: column;
      justify-content: center;
    }

    .slidev-vclick-current {
      font-weight: bold;
      font-size: 2.6em;
    }

    .slidev-vclick-current img {
      scale: 1.5;
    }

</style>

---
layout: image-right
image: /hoare-cover.jpg
---

# 2009 QCon

Summary

Tony Hoare introduced Null references in ALGOL W back in 1965 "simply because it was so easy to implement", says Mr. Hoare. He talks about that decision considering it "my billion-dollar mistake".

- Null references have historically been a bad idea
- Programming language designers should be responsible for the errors in programs written in that language

---

# NullReferenceException

<div class="grid grid-cols-[1fr_2fr] gap-4">
<div class="flex flex-col justify-center">
```csharp {all|3} {lines:true}
private void ConvertParameters(Foo foo)
{
  foreach (var parameter in foo.Parameters)
  {
    this.ConvertParameter(parameter);
  }
}
```
</div>
<div>
<v-click>

```csharp {all|3,8-11,17,18} {lines:true}
public class Foo : FooBase
{
  public Collection<Bar> Parameters { get; set; }

  public override object Clone()
  {
    var clone = base.Clone() as Foo;
    if (this.Parameters != null)
      this.Parameters = new Collection<Bar>(this.Parameters
        .Select(v => v.Clone() as Bar)
        .ToList());
    return clone;
  }

  public override object DoSmth(string fizz, params object[] buzz)
  {
    if (this.Parameters == null)
      return Array.Empty<object>()
    var result = new List<object>();
    foreach (var parameter in this.Parameters)
      result.Add(parameter.DoSmth(fizz, buzz));
    return result.ToArray();
  }
}

```
</v-click>
</div>
</div>

---

# Причины NullReferenceException

<div class="flex flex-col h-3/4 justify-center">

- Автор понимает, что значение может быть null
- Автор не понимает, что значение может быть null  
(из-за ошибки или по невнимательности)

</div>

<style>
  li {
    font-size: 1.5em;
  }
</style>

---

# Name convention


```csharp
public Foo FindFoo(Bar bar);
public Foo GetFooOrDefault(Bar bar);
```

```csharp
public Foo GetFoo(Bar bar);
```

```csharp
public bool TryGetFoo(Bar bar, out Foo foo);
```

<br/>
<br/>

\+ Просто реализовать

<br/>

\- Сложно придерживаться  
\- Наличие null-маркера в имени не гарантирует, что проверка на null будет выполнена

---


# Null object pattern

<img src="/null-object.png">

<br/>
<br/>

\- Раздувается кодовая база  
\- Не забыть и не полениться сделать NullObject наследника  
\- Где попало начинают появляться NotImplementedException

---


# Null reference types (C# 8.0)

```csharp
class User
{
  string Name { get; set; }
  string? MiddleName { get; set; }
}
```

<br />
<br />

\+ Статический анализ

<br />

\- После включения получаем много ошибок в проекте  
\- В библиотеках может быть отключено, но наш код будет считать, что все ок  
\- Можно отключить через null-forgiving синтаксис

---

# А что в ФП?

```fsharp {all} {lines:true}
// F#

type User = {
  Name: string
  MiddleName: string option
}

let fullName user =
  match user.MiddleName with
  | Some v -> user.Name + " " + v
  | None -> user.Name
```

\* В ФП нет методов, только функции, в C# принято все называть методами.

---

# Maybe\<T\>

```csharp {1-6,8-14,16-19,21|1,2,7,15,20} {lines:true}
// C#
public abstract class Maybe<T>
{
  public static Maybe<T> Some(T value) => new Some(value);
  public static Maybe<T> None() => new None();

  public abstract R Match<R>(Func<T, R> Some, Func<R> None);
}

public sealed class Some : Maybe<T>
{
  private T Value { get; }
  public Some(T value) => this.Value = value;

  public override R Match<R>(Func<T, R> Some, Func<R> None) => Some(this.Value);
}

public sealed class None : Maybe<T>
{
  public override R Match<R>(Func<T, R> Some, Func<R> None) => None();
}
```
---

# Maybe\<T\>

<div class="grid grid-cols-[1fr_2fr] gap-4">
<div>
```fsharp {all} {lines:true}
// F#

type User = {
  Name: string
  MiddleName: string option
}

let fullName user =
  match user.MiddleName with
  | Some v -> user.Name + " " + v
  | None -> user.Name
```
</div>
<div>
```csharp {all} {lines:true}
// C#

public class User
{
  public string Name { get; set; }
  public Maybe<string> MiddleName { get; set; }
}

public static string GetFullName(User user)
{
  return user.MiddleName.Match(
    Some: v => user.Name + " " + v,
    None: () => user.Name
  );
}
```
</div>
</div>

---

# Maybe\<T\> &#10142; Maybe\<R\>

<div class="grid grid-cols-2 gap-2">
<div>
```csharp {all} {lines:true}
// C#

public abstract class Maybe<T>
{
  public abstract R Match<R>(
    Func<T, R> Some,
    Func<R> None);

  public Maybe<R> Map<R>(Func<T, R> map) =>
    this.Match(
      Some: v => Maybe<R>.Some(map(v)),
      None: Maybe<R>.None
    );
}
```

</div>

<div>

<v-click>

```csharp {1-6|1-11|all} {lines:true}
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static User GetFromDTO(UserDTO dto)
{
  // build from DTO
}

public static Maybe<User> GetUser(int id)
{
  return FindUserById(id)
    // Maybe<UserDTO> -> Maybe<User>
    .Map(GetFromDTO);
}
```
</v-click>

</div>
</div>

---

# Функтор

<div class="grid grid-cols-[1fr_1.5fr] gap-4">

<div v-click="1">
  <object data="/elev-world.svg" class="h-1/4"/>
</div>
<div>
  <img class="h-1/6" src="/wlaschin.jpg" >

<v-clicks>

<div>

```csharp
// C#, class Maybe<T>
public Maybe<R> Map(Func<T, R> map)
```
```fsharp
// F#
let map = ('t -> 'r) -> Maybe<'t> -> Maybe<'r>
```
</div>

</v-clicks>

</div>

</div>


---

# Для чего нужен функтор

<br/>
Для изменения типа без понимания структуры возвышенного типа
<br/>

```fsharp
// F#
let map = ('a -> 'b) -> E<'a> -> E<'b>
```

<div grid="~ cols-[1.5fr_2fr] gap-4">
<div>


```csharp
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static User GetFromDTO(UserDTO dto)
{
  // build from DTO
}

public static Maybe<User> GetUser(int id)
{
  return FindUserById(id)
    // Maybe<UserDTO> -> Maybe<User>
    .Map(GetFromDTO);
}
```

</div>
<div>
```csharp
// C#

private static List<UserDTO> FindUsersByConferenceId(int id)
{
  return new List<UserDTO>();
}

private static User GetFromDTO(UserDTO dto)
{
  // build from DTO
}

public static List<User> GetUsers(int conferenceId)
{
  return FindUsersByConferenceId(id)
    // List<UserDTO> -> List<User>
    .Select(GetFromDTO);
}
```
</div>

</div>

---

# T &#10142; Maybe\<R\>

```csharp {1-11|all} {lines:true}
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static Maybe<User> GetFromDTO(UserDTO dto)
{
  // build from DTO if possible
}

public static Maybe<User> GetUser(int id)
{
  return FindUserById(id)
    // Maybe<UserDTO> -> Maybe<Maybe<User>>
    .Map(GetFromDTO);
}
```

---

# T &#10142; Maybe\<T\>

<div class="flex flex-col h-full">
  <object data="/two-funcs.svg" class="h-1/2" />
  <object data="/binded-two-funcs.svg" class="h-1/2" v-click/>
</div>

---

# T &#10142; Maybe\<T\>

<div class="flex flex-col h-full justify-center">
  <object data="/maybe-railway-programming.svg" class="h-1/2"/>
</div>

---

# Bind

```fsharp
// F#
let bind = ('a -> E<'b>) -> E<'a> -> E<'b>
```
<div class="grid grid-cols-2 gap-4">
<div>

<v-click>

```csharp {all} {lines:true}
// C#

public abstract class Maybe<T>
{
  public abstract R Match<R>(Func<T, R> Some,
    Func<R> None);

  public Maybe<R> Bind<R>(Func<T, Maybe<R>> bind) =>
    this.Match(
      Some: v => bind(v),
      None: Maybe<R>.None
    );
}
```

</v-click>

</div>

<div>

<v-clicks>

```csharp {all} {lines:true}
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static Maybe<User> GetFromDTO(UserDTO dto)
{
  // build from DTO if possible
}

public static Maybe<User> GetUser(int id)
{
  return FindUserById(id)
    // Maybe<UserDTO> -> Maybe<User>
    .Bind(GetFromDTO);
}
```
</v-clicks>

</div>
</div>

---

# Railway-oriented programming

<div class="flex flex-col h-full">
  <object data="/railway-programming.svg" class="h-1/2"/>
  <object data="/railway-programming.png" class="h-1/2"/>
</div>

---

# Монада

```fsharp {all} {lines:true}
// F#
let bind = ('a -> E<'a>) -> E<'a> -> E<'b>
let return = 'a -> E<'a>
```
<br />

<div grid="~ cols-2 gap-4">
<div>

Классический подход
```csharp {all} {lines:true}
// C#

public static Foo GetFooOrDefault()
{
  var data = ReadData();
  if (data == null)
    return null;

  return Parse(data);
}
```

</div>
<div>

Подход с монадой Maybe
```csharp {all} {lines:true}
// C#

public static Maybe<Foo> GetFoo()
{
  return ReadData()
    .Bind(Parse);
}
```

</div>
</div>

---

# Для чего нужна монада

<br/>
Для построения цепочек вычислений в стиле Railway-oriented programming*.

<br/>
<br/>

```csharp {all} {lines:true}
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static Maybe<User> GetFromDTO(UserDTO dto)
{
  // build from DTO if possible
}

public static Maybe<User> GetUser(int id)
{
  return FindUserById(id)
    // Maybe<UserDTO> -> Maybe<User>
    .Bind(GetFromDTO);
}
```
---

# Map + Bind

```csharp {all} {lines:true}
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static Maybe<User> GetFromDTO(UserDTO dto)
{
  // build from DTO if possible
}

private static string GetFullName(User user)
{
  return user.Name + " " + user.MiddleName;
}

public static Maybe<string> GetUserFullName(int id)
{
  return FindUserById(id)
    .Bind(GetFromDTO) // Maybe<UserDTO> -> Maybe<User>
    .Map(GetFullName) // Maybe<User> -> Maybe<string>
}
```

---
clicks: 2
---

# Maybe\<T\>

<div class="grid grid-cols-[2fr_1.5fr]">
<div>
```csharp {all|1-3,8-10|1-3,10,12,13,16,18,19,22}
// C#
public abstract class Maybe<T>
{
  public static Maybe<T> Some(T value) => new Some(value);
  public static Maybe<T> None() => new None();

  public abstract R Match<R>(Func<T, R> Some, Func<R> None);
  public Maybe<R> Map<R>(Func<T, R> map) => ...;
  public Maybe<R> Bind<R>(Func<T, Maybe<R>> bind) => ...;
}

 public sealed class Some : Maybe<T>
 {
   public override R Match<R>(Func<T, R> Some, Func<R> None) =>
     Some(this.Value);
 }

 public sealed class None : Maybe<T>
 {
   public override R Match<R>(Func<T, R> Some, Func<R> None) =>
     None();
 }
```
</div>
<div class="flex flex-col justify-center">

<div v-if="$slidev.nav.clicks > 0">

- Делегат-преобразователь может принимать только один аргумент

</div>
<div v-if="$slidev.nav.clicks > 1">

- Обертка -- reference type

</div>

</div>
</div>

---

# C# Functional Programming Language Extensions

<div class="flex flex-col h-full justify-center">
<div grid="~ cols-2">
<div>
```csharp {all} {lines:true}
// C#
[Serializable]
public readonly struct Option<A> :
  IEnumerable<A>,
  IOptional,
  IEquatable<Option<A>>,
  IComparable<Option<A>>,
  IComparable,
  ISerializable
{
    internal readonly A? Value;
    internal readonly bool isSome;

```
</div>
<div>
  <img src="/language-ext.svg">
</div>
</div>
</div>

---

# "Why don't you just use F#?"

<br/>
I often see this phrase pulled out when people are sharing lang-ext on twitter, or if the project ends up on Reddit, or on the front page of Hacker News.  It's as though the person writing it assumes all programmers are working on greenfield projects and have total freedom to write in whatever language they like.  In which case, why not Haskell?  It's better than C# and F# combined.

The reason I wrote this project is because I am the CTO of a software company in London, and we have a project that's well into 15 years of development.  It is a C# project and it is now enormous.

---

# Модифицированный пример

<div class="grid grid-cols-[1fr_2fr] gap-4">
<div class="flex flex-col justify-center">
```csharp {all} {lines:true}
private void ConvertFoo(Foo foo)
{
  foo.Parameters.Do(ps => {
    foreach (var parameter in ps)
    {
      this.ConvertBar(parameter);
    }
  });
}
```
</div>
<div>

```csharp {all} {lines:true}
public class Foo : FooBase
{
  public Option<Collection<Bar>> Parameters { get; set; }

  public override object Clone()
  {
    var clone = base.Clone() as Foo;
    clone.Parameters = this.Parameters.Match(
      Some: ps => new Collection<Bar>(ps.Select(v => v.Clone() as Bar)
        .ToList()),
      None: Option<Collection<Foo>>.None
    );
    return clone;
  }

  public override object DoSmth(string fizz, params object[] buzz)
  {
    return this.Parameters.Match(
      Some: parameters => parameters.Select(p => p.DoSmth(fizz, buzz))
        .ToArray(),
      None: Array.Empty<object>());
  }
}

```
</div>
</div>

---

# Оказались на красных рельсах, но по какой причине?

```csharp {all} {lines:true}
// C#

private static Maybe<UserDTO> FindUserById(int id)
{
  return Maybe<User>.None();
}

private static Maybe<User> GetFromDTO(UserDTO dto)
{
  // build from DTO if possible
}

public static Maybe<User> GetUser(int id)
{
  return FindUserById(id)
    // Maybe<UserDTO> -> Maybe<User>
    .Bind(GetFromDTO);
}
```
---

# Railway-oriented programming

<div class="flex flex-col h-full">
  <object data="/either-railway-programming.svg" class="h-1/2"/>
  <object data="/railway-programming.png" class="h-1/2"/>
</div>
---

# Either\<TLeft, TRight\> (Either\<TResult, TError\>)

<div grid="~ cols-[1.2fr_1fr] gap-2">
<div>

```csharp {all} {lines:false}
// C#

public abstract class UserError {}
public class UserNotFound : UserError {}
public class DtoToUserError : UserError {}

```

```csharp {all} {lines:false}
public static Either<UserDTO, UserError> FindUserById(int id)
{
  Either<UserDTO, UserError>.Right(new UserNotFound());
}

public static Either<User, UserError> GetFromDTO(UserDTO dto)
{
  if (parsed)
    return Either<User, UserError>.Left(dto);
  return Either<User, UserError>.Right(new DtoToUserError());
}

public static Either<User, UserError> GetUser(int id)
{
  FindUserById(id).BindLeft(GetFromDTO);
}
```

</div>
<div class="h-1/2 self-center">
  <object data="/user-error-railway.svg" class="h-1/2 m-auto "/>
</div>
</div>

---

# Errors vs Exceptions

<br/>

Основной минус исключений -- никогда не знаешь, где их ожидать.

<br/>
А еще они дорогие.

<br/>
<br/>

Типы нештатных ситуаций:
- Доменные ошибки (пользователь не найден, неверный формат электронного адреса)
- Инфраструктурные ошибки (ошибка подключения к БД, BadRequest)
- Исключительные ситуации, когда дальнейшее выполнение приложения невозможно (OutOfMemoryException, не удалось прочитать конфиг)

---

# Выводы

- null &#8658; Maybe\<T\> (Option\<T\>)
- Exception &#8658; Either\<TLeft, TRight\> (Either\<TResult, TError\>)
- Maybe\<T\> и Either\<TLeft, TRight\> -- это монады
- Методы Map и Bind позволяют строить декларативные цепочки вычислений
  <br/>
  <br/>
- Подробнее с функциональными возможностями можно ознакомиться,  
используя библиотеку language-ext

---

# Полезные ресурсы

<div class="flex flex-col h-full">
<div class="flex gap-8 items-center">
<div class="justify-center">

- Null References: The Billion Dollar Mistake, Tony Hoare
- C# Functional Programming Language Extensions
- Essential F#, Ian Russel
- F# for fun and profit web site, Scott Wlaschin
- Domain modeling made functional, Scott Wlaschin

</div>
<div class="w-50 h-50 float-right">
    <img src="/github-repo-qr.svg">
</div>
</div>

</div>

---
layout: cover
---

# Спасибо!
<br/>
<br/>

## Ваши вопросы

<div class="absolute bottom-5 right-1/3">
    <img class="h-45" src="/telegram-qr.svg">
</div>
