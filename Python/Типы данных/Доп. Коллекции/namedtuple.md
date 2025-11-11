
## Быстрая справка

|Операция|Синтаксис|Описание|
|---|---|---|
|**Импорт**|`from collections import namedtuple`|Подключение функции|
|**Создание типа**|`Point = namedtuple('Point', ['x', 'y'])`|Создание класса-шаблона|
|**Создание экземпляра**|`p = Point(3, 7)`|Позиционные аргументы|
|**Именованные аргументы**|`p = Point(x=3, y=7)`|С указанием имен полей|
|**Доступ по имени**|`p.x`, `p.y`|Через точечную нотацию|
|**Доступ по индексу**|`p[0]`, `p[1]`|Как в обычном кортеже|
|**Распаковка**|`x, y = p`|Как обычный кортеж|
|**Значения по умолчанию**|`namedtuple('Point', ['x', 'y'], defaults=(0, 0))`|Python 3.7+|

## Что такое namedtuple?

**namedtuple** — это фабричная функция, которая создает новый класс (тип данных), являющийся подтипом обычного кортежа с именованными полями.

### Основная идея

```python
from collections import namedtuple

# Point — это КЛАСС (шаблон, фабрика)
Point = namedtuple('Point', ['x', 'y'])

# point — это ЭКЗЕМПЛЯР класса Point
point = Point(3, 7)

print(type(Point))   # <class 'type'>
print(type(point))   # <class '__main__.Point'>
```

**Важно понимать:**

- `Point` (с большой буквы) — это класс, фабрика для создания точек
- `point` (с маленькой буквы) — это конкретный экземпляр с данными
- Имя класса задается первым аргументом `'Point'`, а не названием переменной

## Зачем нужны namedtuple?

### ❌ Без namedtuple (плохо)

```python
point = (3, 7)
print(point[0])  # 3 — что это? x или y?
print(point[1])  # 7 — неочевидно
```

### ✅ С namedtuple (хорошо)

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
point = Point(3, 7)

print(point.x)   # 3 — понятно, что это x
print(point.y)   # 7 — понятно, что это y
```

**Преимущества:**

- 📖 Читаемость кода
- 🔍 Самодокументируемость
- 🛡️ Неизменяемость (как у tuple)
- 💾 Экономия памяти (по сравнению с классами)

## Создание именованных кортежей

### Способы задания полей

```python
from collections import namedtuple

# 1. Список (рекомендуется)
Point = namedtuple('Point', ['x', 'y'])

# 2. Кортеж
Point = namedtuple('Point', ('x', 'y'))

# 3. Строка через пробел
Point = namedtuple('Point', 'x y')

# 4. Строка через запятую
Point = namedtuple('Point', 'x,y')

# 5. Словарь (используются только ключи)
Point = namedtuple('Point', {'x': 0, 'y': 0})

# 6. Множество (НЕ рекомендуется — порядок не гарантирован!)
Point = namedtuple('Point', {'x', 'y'})  # может быть Point(x, y) или Point(y, x)
```

### Способы создания экземпляров

```python
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'city'])

# Позиционные аргументы
p1 = Person('Иван', 25, 'Москва')

# Именованные аргументы
p2 = Person(name='Мария', age=30, city='Санкт-Петербург')

# Смешанные аргументы
p3 = Person('Петр', age=28, city='Казань')

print(p1)  # Person(name='Иван', age=25, city='Москва')
```

## Доступ к данным

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
point = Point(10, 20)

# Доступ по имени (основной способ)
print(point.x)      # 10
print(point.y)      # 20

# Доступ по индексу (как у tuple)
print(point[0])     # 10
print(point[1])     # 20

# Распаковка
x, y = point
print(x, y)         # 10 20

# Итерация
for value in point:
    print(value)    # 10, затем 20

# Срезы (результат — обычный tuple)
print(point[:1])    # (10,)
print(point[1:])    # (20,)
```

## Параметры функции namedtuple()

### Сигнатура

```python
namedtuple(typename, field_names, *, rename=False, defaults=None, module=None)
```

### Параметр `rename=True`

Автоматически переименовывает некорректные поля:

```python
from collections import namedtuple

# Без rename — ошибка (class — ключевое слово)
# Student = namedtuple('Student', ['name', 'age', 'class'])  # ValueError

# С rename — автоматическое переименование
Student = namedtuple('Student', ['name', 'age', 'class'], rename=True)
student = Student('Иван', 20, '10А')

print(student)  # Student(name='Иван', age=20, _2='10А')
# 'class' стало '_2' (по индексу)
```

**Когда переименовываются поля:**

- Начинаются с подчеркивания: `_name` → `_0`
- Являются ключевыми словами: `class`, `if`, `for` → `_N`
- Дублируются: `['x', 'y', 'x']` → `['x', 'y', '_2']`

### Параметр `defaults` (Python 3.7+)

Устанавливает значения по умолчанию **с конца**:

```python
from collections import namedtuple

# Значения по умолчанию для всех полей
Point = namedtuple('Point', ['x', 'y'], defaults=(0, 0))
p1 = Point()           # Point(x=0, y=0)
p2 = Point(5)          # Point(x=5, y=0)
p3 = Point(5, 10)      # Point(x=5, y=10)

# Значение по умолчанию только для последнего поля
Person = namedtuple('Person', ['name', 'age', 'city'], defaults=('Неизвестно',))
person = Person('Иван', 25)  # Person(name='Иван', age=25, city='Неизвестно')
```

### Параметр `module`

Изменяет отображение модуля класса:

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'], module='geometry')
point = Point(1, 2)

print(type(point))  # <class 'geometry.Point'>
```

## Важные особенности

### 1. Неизменяемость

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
point = Point(3, 7)

# ❌ Нельзя изменить значение
# point.x = 10  # AttributeError

# ✅ Можно создать новый экземпляр
point = Point(10, 7)
```

### 2. Изменяемые значения внутри

```python
from collections import namedtuple

Person = namedtuple('Person', ['name', 'children'])
person = Person('Света', ['Лариса', 'Тимур'])

# ✅ Можно изменять изменяемый объект ВНУТРИ
person.children.append('Сослан')
print(person)  # Person(name='Света', children=['Лариса', 'Тимур', 'Сослан'])

# ❌ Нельзя заменить сам список
# person.children = ['Новый список']  # AttributeError
```

**Важно:** Кортежи с изменяемыми объектами **не хешируемы** и не могут быть:

- Элементами множеств
- Ключами словарей

### 3. Наследование от tuple

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
point = Point(3, 7)

# Все методы tuple доступны
print(isinstance(point, tuple))  # True
print(len(point))                # 2
print(point.count(3))            # 1
print(point.index(7))            # 1
```

## Практические примеры

### Пример 1: Работа с точками

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])

# Создание точек
p1 = Point(0, 0)
p2 = Point(3, 4)

# Вычисление расстояния
distance = ((p2.x - p1.x)**2 + (p2.y - p1.y)**2)**0.5
print(f"Расстояние: {distance}")  # 5.0

# Список точек
points = [Point(1, 2), Point(3, 4), Point(5, 6)]
for p in points:
    print(f"Точка ({p.x}, {p.y})")
```

### Пример 2: Чтение из CSV

```python
import csv
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])

# Чтение точек из файла
points = []
with open('points.csv', 'r', encoding='utf-8') as file:
    reader = csv.reader(file)
    for row in reader:
        x, y = row
        points.append(Point(int(x), int(y)))

print(points)
# [Point(x=10, y=20), Point(x=30, y=40), ...]
```

### Пример 3: Структура данных студента

```python
from collections import namedtuple

Student = namedtuple('Student', ['name', 'age', 'grade'], defaults=(0,))

# Создание студентов
students = [
    Student('Иван', 20, 85),
    Student('Мария', 19, 92),
    Student('Петр', 21),  # grade будет 0
]

# Сортировка по оценкам
sorted_students = sorted(students, key=lambda s: s.grade, reverse=True)

for student in sorted_students:
    print(f"{student.name}: {student.grade}")
```

### Пример 4: Возврат нескольких значений

```python
from collections import namedtuple

Result = namedtuple('Result', ['min', 'max', 'avg'])

def analyze_numbers(numbers):
    """Возвращает min, max и среднее"""
    return Result(
        min=min(numbers),
        max=max(numbers),
        avg=sum(numbers) / len(numbers)
    )

# Использование
data = [10, 20, 30, 40, 50]
stats = analyze_numbers(data)

print(f"Минимум: {stats.min}")      # 10
print(f"Максимум: {stats.max}")      # 50
print(f"Среднее: {stats.avg}")       # 30.0
```

### Пример 5: RGB цвета

```python
from collections import namedtuple

Color = namedtuple('Color', ['red', 'green', 'blue'], defaults=(0, 0, 0))

# Предопределенные цвета
RED = Color(255, 0, 0)
GREEN = Color(0, 255, 0)
BLUE = Color(0, 0, 255)
BLACK = Color()  # (0, 0, 0) благодаря defaults

print(RED)    # Color(red=255, green=0, blue=0)
print(BLACK)  # Color(red=0, green=0, blue=0)

# Работа с цветами
def lighten(color, amount=50):
    return Color(
        min(255, color.red + amount),
        min(255, color.green + amount),
        min(255, color.blue + amount)
    )

light_red = lighten(RED)
print(light_red)  # Color(red=255, green=50, blue=50)
```

## Сравнение namedtuple с другими структурами

|Структура|Изменяемость|Доступ по имени|Память|Скорость|
|---|---|---|---|---|
|**dict**|Изменяемый|✅|Больше|Медленнее|
|**class**|Изменяемый|✅|Больше|Медленнее|
|**tuple**|Неизменяемый|❌|Меньше|Быстрее|
|**namedtuple**|Неизменяемый|✅|Меньше|Быстрее|

## Распространенные ошибки

### ❌ Ошибка 1: Неправильное название переменной

```python
# Плохо (запутанно)
unknowntype = namedtuple('Point', ['x', 'y'])
obj = unknowntype(1, 2)
print(type(obj))  # <class '__main__.Point'> — имя класса всё равно 'Point'

# Хорошо (понятно)
Point = namedtuple('Point', ['x', 'y'])
point = Point(1, 2)
```

### ❌ Ошибка 2: Попытка изменить значение

```python
Point = namedtuple('Point', ['x', 'y'])
point = Point(1, 2)

# Ошибка!
# point.x = 10  # AttributeError
```

### ❌ Ошибка 3: Использование ключевых слов

```python
# Ошибка!
# Student = namedtuple('Student', ['name', 'class'])  # ValueError

# Правильно — используйте rename
Student = namedtuple('Student', ['name', 'class'], rename=True)
```

### ❌ Ошибка 4: Множество как field_names

```python
# Плохо — порядок полей не гарантирован
Point = namedtuple('Point', {'x', 'y'})
# Может быть Point(x, y) или Point(y, x)

# Хорошо — используйте список
Point = namedtuple('Point', ['x', 'y'])
```

## Когда использовать namedtuple?

### ✅ Используйте когда:

- Нужна неизменяемая структура данных
- Требуется читаемый код
- Много мелких объектов (экономия памяти)
- Нужна совместимость с tuple
- Простая структура без методов

### ❌ Не используйте когда:

- Нужна изменяемость → используйте `dict` или `class`
- Нужны методы и логика → используйте `class` или `dataclass`
- Значения по умолчанию сложные → используйте `dataclass` (Python 3.7+)
- Нужна валидация → используйте `class` с `@property`

## Полезные ссылки

- [Официальная документация](https://docs.python.org/3/library/collections.html#collections.namedtuple)
- [PEP 3132](https://www.python.org/dev/peps/pep-3132/) — Extended Iterable Unpacking
- [Исходный код namedtuple](https://github.com/python/cpython/blob/main/Lib/collections/__init__.py)