---
tags:
  - python
  - тема/классы
  - тема/ооп
  - тема/dataclass
  - статус/завершён
уровень: эксперт
---

# 🔧 Как dataclass работает

## 🔒 frozen=True: парадокс с __setattr__

### Вопрос

```python
@dataclass(frozen=True)
class Person:
    name: str

person = Person('Гвидо')
person.name = 'Илон'  # ❌ FrozenInstanceError
```

**Парадокс:** Если объект frozen (неизменяемый), как тогда `__init__` записывает значения `self.name = name`?

### Ответ: Dataclass использует object.__setattr__

Dataclass генерирует `__init__` специально для frozen объектов:

```python
# Вместо обычного:
def __init__(self, name):
    self.name = name

# Dataclass генерирует:
def __init__(self, name):
    object.__setattr__(self, 'name', name)
```

### Почему это работает

**При `frozen=True` dataclass переопределяет `__setattr__`:**

```python
def __setattr__(self, name, value):
    raise FrozenInstanceError(f"cannot assign to field '{name}'")
```

**Обычное присваивание вызывает эту ошибку:**

```
self.name = value
    ↓
Person.__setattr__('name', value)  ← вызывается переопределённый метод
    ↓
FrozenInstanceError
```

**Но `object.__setattr__` обходит это:**

```
object.__setattr__(self, 'name', value)
    ↓
напрямую меняет атрибут, БЕЗ вызова Person.__setattr__
    ↓
работает!
```

### Схема потока вызовов

```
Обычное присваивание:
self.name = value
    │
    ▼
Person.__setattr__()
    │
    ▼
FrozenInstanceError ❌

─────────────────────────────

object.__setattr__:
object.__setattr__(self, 'name', value)
    │
    ▼
напрямую к механизму Python
    │
    ▼
атрибут изменён ✅
```

### Это официальный механизм

Это НЕ "обход хака". Это официальный способ, который сам `dataclass` использует внутри при `frozen=True`.

### Практический случай: __post_init__ с frozen

```python
@dataclass(frozen=True)
class Point:
    x: float
    y: float
    distance_from_origin: float = field(init=False)
    
    def __post_init__(self):
        # ❌ Это не сработает
        # self.distance_from_origin = (self.x**2 + self.y**2)**0.5
        
        # ✅ Нужно использовать object.__setattr__
        object.__setattr__(
            self,
            'distance_from_origin',
            (self.x**2 + self.y**2)**0.5
        )

p = Point(3, 4)
print(p.distance_from_origin)  # → 5.0 ✅

p.distance_from_origin = 10  # ❌ FrozenInstanceError
```

---

## 🧠 Формирование __init__: пошагово

### Исходный код

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
    city = "Moscow"
```

### Шаг 1: Python создаёт обычный класс

Во время выполнения тела класса:

```
name: str      ← аннотация (НЕ атрибут!)
age: int       ← аннотация
city = "Moscow" ← обычный атрибут класса
```

**Результат:**
- `name` и `age` → в `__annotations__`
- `city` → остаётся как атрибут класса

```python
Person.__annotations__ = {
    'name': str,
    'age': int
}

Person.city = "Moscow"  # ← обычный атрибут
```

### Шаг 2: Dataclass читает __annotations__

```python
{
    'name': str,
    'age': int
}
```

**Важно:** `city` НЕ здесь, потому что это просто присваивание, а не аннотация.

### Шаг 3: Dataclass генерирует __init__

На основе прочитанных аннотаций:

```python
def __init__(self, name, age):
    self.name = name
    self.age = age
```

### Шаг 4: Результат

```python
person = Person('Гвидо', 67)

print(person.name)   # → 'Гвидо' (от параметра __init__)
print(person.age)    # → 67 (от параметра __init__)
print(person.city)   # → 'Moscow' (от атрибута класса)

# Но city НЕ параметр __init__!
Person('Гвидо', 67, 'SF')  # ❌ TypeError: too many positional arguments
```

### Ключевая формула

```
аннотация (x: int)
    ↓
__annotations__
    ↓
dataclass читает
    ↓
генерирует __init__
    ↓
обычный атрибут класса (x = 10)
остаётся как есть
```

---

## ⚠️ Наследование и порядок полей

### Как dataclass собирает поля

```python
@dataclass
class Animal:
    name: str
    age: int = 0

@dataclass
class Dog(Animal):
    breed: str
```

**Dataclass НЕ создаёт отдельный `__init__` для Dog.**

Он собирает **ОДИН общий `__init__`** по всей цепочке наследования.

### Шаг 1: Собирает поля родителя

```
name
age=0
```

### Шаг 2: Добавляет поля потомка

```
name
age=0
breed
```

### Шаг 3: Пытается генерировать __init__

```python
def __init__(self,
             name,
             age=0,
             breed):  # ← без default!
```

### ❌ Почему это ошибка

Python запрещает:

```python
def f(x=10, y):  # ❌ non-default argument follows default argument
    pass
```

**Правило Python:**
```
обычный параметр
    ↓
параметр со значением по умолчанию
```

**В обратном порядке запрещено!**

### 💥 Результат

```
TypeError:
non-default argument 'breed'
follows default argument
```

### ✅ Как исправить

```python
@dataclass
class Dog(Animal):
    breed: str = "Unknown"  # ← добавляем default
```

Теперь:

```python
def __init__(self,
             name,
             age=0,
             breed="Unknown"):  # ✅ валидно
```

### 📌 Главное правило

После объединения ВСЕХ полей (родители → потомки) должен сохраняться порядок:

```
non-default fields (обязательные)
    ↓
default fields (с значениями)
```

**Допустимо:**
```python
@dataclass
class A:
    x: int

@dataclass
class B(A):
    y: int = 10

# → def __init__(self, x, y=10) ✅
```

**Недопустимо:**
```python
@dataclass
class A:
    x: int = 10

@dataclass
class B(A):
    y: int

# → def __init__(self, x=10, y) ❌
```

---

## 🔄 Переопределение полей при наследовании

### Ключевая идея

Когда в потомке переопределяешь поле, оно **остаётся на той же позиции**, на которой первый раз появилось в иерархии:

```python
@dataclass
class Animal:
    name: str       # позиция 0
    age: int        # позиция 1
    weight: float   # позиция 2

@dataclass
class Dog(Animal):
    age: int = 5    # переопределяем, но позиция та же (1)!
    breed: str = "Unknown"
```

### Итоговый порядок полей

```
name            ← позиция 0 (из Animal)
age=5           ← позиция 1 (переопределено, но позиция та же!)
weight          ← позиция 2 (из Animal, обязательное!)
breed="Unknown" ← из Dog
```

### ❌ Проблема

```python
# Dataclass попытается создать:
def __init__(self, name, age=5, weight, breed="Unknown"):
    # ❌ weight идёт после age=5
```

### ✅ Решение

Все поля после первого с default должны иметь default:

```python
@dataclass
class Dog(Animal):
    age: int = 5
    weight: float = 25.0  # ← тоже с default
    breed: str = "Unknown"
```

### Главная идея

**Позиция переопределяемого поля диктуется первым появлением в иерархии наследования**, а не тем, где оно переопределено.

---

## ❓ Q&A: Как это работает

### Вопрос 1: Почему default_factory=dict, а не default_factory={}?

**Объяснение:**

- `{}` — это **результат вызова** (пустой словарь)
- `dict` — это **сама функция** (конструктор)

```python
# ❌ НЕПРАВИЛЬНО
field(default_factory={})
# {} создаётся ОДИН раз при определении класса
# Все экземпляры разделяют ОДИН словарь!

# ✅ ПРАВИЛЬНО
field(default_factory=dict)
# При создании каждого экземпляра вызывается dict()
# Создаётся НОВЫЙ словарь
```

**Демонстрация:**

```python
from dataclasses import dataclass, field

@dataclass
class Config1:
    settings: dict = field(default_factory=dict)

@dataclass
class Config2:
    settings: dict = field(default_factory=dict)

c1 = Config1()
c2 = Config2()

c1.settings['timeout'] = 30
print(c1.settings)  # → {'timeout': 30}
print(c2.settings)  # → {} (отдельный словарь!)
```

---

### Вопрос 2: Как работают три формата в make_dataclass()?

**Формат 1: только имя**
```python
'name'
# → name: Any (без аннотации типа)
```

**Формат 2: имя и тип**
```python
('name', str)
# → name: str
```

**Формат 3: имя, тип и field()**
```python
('name', str, field(default=''))
# → name: str = '' (с настройкой)
```

**Все три работают вместе:**

```python
from dataclasses import make_dataclass, field
from typing import Any

Person = make_dataclass('Person', [
    'id',                    # формат 1
    ('name', str),           # формат 2
    ('age', int, field(default=0)),  # формат 3
])

person = Person(123, 'Гвидо')
print(person)  
# → Person(id=123, name='Гвидо', age=0)
```

---

## 📚 Как dataclass НЕ магия

### Ключевой момент

Dataclass — это просто **автоматический код генератор**.

Он не использует никакие специальные возможности Python. Он просто:

1. Читает аннотации из `__annotations__`
2. Генерирует обычный Python код
3. Вставляет код в класс

### Пример: что генерируется

```python
@dataclass
class Person:
    name: str
    age: int = 30
    friends: list = field(default_factory=list)
    password: str = field(repr=False)
```

Примерно эквивалентно:

```python
class Person:
    def __init__(self, name, age=30, friends=None, password=''):
        self.name = name
        self.age = age
        self.friends = friends if friends is not None else []
        self.password = password
    
    def __repr__(self):
        return f"Person(name={self.name!r}, age={self.age!r}, friends={self.friends!r})"
    
    def __eq__(self, other):
        if not isinstance(other, Person):
            return NotImplemented
        return (self.name == other.name and 
                self.age == other.age and
                self.friends == other.friends and
                self.password == other.password)
```

(реальная генерация немного сложнее, но принцип тот же)

### Если сгенерированный код невалиден

Python выбрасывает ошибку. Это **не проблема dataclass** — это просто что код оказался невалидным.

---

## ✅ Ключевые правила эксперта

✅ `frozen=True` использует `object.__setattr__` в `__init__`  
✅ Формирование `__init__` читает только `__annotations__`  
✅ Обычные присваивания в теле класса игнорируются  
✅ Наследование собирает ОДИН общий `__init__`  
✅ Порядок полей подчиняется правилам Python  
✅ Переопределённые поля остаются на той же позиции  
✅ `default_factory` — это функция, не результат её вызова  
✅ Dataclass — это просто генератор кода, не магия  

---

## 🔗 Связанные темы

- [[00 — 📦 Основы dataclass]]
- [[01 — 🚀 Продвинутые возможности dataclass]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]
- [[35 — 🏷 Типизация (type hints)]]

---

#python/dataclass #oop #internals #expert
