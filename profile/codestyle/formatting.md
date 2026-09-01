# Formatting

1. Добавляйте переносы строк до и после многострочных элементов кода (кроме случаев когда элемент находится в конце блока). 
Однострочные элементы кода можно группировать без пропуска строк.

### Хорошо
```csharp
var location = "Yerevan";
var age = 69;

var relevantStudents = _students
   .Where(x => x.Location.Equals(location))
   .Where(x => x.Age.Equals(age));
   
foreach (var student in relevantStudents)
{
   Console.WriteLine($"Найден дед - {student.Name}");
}
```

### Плохо
```cs
var location = "Yerevan";
var age = 69;
var relevantStudents = _students
   .Where(x => x.Location.Equals(location))
   .Where(x => x.Age.Equals(age));
foreach (var student in relevantStudents)
{
   Console.WriteLine($"Найден дед - {student.Name}");
}
```

2. Добавляйте перенос строки перед оператором `return`.

### Хорошо
```csharp
public double Calculate(double width, double height)
{
    var baseArea = width * height;
    var scale = baseArea / 2.0;
   
    return baseArea + scale;
}
```

### Плохо
```csharp
public double Calculate(double width, double height)
{
    var baseArea = width * height;
    var scale = baseArea / 2.0;
    return baseArea + scale;
}
```
