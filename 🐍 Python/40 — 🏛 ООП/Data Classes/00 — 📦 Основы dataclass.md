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

| Что нужно | Код | Описание |
|---|---|---|
| **Создать dataclass** | `@dataclass`<br>`class Person:`<br>`    name: str` | Автоматически создаёт `__init__`, `__repr__`, `__eq__` |
| **Значение по умолчанию** | `age: int = 30` | Простое значение по умолчанию |
| **Изменяемый тип** | `friends: list = field(default_factory=list)` | ОБЯЗАТЕЛЬНО для списков/словарей! |
| **Неизменяемый** | `@dataclass(frozen=True)` | Объект нельзя менять после создания |
| **Сравнение** | `@dataclass(order=True)` | Добавляет методы `<`, `>`, `<=`, `>=` |
| **Скрыть из repr** | `field(repr=False)` | Поле не выводится при print() |
| **Исключить сравнение** | `field(compare=False)` | Поле не учитывается в == |
| **Вычисляемое поле** | `field(init=False)` + `__post_init__` | Вычисляется в __post_init__ |
| **Кортеж/словарь** | `astuple(obj)` / `asdict(obj)` | Преобразование объекта |

---

## 🎯 СРАВНЕНИЕ: dataclass vs обычный класс

### ❌ БЕЗ dataclass (15 строк)

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

### ✅ С dataclass (3 строки)

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

**Data Classes** — декоратор `@dataclass`, который автоматически генерирует стандартные методы для классов хранения данных.

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
@dataclass
class Person:
    name: str
    surname: str
    age: int

person = Person('Гвидо', 'ван Россум', 67)
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
```

### ⚠️ ПРАВИЛО: обязательные поля ПЕРЕД опциональными

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    name: str = None
    age: int  # ← ошибка!

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    name: str
    age: int = 30
```

### `frozen=True` — неизменяемость

```python
@dataclass(frozen=True)
class Point:
    x: float
    y: float

p = Point(1, 2)
p.x = 3  # ❌ FrozenInstanceError
```

### `order=True` — методы сравнения

```python
@dataclass(order=True)
class Person:
    name: str
    age: int

p1 = Person('Гвидо', 67)
p2 = Person('Илон', 51)

print(p1 > p2)  # → False
```

---

## 🔧 РАБОТА С ПОЛЯМИ: field()

### ❌ ПРОБЛЕМА: изменяемые типы

```python
@dataclass
class Team:
    members: list = []  # ❌ ОШИБКА!

# ValueError: mutable default <class 'list'> for field members is not allowed
```

**Почему?** Значение по умолчанию создаётся ОДИН РАЗ. Все экземпляры разделяют ОДИН список!

### ✅ РЕШЕНИЕ: `default_factory`

```python
from dataclasses import dataclass, field

@dataclass
class Team:
    members: list = field(default_factory=list)

t1 = Team()
t2 = Team()
t1.members.append('Alice')

print(t1.members)  # → ['Alice']
print(t2.members)  # → [] ✅
```

### Параметры field()

```python
# Скрыть из __repr__
password: str = field(repr=False)

# Исключить из сравнения
id: int = field(compare=False)

# Исключить из конструктора
fullname: str = field(init=False)

# Простое значение по умолчанию
age: int = field(default=0)
```

---

## 📝 ТИПИЗАЦИЯ

### `typing.Any` — если тип неизвестен

```python
from typing import Any

@dataclass
class Container:
    name: str
    data: Any  # может быть что угодно

container = Container('config', {'key': 'value'})
```

---

## 🔄 РАСШИРЕННАЯ ИНИЦИАЛИЗАЦИЯ: __post_init__

### Вычисляемые атрибуты

```python
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

### `InitVar` — параметры только для __post_init__

```python
from dataclasses import InitVar

@dataclass
class Rectangle:
    width: float
    height: float
    unit: InitVar[str] = 'cm'
    
    def __post_init__(self, unit):
        print(f"Размер: {unit}")

r = Rectangle(10, 20, 'mm')
print(hasattr(r, 'unit'))  # → False
```

---

## 👨‍👧 НАСЛЕДОВАНИЕ

### Порядок полей

```python
@dataclass
class Animal:
    name: str
    age: int

@dataclass
class Dog(Animal):
    breed: str

d = Dog('Rex', 5, 'Лабрадор')
```

Dataclass собирает поля: сначала из родителя, потом из потомка. Создаётся ОДИН общий `__init__`.

### ⚠️ С defaults нужна осторожность

```python
@dataclass
class Animal:
    name: str
    age: int = 0

@dataclass
class Dog(Animal):
    breed: str = "Unknown"  # ← обязательно с default!
```

### __post_init__ в наследнике

```python
@dataclass
class Dog(Animal):
    breed: str
    
    def __post_init__(self):
        super().__post_init__()  # ← вызови родителя!
        print(f"Порода: {self.breed}")
```

---

## 🛠️ УТИЛИТЫ

### astuple() и asdict()

```python
from dataclasses import astuple, asdict

person = Person('Гвидо', 'ван Россум', 67)

print(astuple(person))
# → ('Гвидо', 'ван Россум', 67)

print(asdict(person))
# → {'name': 'Гвидо', 'surname': 'ван Россум', 'age': 67}
```

---

## 🎯 КОГДА ИСПОЛЬЗОВАТЬ dataclass

### ✅ ИСПОЛЬЗУЙ

- Простое хранилище данных (API response, конфиг, DTO)
- Много атрибутов, простая логика
- Нужна стандартная функциональность
- Конфиги и настройки
- Кэширование результатов

### ❌ НЕ ИСПОЛЬЗУЙ

- Сложная логика класса
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
✅ При наследовании вызови `super().__post_init__()`  

---

## 🔗 Связанные темы

- [[01 — 🚀 Всё про dataclass (продвинутый уровень)]]
- [[00 — Признаки ООП]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]

---

#python/dataclass #oop #classes #beginner
