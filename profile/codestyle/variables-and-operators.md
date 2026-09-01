# Variable and operator declaration

1. Для неочевидных числовых значений необходимо создавать именованные константы. Не используйте в коде магические числа.

```csharp
// ПЛОХО
public Student AddStudent(string name, string surname)
{
   var student = new Student(name, surname);

   // сравниваем с магическим числом
   if (_students.Count >= 20)
   {
      throw new Exception("Students limit exceeded");
   }

   _students.Add(student);

   return student;
}

// ХОРОШО
public Student AddStudent(string name, string surname)
{
   var student = new Student(name, surname);

   // сравниваем с читаемой константой
   if (_students.Count >= MaxStudentsAmount)
   {
      throw new Exception("Students limit exceeded");
   }

   _students.Add(student);

   return student;
}
```

2. Используйте `var`, только если тип переменной понятен из контекста (например, при инициализации через конструктор). Если тип неочевиден или является встроенным, указывайте тип явно. Правила и инспекции IDE / `.editorconfig` имеют приоритет над описанием в данном документе.

```csharp
// ПЛОХО
var percents = bankAccount.CalculatePercents();
var count = 0;

// ХОРОШО
Money percents = bankAccount.CalculatePercents();
int count = 0;
var students = new List<Student>();
```

3. Задавайте `default` ветку в операторе `switch` (или `_` в switch-выражениях). Если поведение не определено — выбрасывайте исключение. Предпочитайте switch-выражения (`switch expressions`) классическим операторам `switch`.

```csharp
// ПЛОХО: отсутствие обработки неопределенного поведения
switch (deposit)
{
   case < 30:
      Console.WriteLine("Your percent is 3");
      break;
   case < 50:
      Console.WriteLine("Your percent is 5");
      break;
}

// ХОРОШО: использование switch expression и явный выброс исключения в default-ветке
decimal percent = deposit switch
{
    < 30 => 3m,
    < 50 => 5m,
    _ => throw new InvalidOperationException($"Unexpected deposit value: {deposit}"),
};
```

4. При сравнении переменной с константой сначала указывается переменная, потом константа.

```csharp
// ПЛОХО
if (MaxStudentsAmount <= _students.Count)
{
   throw new InvalidOperationException("Students limit exceeded");
}

// ХОРОШО
if (_students.Count >= MaxStudentsAmount)
{
   throw new InvalidOperationException("Students limit exceeded");
}
```

5. Локальные переменные должны объявляться и инициализироваться как можно ближе к месту их непосредственного использования.

```csharp
// ПЛОХО
public void ProcessNumbers(IEnumerable<int> numbers)
{
   var oddOnly = new List<int>();
   var oddUnique = new List<int>();
   int count;

   // ...вычисления...
   oddOnly.AddRange(numbers.Where(x => x % 2 != 0));

   // ...вычисления...
   oddUnique.AddRange(oddOnly.Distinct());
}

// ХОРОШО
public void ProcessNumbers(IEnumerable<int> numbers)
{
   var oddOnly = numbers.Where(x => x % 2 != 0).ToList();
   // ...вычисления...

   var oddUnique = oddOnly.Distinct().ToList();
   // ...вычисления...
}
```

6. Минимизируйте уровень вложенности, где это возможно без потери читаемости (Guard Clauses). Этого можно добиться ранним выходом (`return` / `throw`), инвертированием условного оператора `if` ([Replace Nested Conditional with Guard Clauses](https://refactoring.com/catalog/replaceNestedConditionalWithGuardClauses.html)), а также использованием встроенных `ThrowIf*` методов (`ArgumentNullException.ThrowIfNull`, `ArgumentOutOfRangeException.ThrowIfNegativeOrZero` и т.д.).

```csharp
// ПЛОХО: лишняя вложенность через else и ручные проверки аргументов
if (amount <= 0)
{
    throw new ArgumentOutOfRangeException(nameof(amount));
}
else
{
    if (_students.Count >= MaxStudentsAmount)
    {
        throw new InvalidOperationException("Students limit exceeded");
    }
    else 
    {
        _students.Add(student);
        return student;
    }
}

// ХОРОШО: Guard Clauses и встроенные Throw-хелперы
ArgumentOutOfRangeException.ThrowIfNegativeOrZero(amount);

if (_students.Count >= MaxStudentsAmount)
{
    throw new InvalidOperationException("Students limit exceeded");
}

_students.Add(student);

return student;
```

7. Не используйте искусственные boolean-флаги для управления условиями выхода из цикла (когда можно использовать `break`, `return` или понятное условие завершения).

```csharp
// ПЛОХО: управление выходом через внешний флаг-мутатор
var isFound = false;
foreach (var student in students)
{
    if (isFound is false && student.Id == targetId)
    {
        ProcessStudent(student);
        isFound = true;
    }
}

// ХОРОШО: немедленный выход через break или return
foreach (var student in students)
{
    if (student.Id == targetId)
    {
        ProcessStudent(student);
        break;
    }
}
```

8. Предпочитайте `condition is false` вместо `!condition` (not pattern).

```csharp
// ПЛОХО
if (!isFound)
{
   ...
}

// ХОРОШО
if (isFound is false)
{
   ...
}
```
