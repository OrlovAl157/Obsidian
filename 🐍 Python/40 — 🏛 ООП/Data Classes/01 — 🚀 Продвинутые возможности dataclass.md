---
tags:
  - python
  - тема/классы
  - тема/ооп
  - тема/dataclass
  - статус/завершён
уровень: продвинутый
---

# 🚀 Продвинутые возможности dataclass

## 📊 ВСЕ ПАРАМЕТРЫ ДЕКОРАТОРА @dataclass

### Полная таблица

| Параметр | Значение по умолчанию | Что делает |
|---|---|---|
| `init` | `True` | Генерирует `__init__` |
| `repr` | `True` | Генерирует `__repr__` |
| `eq` | `True` | Генерирует `__eq__` |
| `order` | `False` | Генерирует `<`, `>`, `<=`, `>=` |
| `frozen` | `False` | Делает объекты неизменяемыми |
| `slots` | `False` | Использует `__slots__` (Python 3.10+) |

### Примеры комбинаций

```python
# Базовый
@dataclass
class Person:
    name: str

# С полным функционалом
@dataclass(init=True, repr=True, eq=True, order=True, frozen=False)
class Person:
    name: str

# Без __repr__
@dataclass(repr=False)
class Person:
    name: str

person = Person('Гвидо')
print(person)  # → <__main__.Person object at 0x...>
```

---

## 🔒 Как работает frozen=True

### Парадокс

```python
@dataclass(frozen=True)
class Person:
    name: str

person = Person('Гвидо')
person.name = 'Илон'  # ❌ FrozenInstanceError
```

**Вопрос:** Если объект frozen, как тогда `__init__` записывает значения?

### Ответ: object.__setattr__

Dataclass генерирует `__init__` с использованием `object.__setattr__`:

```python
def __init__(self, name):
    object.__setattr__(self, 'name', name)  # ← обходит frozen
```

**Почему это работает:**

При `frozen=True` dataclass переопределяет `__setattr__`:

```python
def __setattr__(self, name, value):
    raise FrozenInstanceError
```

Обычное присваивание:
```
self.name = value
    ↓
Person.__setattr__()
    ↓
FrozenInstanceError
```

Но `object.__setattr__` обходит это:
```
object.__setattr__(self, 'name', value)
    ↓
напрямую меняет объект (без Person.__setattr__)
```

**Это официальный механизм**, который сам dataclass использует внутри frozen.

---

## 💾 SLOTS=TRUE (Python 3.10+) — Оптимизация памяти

### Что это?

`__slots__` — это способ оптимизации памяти. Вместо словаря `__dict__` для хранения атрибутов используется кортеж.

### БЕЗ slots

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
print(p.__dict__)  # → {'x': 1.0, 'y': 2.0}  (использует словарь)
print(hasattr(p, '__dict__'))  # → True
```

### С slots=True

```python
from dataclasses import dataclass

@dataclass(slots=True)
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
print(hasattr(p, '__dict__'))  # → False (нет __dict__)
print(p.__slots__)  # → ('x', 'y')
```

### Преимущества и недостатки

**Преимущества:**
- ✅ Меньше памяти (нет `__dict__`)
- ✅ Быстрее доступ к атрибутам
- ✅ Чуть быстрее создание объектов

**Недостатки:**
- ❌ Нельзя добавлять новые атрибуты динамически
- ❌ Требует Python 3.10+

### Когда использовать

```python
# ✅ ИСПОЛЬЗУЙ slots=True
@dataclass(slots=True)
class Vector:
    x: float
    y: float
    z: float

# Примеры:
# - Создаёшь миллионы объектов
# - Критична производительность
# - Не нужна динамическая добавление атрибутов

# ❌ НЕ ИСПОЛЬЗУЙ slots=True
@dataclass(slots=True)
class FlexibleClass:
    data: dict

v = Vector(1, 2, 3)
v.w = 4  # ❌ AttributeError: 'Vector' object has no attribute 'w'
```

---

## 📋 ПОЛНОЕ ОПИСАНИЕ field()

### Все параметры field()

```python
from dataclasses import field

my_field = field(
    default=None,              # простое значение по умолчанию
    default_factory=list,      # функция для создания default
    init=True,                 # участвует в __init__
    repr=True,                 # участвует в __repr__
    compare=True,              # участвует в сравнении
    hash=None,                 # участвует в хеше
    metadata=None,             # метаданные (произвольный dict)
)
```

### `hash=True` — учитывать при вычислении хеша

```python
@dataclass
class Person:
    name: str
    age: int = field(hash=False)  # не учитывать в хеше

p1 = Person('Гвидо', 67)
p2 = Person('Гвидо', 68)

print(hash(p1) == hash(p2))  # → True (age не учитывается)
```

### `metadata=None` — произвольные метаданные

```python
from dataclasses import dataclass, field, fields

@dataclass
class Person:
    name: str = field(metadata={'description': 'Полное имя'})
    age: int = field(metadata={'min': 0, 'max': 150})

# Получить все поля класса
for f in fields(Person):
    print(f.metadata)
    # → {'description': 'Полное имя'}
    # → {'min': 0, 'max': 150}
```

---

## 🧠 ЧТО ПРОИСХОДИТ ПОД КАПОТОМ: формирование __init__

### Как Python создаёт класс

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
    city = "Moscow"
```

**Шаг 1: Python создаёт обычный класс**

Во время выполнения тела класса:
- `name: str` — это аннотация (НЕ атрибут!)
- `age: int` — это аннотация
- `city = "Moscow"` — это обычный атрибут класса

**Шаг 2: Dataclass читает аннотации**

```python
Person.__annotations__ = {
    'name': str,
    'age': int
}
```

**Важно:** `city` НЕ в аннотациях, потому что это просто присваивание, а не аннотация.

**Шаг 3: Dataclass генерирует __init__**

```python
def __init__(self, name, age):
    self.name = name
    self.age = age
```

**Шаг 4: Результат**

```python
person = Person('Гвидо', 67)
print(person.name)   # → 'Гвидо'
print(person.age)    # → 67
print(person.city)   # → 'Moscow' (из атрибута класса)

# Но city НЕ параметр __init__!
person2 = Person('Илон', 51, 'SF')  # ❌ TypeError
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
обычный атрибут класса (x = 10) игнорируется
```

---

## ⚠️ НАСЛЕДОВАНИЕ И ПОРЯДОК ПОЛЕЙ

### Как dataclass собирает поля при наследовании

```python
@dataclass
class Animal:
    name: str
    age: int = 0

@dataclass
class Dog(Animal):
    breed: str
```

**Шаг 1:** Dataclass собирает поля родителя
```
name
age=0
```

**Шаг 2:** Добавляет поля потомка
```
name
age=0
breed
```

**Шаг 3:** Пытается генерировать __init__

```python
def __init__(self, name, age=0, breed):  # ❌ ОШИБКА!
```

### ❌ Почему это ошибка

Python запрещает такой порядок параметров:

```python
def f(x=10, y):  # ❌ non-default argument follows default argument
    pass
```

### 💥 Ошибка

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
def __init__(self, name, age=0, breed="Unknown"):  # ✅
```

### 📌 Главное правило

После объединения ВСЕХ полей (родители + потомки) должен сохраняться порядок:

```
non-default fields
    ↓
default fields
```

**Допустимо:**
```python
@dataclass
class A:
    x: int

@dataclass
class B(A):
    y: int = 10
```
→ `def __init__(self, x, y=10)` ✅

**Недопустимо:**
```python
@dataclass
class A:
    x: int = 10

@dataclass
class B(A):
    y: int
```
→ `def __init__(self, x=10, y)` ❌

---

## 🔄 ПЕРЕОПРЕДЕЛЕНИЕ ПОЛЕЙ ПРИ НАСЛЕДОВАНИИ

### Ключевой момент

Когда в потомке переопределяешь поле, оно остаётся **на той же позиции**, на которой было в родителе:

```python
@dataclass
class Animal:
    name: str
    age: int
    weight: float

@dataclass
class Dog(Animal):
    age: int = 5  # ← переопределяем age
    breed: str = "Unknown"
```

**Итоговый порядок полей:**

```
name          ← из Animal
age=5         ← переопределено в Dog, но позиция та же!
weight        ← из Animal (обязательное)
breed="Unknown" ← из Dog
```

**Но это создаст ошибку:**

```python
# ❌ Потому что после age=5 идёт weight (без default)
# TypeError: non-default argument 'weight' follows default argument
```

**Правильно:**

```python
@dataclass
class Dog(Animal):
    age: int = 5
    breed: str = "Unknown"
    weight: float = 25.0  # ← тоже с default
```

**Главная идея:** Позиция переопределяемого поля диктуется первым появлением в иерархии наследования.

---

## ❓ Q&A: Частые вопросы

### Вопрос 1: Какую функцию использовать в default_factory для словаря?

**Варианты:**
- A. `default_factory={}`
- B. `default_factory=dict`
- C. `default_factory=lambda: dict()`

**Ответ: ✅ B — `default_factory=dict`**

В `default_factory` нужно передавать **саму функцию (callable)**, а не результат её вызова.

```python
# ❌ НЕПРАВИЛЬНО
('settings', dict, field(default_factory={}))
# ValueError: mutable default

# ✅ ПРАВИЛЬНО
('settings', dict, field(default_factory=dict))

Config1 = make_dataclass('Config', [...])
Config2 = make_dataclass('Config', [...])

c1 = Config1()
c2 = Config2()

c1.settings['timeout'] = 30
print(c1.settings)  # → {'timeout': 30}
print(c2.settings)  # → {} (отдельный словарь!)
```

---

### Вопрос 2: Три формата полей в make_dataclass()

`make_dataclass()` принимает поля в трёх форматах:

```python
Person = make_dataclass('Person', [
    'name',                           # формат 1: только имя (тип Any)
    ('age', int),                     # формат 2: имя и тип
    ('email', str, field(default='')), # формат 3: имя, тип и field()
])
```

---

### Вопрос 3: Когда использовать __post_init__ с frozen=True?

```python
@dataclass(frozen=True)
class Point:
    x: float
    y: float
    length: float = field(init=False)
    
    def __post_init__(self):
        # ❌ Обычное присваивание не работает
        # self.length = (self.x**2 + self.y**2)**0.5
        
        # ✅ Используй object.__setattr__
        object.__setattr__(self, 'length', (self.x**2 + self.y**2)**0.5)

p = Point(3, 4)
print(p.length)  # → 5.0 ✅

p.length = 10  # ❌ FrozenInstanceError
```

---

## 📚 Частые ошибки и решения

### Ошибка 1: Забыли field() для изменяемого типа

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Team:
    members: list = []

# ValueError: mutable default <class 'list'> for field members is not allowed

# ✅ ПРАВИЛЬНО
@dataclass
class Team:
    members: list = field(default_factory=list)
```

### Ошибка 2: Обязательное поле после опционального

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    age: int = 30
    name: str

# TypeError: non-default argument 'name' follows default argument

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    name: str
    age: int = 30
```

### Ошибка 3: Переопределение при наследовании без default

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Animal:
    age: int = 0

@dataclass
class Dog(Animal):
    breed: str  # ← без default!

# TypeError: non-default argument 'breed' follows default argument

# ✅ ПРАВИЛЬНО
@dataclass
class Dog(Animal):
    breed: str = "Unknown"
```

---

## ✅ Ключевые правила продвинутого уровня

✅ Все параметры `@dataclass()` работают вместе  
✅ `frozen=True` использует `object.__setattr__` внутри  
✅ `slots=True` экономит память, но теряется гибкость  
✅ `field()` имеет 6+ параметров для тонкой настройки  
✅ Формирование `__init__` подчиняется правилам Python  
✅ Наследование требует соблюдения порядка defaults  
✅ Переопределённые поля остаются на той же позиции  
✅ `metadata` позволяет добавлять свои данные к полям  

---

## 🔗 Связанные темы

- [[00 — 📦 Основы dataclass]]
- [[02 — 🔧 Как dataclass работает]]
- [[35 — 🏷 Типизация (type hints)]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]

---

#python/dataclass #oop #advanced #fields
