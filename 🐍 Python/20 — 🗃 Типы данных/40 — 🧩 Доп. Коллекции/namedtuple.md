## Пример для быстрой справки

```python
from collections import namedtuple

# Создаем класс Point с полями x и y
Point = namedtuple('Point', ['x', 'y'])

# Создаем экземпляр
p = Point(3, 7)

print(p)           # Point(x=3, y=7)
print(p.x, p.y)    # 3 7
print(p[0], p[1])  # 3 7
```

Все примеры в таблице ниже основаны на этом коде ⬇️

## Быстрая справка

|Операция                 |Синтаксис                                         |Описание                          |
|-------------------------|--------------------------------------------------|----------------------------------|
|**Импорт**               |`from collections import namedtuple`              |Подключение функции               |
|**Создание типа**        |`Point = namedtuple('Point', ['x', 'y'])`         |Создание класса-шаблона           |
|**Создание экземпляра**  |`p = Point(3, 7)`                                 |Позиционные аргументы             |
|**Именованные аргументы**|`p = Point(x=3, y=7)`                             |С указанием имен полей            |
|**Доступ по имени**      |`p.x`, `p.y`                                      |Через точечную нотацию            |
|**Доступ по индексу**    |`p[0]`, `p[1]`                                    |Как в обычном кортеже             |
|**Распаковка**           |`x, y = p`                                        |Как обычный кортеж                |
|**Изменение полей**      |`p2 = p._replace(x=10)`                           |Создает новый экземпляр           |
|**Преобразование в dict**|`p._asdict()`                                     |Возвращает OrderedDict            |
|**Список полей**         |`Point._fields`                                   |Кортеж с именами полей            |
|**Defaults полей**       |`Point._field_defaults`                           |Словарь со значениями по умолчанию|
|**Создание из списка**   |`Point._make([3, 7])`                             |Создание из итерируемого объекта  |
|**Значения по умолчанию**|`namedtuple('Point', ['x', 'y'], defaults=(0, 0))`|Python 3.7+                       |

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

## Встроенные методы и атрибуты

### Метод `_replace()` — изменение полей

Создает **новый** экземпляр с измененными значениями (исходный остается неизменным):

```python
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'city'])
person = Person('Иван', 25, 'Москва')

# Создаем новый экземпляр с измененным возрастом
person_older = person._replace(age=26)

print(person)        # Person(name='Иван', age=25, city='Москва')
print(person_older)  # Person(name='Иван', age=26, city='Москва')

# Можно изменить несколько полей
person_new = person._replace(age=30, city='Санкт-Петербург')
print(person_new)    # Person(name='Иван', age=30, city='Санкт-Петербург')
```

**Практическое применение — инкремент значения:**

```python
Pet = namedtuple('Pet', ['type', 'name', 'age'])
frank = Pet(type='pigeon', name='Френк', age=3)

# Френк стареет
frank = frank._replace(age=frank.age + 1)
print(frank)  # Pet(type='pigeon', name='Френк', age=4)
```

### Метод `_asdict()` — преобразование в словарь

Возвращает `OrderedDict` (до Python 3.8) или обычный `dict` (Python 3.8+):

```python
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'city'])
person = Person('Мария', 30, 'Казань')

# Преобразование в словарь
person_dict = person._asdict()
print(person_dict)
# OrderedDict([('name', 'Мария'), ('age', 30), ('city', 'Казань')])

# Теперь можно изменять
person_dict['age'] = 31
print(person_dict)
# OrderedDict([('name', 'Мария'), ('age', 31), ('city', 'Казань')])
```

**Практическое применение — сериализация в JSON:**

```python
import json
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'city'])
person = Person('Алексей', 28, 'Новосибирск')

# Преобразование в JSON
json_data = json.dumps(person._asdict())
print(json_data)  # {"name": "Алексей", "age": 28, "city": "Новосибирск"}
```

### Атрибут `_fields` — список полей

Возвращает кортеж с именами всех полей:

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y', 'z'])

print(Point._fields)  # ('x', 'y', 'z')
print(type(Point._fields))  # <class 'tuple'>

# Проверка наличия поля
if 'x' in Point._fields:
    print("Поле x существует")

# Итерация по полям
for field in Point._fields:
    print(field)  # x, y, z
```

**Практическое применение — динамическое создание:**

```python
from collections import namedtuple

# Создание одного типа на основе другого
Point2D = namedtuple('Point2D', ['x', 'y'])
Point3D = namedtuple('Point3D', Point2D._fields + ('z',))

print(Point3D._fields)  # ('x', 'y', 'z')
```

**Итерация по полям и значениям:**

```python
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])
timur = Person('Тимур', 29, 170)

# Используем zip для итерации
for field, value in zip(Person._fields, timur):
    print(f'{field} -> {value}')

# Вывод:
# name -> Тимур
# age -> 29
# height -> 170
```

### Атрибут `_field_defaults` — значения по умолчанию

Словарь, который показывает, какие поля имеют значения по умолчанию:

```python
from collections import namedtuple

# С значениями по умолчанию
Person = namedtuple('Person', ['name', 'age', 'country'], defaults=['Russia'])

print(Person._field_defaults)  # {'country': 'Russia'}

timur = Person('Тимур', 29)  # country будет 'Russia'
print(timur)  # Person(name='Тимур', age=29, country='Russia')

# Без значений по умолчанию
Point = namedtuple('Point', ['x', 'y'])
print(Point._field_defaults)  # {}
```

**Практическое применение — проверка обязательных полей:**

```python
from collections import namedtuple

Config = namedtuple('Config', ['host', 'port', 'debug', 'timeout'], 
                    defaults=(False, 30))

print(Config._field_defaults)  # {'debug': False, 'timeout': 30}

# Определяем, какие поля обязательные
required_fields = [f for f in Config._fields if f not in Config._field_defaults]
print(f"Обязательные поля: {required_fields}")  # ['host', 'port']

optional_fields = list(Config._field_defaults.keys())
print(f"Необязательные поля: {optional_fields}")  # ['debug', 'timeout']
```

### Метод `_make()` — создание из итерируемого объекта

Создает экземпляр из списка, кортежа или другой итерируемой последовательности:

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])

# Обычное создание
p1 = Point(3, 7)

# Создание из списка
data = [10, 20]
p2 = Point._make(data)
print(p2)  # Point(x=10, y=20)

# Создание из кортежа
p3 = Point._make((5, 15))
print(p3)  # Point(x=5, y=15)
```

**Практическое применение — чтение из CSV:**

```python
import csv
from collections import namedtuple

Employee = namedtuple('Employee', ['name', 'age', 'salary'])

with open('employees.csv', 'r') as file:
    reader = csv.reader(file)
    next(reader)  # Пропускаем заголовок
    
    employees = [Employee._make(row) for row in reader]
    
for emp in employees:
    print(f"{emp.name}: {emp.salary}")
```

### Все специальные методы и атрибуты

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'], defaults=(0,))
point = Point(3, 7)

# Атрибуты
Point._fields          # ('x', 'y') - кортеж имен полей
Point._field_defaults  # {'y': 0} - словарь со значениями по умолчанию

# Методы
point._asdict()        # dict - преобразование в словарь
point._replace(x=10)   # Point(x=10, y=7) - создание с измененными полями
Point._make([5, 15])   # Point(x=5, y=15) - создание из итерируемого объекта
```

## Приемы и паттерны

### Прототип для значений по умолчанию (Python < 3.7)

Если `defaults` недоступен, используйте прототип:

```python
from collections import namedtuple

Pet = namedtuple('Pet', ['type', 'name', 'alt_name'])

# Создаем прототип с дефолтными значениями
default_pet = Pet(type=None, name=None, alt_name='нет')

# Используем _replace для создания новых экземпляров
frank = default_pet._replace(type='pigeon', name='Френк')
print(frank)  # Pet(type='pigeon', name='Френк', alt_name='нет')

claire = default_pet._replace(type='fox', name='Клер')
print(claire)  # Pet(type='fox', name='Клер', alt_name='нет')
```

### Обновление нескольких экземпляров

```python
from collections import namedtuple

Product = namedtuple('Product', ['name', 'price', 'quantity'])

products = [
    Product('Яблоко', 50, 100),
    Product('Банан', 60, 80),
    Product('Апельсин', 70, 120)
]

# Повышаем все цены на 10%
updated_products = [p._replace(price=int(p.price * 1.1)) for p in products]

for p in updated_products:
    print(f"{p.name}: {p.price}")
# Яблоко: 55
# Банан: 66
# Апельсин: 77
```

### Копирование с частичными изменениями

```python
from collections import namedtuple

Config = namedtuple('Config', ['host', 'port', 'debug', 'timeout'])

# Базовая конфигурация
base_config = Config('localhost', 8080, False, 30)

# Конфигурация для разработки
dev_config = base_config._replace(debug=True, timeout=60)

# Конфигурация для продакшена
prod_config = base_config._replace(host='prod.example.com', port=443)

print(dev_config)   # Config(host='localhost', port=8080, debug=True, timeout=60)
print(prod_config)  # Config(host='prod.example.com', port=443, debug=False, timeout=30)
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

|Структура     |Изменяемость|Доступ по имени|Память|Скорость |
|--------------|------------|---------------|------|---------|
|**dict**      |Изменяемый  |✅              |Больше|Медленнее|
|**class**     |Изменяемый  |✅              |Больше|Медленнее|
|**tuple**     |Неизменяемый|❌              |Меньше|Быстрее  |
|**namedtuple**|Неизменяемый|✅              |Меньше|Быстрее  |

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