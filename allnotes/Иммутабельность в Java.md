Иммутабельность — это концепция, при которой объект не может быть изменен после создания. Это обеспечивает безопасность, предсказуемость и упрощает многопоточность (поскольку нет риска гонки данных). В Java есть несколько способов добиться иммутабельности. Давайте разберём основные подходы с примерами. Я сосредоточусь на Java, но упомяну аналогии с другими языками.

#### 1. **Использование `final` полей и отсутствие сеттеров**
   - **Принцип:** Делайте все поля `final`, чтобы они могли быть установлены только в конструкторе. Не предоставляйте методы-модификаторы (сеттеры).
   - **Преимущества:** Простой и надёжный способ. Компилятор предотвращает изменения.
   - **Пример:**
     ```java
     public final class ImmutablePoint {
         private final int x;
         private final int y;
         
         public ImmutablePoint(int x, int y) {
             this.x = x;
             this.y = y;
         }
         
         public int getX() { return x; }
         public int getY() { return y; }
         // Нет сеттеров
     }
     ```
   - **Недостатки:** Если поле — ссылка на мутируемый объект (например, List), нужно использовать "defensive copying" (смотрите способ 3).

#### 2. **Использование records (с JDK 14+)**
   - **Принцип:** Records автоматически создают иммутабельные структуры с финализированными полями, геттерами и методами `equals()`, `hashCode()`, `toString()`. Нет сеттеров.
   - **Преимущества:** Минимум boilerplate-кода. Идеально для DTO, ключей и структур данных.
   - **Пример:**
     ```java
     public record ImmutableUser(String name, int age) {
         // Можно добавить валидацию в конструкторе
         public ImmutableUser {
             if (age < 0) throw new IllegalArgumentException("Age cannot be negative");
         }
         // Кастомные методы возможны, но поля immutable
         public String greeting() { return "Hello, " + name; }
     }
     ```
   - **Аналогии:** Data classes в Kotlin или case classes в Scala.

#### 3. **Defensive copying (защитное копирование)**
   - **Принцип:** Если объект содержит мутируемые поля (например, массивы или коллекции), создавайте глубокие копии при передаче данных. Копируйте в конструкторе и геттерах.
   - **Преимущества:** Предотвращает изменение объекта через ссылки.
   - **Пример:**
     ```java
     public final class ImmutableListWrapper {
         private final List<String> items;
         
         public ImmutableListWrapper(List<String> items) {
             this.items = List.copyOf(items); // Создаёт immutable копию
         }
         
         public List<String> getItems() {
             return List.copyOf(items); // Возвращает копию
         }
     }
     ```
   - **В JDK 10+:** Используйте `List.of()`, `Set.of()`, `Map.of()` для создания immutable коллекций.


