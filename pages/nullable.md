# Nullable

```csharp
public struct Nullable<T> where T : struct
```
<v-click>

<div class="grid grid-cols-[1fr_2fr] gap-4">
<div>

```csharp {all} {lines:true}
public struct Maybe<T>
  where T: class
{
  public T Value { get; }
  public bool HasValue { get; }

  public Maybe()
  {
    this.Value = default;
    this.HasValue = false;
  }

  public Maybe(T value)
  {
    this.HasValue = value is not null;
    this.Value = value ?? default;
  }
```
</div>
<div>

```csharp {all} {lines:true, startLine:17}
  public static bool operator ==(Maybe<T> value, object another)
  {
    if (another is null && !value.HasValue)
      return true;
    if (another is not Maybe<T> cast)
      return false;
    return value.HasValue == cast.HasValue && value.Value.Equals(cast.Value);
  }

  public static bool operator !=(Maybe<T> value, object another) =>
    !(value == another);

  public static implicit operator T(Maybe<T> value)
  {
    if (value.HasValue)
      return value.Value;
    throw new ArgumentNullException();
  }
}
```
</div>
</div>

</v-click>

---

# Nullable


```csharp {all} {lines:true}
public class User
{
  public string Name { get; set; }
  public Maybe<string> MiddleName { get; set; }
}

public static string GetFullName(User user)
{
  if (user.MiddleName != null)
    return user.Name + " " + user.MiddleName.Value;
  else
    return user.Name;
}
```
---
