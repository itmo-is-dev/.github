# Property declaration

1. При объявлении автосвойств, помещайте аксессоры на одной строке с названием и типом
```csharp
// ПЛОХО
public int Value
{ get; set; }

// ХОРОШО
public int Value { get; set; }
```
2. При объявлении get-only свойств, используйте bodied expressions вместо явного `get` аксессора
```csharp
// ПЛОХО
public IReadOnlyCollection<string> Values
{
    get 
    {
        return _values;
    }
}

public IReadOnlyCollection<string> Values
{
    get => _values;
}

// ХОРОШО
public IReadOnlyCollection<string> Values => _values;
```
