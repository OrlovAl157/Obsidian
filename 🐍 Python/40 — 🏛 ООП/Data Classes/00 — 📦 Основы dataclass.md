---
tags:
  - python
  - тема/классы
  - тема/ооп
  - тема/dataclass
  - статус/завершён
уровень: начинающий
---

# 📦 Основы dataclass

## ⚡ БЫСТРАЯ СПРАВКА

### Основные параметры и приёмы

| Что нужно | Код | Описание |
|---|---|---|
| **Создать простой dataclass** | `@dataclass`<br>`class Person:`<br>`    name: str` | Автоматически создаёт `__init__`, `__repr__`, `__eq__` |
| **Значение по умолчанию** | `age: int = 30` | Простое значение по умолчанию |
| **Изменяемый тип (список)** | `friends: list = field(default_factory=list)` | ОБЯЗАТЕЛЬНО для списков/словарей! |
| **Неизменяемый объект** | `@dataclass(frozen=True)` | Объект нельзя менять после создания |
| **Сравнение (<, >, и т.д.)** | `@dataclass(order=True)` | Добавляет методы `<`, `>`, `<=`, `>=` |
| **Скрыть поле из `__repr__`** | `field(repr=False)` | Поле не выводится при `print()` |
| **Исключить из сравнения** | `field(compare=False)` | Поле не учитывается в `==` |
| **Вычисляемое поле** | `field(init=False)` + `__post_init__` | Поле вычисляется, не передаётся в конструктор |
| **Вывести как кортеж** | `astuple(obj)` | Преобразование в кортеж |
| **Вывести как словарь** | `asdict(obj)` | Преобразование в словарь |

---

## 🎯 СРАВНЕНИЕ: dataclass vs обычный класс

### ❌ БЕЗ dataclass (15 строк кода)

```python
class Person:
    def __init__(self, name, surname, age):
        self.name = name
        self.surname = surname
        self.age = age
    
    def __repr__(self):
        return f"Person(name={self.name!r}, surname={self.surname!r}, age={self.age!r})"
    
    def __eq__(self, other):
        if not isinstance(other, Person):
            return NotImplemented
        return (self.name == other.name and 
                self.surname == other.surname and 
                self.age == other.age)
```

### ✅ С dataclass (3 строки кода)

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    surname: str
    age: int
```

**Экономия: 12 строк кода!** 🎉

---

## 💡 БАЗОВЫЕ КОНЦЕПЦИИ

### Что это?

**Data Classes** — декоратор `@dataclass`, который автоматически генерирует стандартные методы для классов, предназначенных для хранения данных.

### Три автоматических метода

- **`__init__`** — конструктор с параметрами
- **`__repr__`** — красивое представление объекта
- **`__eq__`** — сравнение по содержимому

### ⚠️ ВАЖНО: Аннотация типов обязательна!

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    age = 30  # ← БЕЗ аннотации!

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    age: int = 30  # ← с аннотацией!
```

---

## 🚀 ОСНОВНЫЕ ВОЗМОЖНОСТИ

### Простое создание

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    surname: str
    age: int

person = Person('Гвидо', 'ван Россум', 67)
print(person.name)      # → Гвидо
print(person.surname)   # → ван Россум
print(person.age)       # → 67
```

### Значения по умолчанию

```python
@dataclass
class Person:
    name: str
    surname: str
    age: int = None

person1 = Person('Гвидо', 'ван Россум')
person2 = Person('Илон', 'Маск', 51)

print(person1)  # → Person(name='Гвидо', surname='ван Россум', age=None)
print(person2)  # → Person(name='Илон', surname='Маск', age=51)
```

### ⚠️ ПРАВИЛО: обязательные поля ПЕРЕД опциональными

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    name: str = None      # ← с default
    age: int              # ❌ ОШИБКА: обязательное ПОСЛЕ опционального

# TypeError: non-default argument 'age' follows default argument

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    name: str             # обязательное
    age: int = 30         # опциональное (после обязательного)
```

### `frozen=True` — неизменяемость

```python
@dataclass(frozen=True)
class ImmutablePoint:
    x: float
    y: float

p = ImmutablePoint(1, 2)
p.x = 3  # ❌ FrozenInstanceError: cannot assign to field 'x'
```

### `order=True` — методы сравнения

```python
@dataclass(order=True)
class Person:
    name: str
    age: int

p1 = Person('Гвидо', 67)
p2 = Person('Илон', 51)

print(p1 > p2)   # → False (сначала сравнивает name)
print(p1 < p2)   # → True
```

---

## 🔧 РАБОТА С ПОЛЯМИ: field()

### ❌ ПРОБЛЕМА: изменяемые типы как default

```python
@dataclass
class Team:
    members: list = []  # ❌ ОШИБКА!

# ValueError: mutable default <class 'list'> for field members is not allowed
```

**Почему ошибка?** Значение по умолчанию создаётся ОДИН РАЗ при определении класса. Все экземпляры будут **разделять ОДИН список**!

### ✅ РЕШЕНИЕ: `field(default_factory=...)`

```python
from dataclasses import dataclass, field

@dataclass
class Team:
    members: list = field(default_factory=list)

t1 = Team()
t2 = Team()
t1.members.append('Alice')

print(t1.members)  # → ['Alice']
print(t2.members)  # → [] ✅ Пусто, как надо!
```

**Как работает:** При создании каждого объекта вызывается `list()` и создаётся **новый** список.

### Параметры `field()`

#### `default=...` для простых значений

```python
from dataclasses import dataclass, field

@dataclass
class Person:
    name: str
    age: int = field(default=0)  # то же самое, что = 0

person = Person('Гвидо')
print(person.age)  # → 0
```

#### `repr=False` — скрыть из `__repr__`

```python
@dataclass
class User:
    name: str
    password: str = field(repr=False)  # не выводим пароль

user = User('Alice', 'secret123')
print(user)  # → User(name='Alice')
print(user.password)  # → secret123 (есть, но не выводится)
```

#### `compare=False` — исключить из сравнения

```python
@dataclass
class Person:
    name: str
    age: int = field(compare=False)

p1 = Person('Гвидо', 67)
p2 = Person('Гвидо', 68)

print(p1 == p2)  # → True (age не сравнивается)
```

#### `init=False` — исключить из конструктора

```python
@dataclass
class Person:
    name: str
    age: int = field(init=False)

person = Person('Гвидо')  # ✅ Без age
print(hasattr(person, 'age'))  # → False (атрибута нет)
```

**С default:**
```python
@dataclass
class Person:
    name: str
    age: int = field(init=False, default=0)

person = Person('Гвидо')
print(person.age)  # → 0 (есть default)
```

---

## 📝 ТИПИЗАЦИЯ И HINTS

### `typing.Any` — если тип неизвестен

```python
from dataclasses import dataclass
from typing import Any

@dataclass
class Container:
    name: str
    data: Any  # может быть что угодно

container = Container('config', {'key': 'value'})
print(container)  # → Container(name='config', data={'key': 'value'})

container2 = Container('numbers', [1, 2, 3])
print(container2)  # → Container(name='numbers', data=[1, 2, 3])
```

---

## 🔄 РАСШИРЕННАЯ ИНИЦИАЛИЗАЦИЯ: __post_init__

### Вычисляемые атрибуты

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    surname: str
    fullname: str = None
    
    def __post_init__(self):
        self.fullname = self.name + ' ' + self.surname

person = Person('Гвидо', 'ван Россум')
print(person.fullname)  # → Гвидо ван Россум
```

### С `init=False` — не передавать в конструктор

```python
from dataclasses import dataclass, field

@dataclass
class Person:
    name: str
    surname: str
    fullname: str = field(init=False)
    
    def __post_init__(self):
        self.fullname = self.name + ' ' + self.surname

person = Person('Гвидо', 'ван Россум')
print(person.fullname)  # → Гвидо ван Россум
```

### `InitVar` — параметры только для `__post_init__`

```python
from dataclasses import dataclass, InitVar

@dataclass
class Rectangle:
    width: float
    height: float
    unit: InitVar[str] = 'cm'
    
    def __post_init__(self, unit):
        print(f"Прямоугольник в {unit}")

r = Rectangle(10, 20, 'mm')
print(hasattr(r, 'unit'))  # → False (нет атрибута)
```

---

## 👨‍👧 НАСЛЕДОВАНИЕ

### Порядок полей при наследовании

```python
@dataclass
class Animal:
    name: str
    age: int

@dataclass
class Dog(Animal):
    breed: str

d = Dog('Rex', 5, 'Лабрадор')
print(d)  # → Dog(name='Rex', age=5, breed='Лабрадор')
```

### ⚠️ Поля с defaults идут в конце

```python
@dataclass
class Animal:
    name: str
    age: int = 0

@dataclass
class Dog(Animal):
    breed: str  # ❌ ОШИБКА! Обязательное поле после опционального

# ✅ ПРАВИЛЬНО:
@dataclass
class Dog(Animal):
    breed: str = "Unknown"
```

### Переопределение `__post_init__` в наследнике

```python
@dataclass
class Animal:
    name: str
    
    def __post_init__(self):
        print(f"Создано животное {self.name}")

@dataclass
class Dog(Animal):
    breed: str
    
    def __post_init__(self):
        super().__post_init__()  # ← Вызовем родителя!
        print(f"Порода: {self.breed}")

d = Dog('Rex', 'Лабрадор')
# Вывод:
# Создано животное Rex
# Порода: Лабрадор
```

---

## 🎲 ДИНАМИЧЕСКОЕ СОЗДАНИЕ: make_dataclass()

### Простое создание

```python
from dataclasses import make_dataclass

Person = make_dataclass('Person', [
    ('name', str),
    ('surname', str),
    ('age', int),
])

person = Person('Гвидо', 'ван Россум', 67)
print(person)  # → Person(name='Гвидо', surname='ван Россум', age=67)
```

### С значениями по умолчанию

```python
from dataclasses import make_dataclass, field

Person = make_dataclass('Person', [
    ('name', str),
    ('surname', str),
    ('age', int, field(default=0)),
    ('tags', list, field(default_factory=list)),
])

person = Person('Alice', 'Smith')
print(person)  # → Person(name='Alice', surname='Smith', age=0, tags=[])
```

### С параметрами и методами

```python
Person = make_dataclass('Person', 
    [('name', str), ('age', int)],
    frozen=True,
    namespace={
        'greet': lambda self: f"Hello, {self.name}!"
    }
)

person = Person('Гвидо', 67)
print(person.greet())  # → Hello, Гвидо!
```

### Три формата полей в make_dataclass()

```python
Person = make_dataclass('Person', [
    'name',                          # формат 1: только имя (Any)
    ('age', int),                    # формат 2: имя и тип
    ('email', str, field(default='')), # формат 3: имя, тип и field()
])
```

---

## 🛠️ УТИЛИТЫ: astuple() и asdict()

### Преобразование в кортеж

```python
from dataclasses import dataclass, astuple

@dataclass
class Person:
    name: str
    surname: str
    age: int

person = Person('Гвидо', 'ван Россум', 67)
print(astuple(person))  # → ('Гвидо', 'ван Россум', 67)
```

### Преобразование в словарь

```python
from dataclasses import dataclass, asdict

person = Person('Гвидо', 'ван Россум', 67)
print(asdict(person))  
# → {'name': 'Гвидо', 'surname': 'ван Россум', 'age': 67}
```

---

## 🎯 КОГДА ИСПОЛЬЗОВАТЬ dataclass

### ✅ ИСПОЛЬЗУЙ dataclass

- Простое хранилище данных (API response, конфиг, DTO)
- Много атрибутов, простая логика
- Нужна стандартная функциональность (`__init__`, `__repr__`, `__eq__`)
- Конфиги и настройки
- Кэширование результатов

### ❌ НЕ используй dataclass

- Сложная логика класса (много методов)
- Часто переписываешь `__init__`
- Сложное наследование
- Нужна валидация → используй `pydantic`
- Нужна гибкость → используй `attrs` или обычный класс

---

## ✅ Ключевые правила

✅ Аннотации типов ОБЯЗАТЕЛЬНЫ  
✅ Обязательные поля ПЕРЕД опциональными  
✅ Для изменяемых типов используй `field(default_factory=...)`  
✅ `__post_init__` вызывается ПОСЛЕ `__init__`  
✅ `init=False` исключает поле из конструктора  
✅ `frozen=True` делает объекты неизменяемыми  
✅ `order=True` добавляет методы сравнения  
✅ При наследовании вызови `super().__post_init__()` если оверрайдишь  

---

## 🔗 Связанные темы

- [[01 — 🚀 Продвинутые возможности dataclass]]
- [[02 — 🔧 Как dataclass работает]]
- [[00 — Признаки ООП]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]

---

#python/dataclass #oop #classes #beginner
