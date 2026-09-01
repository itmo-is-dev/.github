# Naming

1. Основой нейминга является кодстайл майкрософта: <https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/naming-guidelines>;
2. Имена должны быть читабельными и понятными, без сокращений и грамматических ошибок во всех идентификаторах (классы, методы, свойства, переменные). Исключение – общепринятые сокращения, напр. `i`, `j`, `k` в циклах;

```csharp
// ПЛОХО
public class StudRepo { ... }
public void CalcAvgGrade() { ... }

var a = new List<Student>();
var stds = new List<Student>();
var list = new List<Student>();

// ХОРОШО
public class StudentRepository { ... }
public void CalculateAverageGrade() { ... }

var students = new List<Student>();
```
3. Не используйте отрицание в названиях (например, `isNotValid`, `hasNoAccess`). Отрицание в имени создает лишнюю когнитивную нагрузку и требует мысленного разворачивания дополнительной логической инверсии при чтении условий, особенно в комбинации с проверками (например, `isNotValid is false`):

```csharp
bool isRed = color == Colors.Red; // Good
bool isNotBlue = color != Colors.Blue; // Bad

if (isRed is false // Ok
    && isNotBlue is false) // Трудно воспринимать (двойное отрицание)
    return ...
```
4. Избегайте конструкций с предлогом `Of` вида `<характеристика>Of<Сущность>` (например, `numberOfStudents`, `countOfOrders`, `listOfUsers`). Размещайте ключевую сущность в начале, а уточняющую характеристику — в качестве суффикса: `<сущность><Характеристика>` (`studentsCount`, `ordersCount`), либо используйте множественное число для коллекций (`students`, `users`). Это делает названия чище и логичнее группирует переменные.

```csharp
// ПЛОХО
int numberOfStudents = 10;
int countOfOrders = 5;
List<User> listOfUsers = GetUsers();

// ХОРОШО
int studentsCount = 10;
int ordersCount = 5;
IReadOnlyCollection<User> users = GetUsers();
```
5. Придерживайтесь нейминга в соответствии с [A/HC/LC Pattern'ом](https://github.com/kettanaito/naming-cheatsheet#ahclc-pattern).  
   Имя состоит из `prefix? + action (A) + high context (HC) + low context? (LC)`:
   1. `Action` (A) — действие, которое выполняется (`Get`, `Set`, `Create`, `Remove`, etc.)
   2. `High Context` (HC) — ключевая сущность, над которой совершается действие (`User`, `Order`, etc.)
   3. `Low Context` (LC) — зависимая сущность, детализация или модификатор (`Assignments`, `ById`, `Status`, etc.)

```csharp
// ПЛОХО
public IEnumerable<Assignment> GetAssignmentsUser(Guid userId); // Нарушен порядок: LC перед HC
public void UserPasswordUpdate(User user, string password);    // Action в конце, а не в начале
public IEnumerable<Assignment> GetAssignments(Guid userId);    // Отсутствует HC — неясен контекст сущности

// ХОРОШО
// Action (Get) + HC (User) + LC (Assignments)
public IEnumerable<Assignment> GetUserAssignments(Guid userId);

// Action (Update) + HC (User) + LC (Password)
public void UpdateUserPassword(User user, string password);
```

## Префиксы

1. Для методов получения/доступа к данным используйте префиксы **`Find`** и **`Get`** в зависимости от гарантии существования результата:
   - **`Find`** — используется, когда объект может отсутствовать. Метод возвращает nullable-тип (`T?`) или `null`, если элемент не найден, и **не** бросает исключение при отсутствии данных.
   - **`Get`** — используется, когда объект гарантированно должен существовать. Метод возвращает non-nullable значение (`T`) и **обязан** выбросить исключение, если объект не найден.

```csharp
// ПЛОХО
// Get возвращает null вместо непустого значения
public Student? GetStudent(int id)
{
    return _students.FirstOrDefault(s => s.Id == id);
}

// Find бросает исключение при отсутствии сущности
public Student FindStudent(int id)
{
    return _students.Single(s => s.Id == id);
}

// ХОРОШО
// Find возвращает null, если студент не найден
public Student? FindStudent(int id)
{
    return _students.FirstOrDefault(s => s.Id == id);
}

// Get гарантированно возвращает студента либо выбрасывает исключение
public Student GetStudent(int id)
{
    Student? student = FindStudent(id);

    if (student is null)
        throw new NotFoundException($"Student with id {id} was not found.");

    return student;
}
```

2. Методы, пытающиеся выполнить действие или получение данных, которые могут завершиться неудачей без выброса исключения, должны иметь префикс `Try` и возвращать `bool` (в том числе в рамках классического .NET Try-паттерна с `out` параметром).

```csharp
// ПЛОХО: метод с префиксом Try возвращает void, статус выполнения операции неизвестен
public void TryWithdrawMoney(CreditCard creditCard, int password, double moneyToWithdraw)
{
    if (creditCard.IsPasswordCorrect(password))
        creditCard.Withdraw(moneyToWithdraw);
}

// ХОРОШО: метод возвращает bool, отражающий успешность операции
public bool TryWithdrawMoney(CreditCard creditCard, int password, double moneyToWithdraw)
{
    if (creditCard.IsPasswordCorrect(password) is false)
        return false;

    creditCard.Withdraw(moneyToWithdraw);
    return true;
}

// ХОРОШО: классический Try-паттерн с out-параметром
// [NotNullWhen(true)] указывает компилятору, что если метод вернул true, то student гарантированно не null
public bool TryFindStudent(int id, [NotNullWhen(true)] out Student? student)
{
    student = _students.FirstOrDefault(s => s.Id == id);
    return student is not null;
}
```
