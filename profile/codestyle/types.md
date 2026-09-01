# Type declaration

1. Инициализация полей должна происходить в конструкторе, а не при их объявлении. Исключение — инициализация пустых коллекций (например, `new List<T>()`) и базовых структур по умолчанию.

```csharp
// ПЛОХО: нетривиальная инициализация или создание зависимостей при объявлении поля
public class OrderService
{
    private readonly IUserRepository _userRepository = new UserRepository();
    private readonly DateTime _createdAt = DateTime.UtcNow;
}

// ХОРОШО: инициализация зависимостей в конструкторе; пустые коллекции допустимо инициализировать inline
public class OrderService
{
    private readonly List<Order> _orders = new List<Order>();
    private readonly IUserRepository _userRepository;
    private readonly DateTime _createdAt;

    public OrderService(IUserRepository userRepository, DateTime createdAt)
    {
        _userRepository = userRepository;
        _createdAt = createdAt;
    }
}
```
2. Конструкторы должны полностью инициализировать объект. Валидация аргументов должна происходить в конструкторах.
3. Минимизируйте область доступа к данным. Предпочтительней хранить информацию в приватных полях нежели в публичных свойствах. Методы, которые не нужны внешнему коду, нужно делать приватными.
4. Не оставляйте мутабельные поля для отложенной инициализации. Инициализируйте поля в конструкторах и делайте иммутабельные поля и свойства, где это уместно.
5. Не использовать поля для передачи данных внутри метода или между методами класса.

```csharp
// ПЛОХО: использование поля для передачи промежуточного состояния/данных между методами
public class OrderProcessor
{
    private decimal _discountAmount;

    public void Process(Order order)
    {
        CalculateDiscount(order);
        ApplyDiscount(order);
    }

    private void CalculateDiscount(Order order)
    {
        _discountAmount = order.Total * 0.1m;
    }

    private void ApplyDiscount(Order order)
    {
        order.Total -= _discountAmount;
    }
}

// ХОРОШО: передача данных через аргументы и возвращаемые значения методов
public class OrderProcessor
{
    public void Process(Order order)
    {
        var discountAmount = CalculateDiscount(order);
        ApplyDiscount(order, discountAmount);
    }

    private decimal CalculateDiscount(Order order)
    {
        return order.Total * 0.1m;
    }

    private void ApplyDiscount(Order order, decimal discountAmount)
    {
        order.Total -= discountAmount;
    }
}
```
6. Поддерживайте инвариант типа. Если у типа есть несколько полей, которые между собой связаны, то не должно быть способа изменить одно из полей и нарушить связь между ними.
7. Члены класса должны располагаться [в следующем порядке](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1201.md):
   1. Константы
   2. Поля
   3. Конструкторы и Create-методы
   4. Свойства
   5. Публичные методы
   6. Приватные методы
8. Нумерация значений енама должна начинаться с 1. 0 должен быть использован для неопределённых значений.
9. Не используйте приватные свойства.
10. Не используйте публичные поля.
11. Не используйте оператор `==` для сравнения не числовых типов. Не переопределяйте оператор `==` для не числовых типов.
12. Не используйте наследование для переиспользования логики. Если объект наследуется, то справедливым должно быть высказывание, что производный объект является базовым (см. LSP).
