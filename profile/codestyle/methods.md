# Method declaration

1. Метод, возвращающий коллекцию, в случае отсутствия элементов для возврата, должен возвращать пустую коллекцию, а не
   null.

```csharp
// ПЛОХО
public List<Student>? FindStudents(int course)
{
    List<Student>? students = null;

    /* ищем студентов любым возможным методом */

    // если студенты не нашлись, возвращаем null
    if (students is null || students.Count == 0)
        return null;

    // возвращаем студентов, если хоть кто-то нашёлся
    return students;
}

// ХОРОШО
public List<Student> FindStudents(int course)
{
    // создаём пустой лист
    var students = new List<Student>();

    /* ищем студентов любым возможным методом */

    // возвращаем студентов даже, если это пустой лист (не null)
    return students;
}
```

2. Метод, который работает с пользовательскими аргументами, должен валидировать их.

```csharp
// ПЛОХО
public void FindStudentByFullName(string name, string surname)
{
    /* поиск студента без проверки входных данных */
}

// ХОРОШО
public void FindStudentByFullName(string name, string surname)
{
    ArgumentException.ThrowIfNullOrWhiteSpace(name);
    ArgumentException.ThrowIfNullOrWhiteSpace(surname);

    /* поиск студента после проверки входных данных */
}
```

3. В конструкторе должен соблюдаться порядок инициализации:
    1. Валидация аргументов
    2. Инициализация, которая не зависит от аргументов
    3. Инициализация полей аргументами
    4. Инициализация, которая требует какой-то логики, вызовов методов

```csharp
// ХОРОШО
public MegaFaculty(string facultyName)
{
    // валидация
    ArgumentException.ThrowIfNullOrWhiteSpace(facultyName);

    // инициализация, не зависящая от аргументов
    _courses = new List<OgnpCourse>();

    // инициализация полей аргументами
    Name = facultyName;

    /* сложная инициализация с вызовом различных методов */
    NotifyISU(this);
}
```