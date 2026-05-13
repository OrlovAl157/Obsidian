---
tags:
  - python
  - тема/классы
  - тема/ооп
  - тема/dataclass
  - статус/завершён
уровень: продвинутый
---

# 🚀 Всё про dataclass (продвинутый уровень)

## 📊 ВСЕ ПАРАМЕТРЫ ДЕКОРАТОРА @dataclass

| Параметр | Значение по умолчанию | Что делает |
|---|---|---|
| `init` | `True` | Генерирует `__init__` |
| `repr` | `True` | Генерирует `__repr__` |
| `eq` | `True` | Генерирует `__eq__` |
| `order` | `False` | Генерирует `<`, `>`, `<=`, `>=` |
| `frozen` | `False` | Делает объекты неизменяемыми |
| `slots` | `False` | Использует `__slots__` (Python 3.10+) |

---

## 🔒 frozen=True: парадокс с object.__setattr__

### Парадокс

```python
@dataclass(frozen=True)
class Person:
    name: str

person = Person('Гвидо')
person.name = 'Илон'  # ❌ FrozenInstanceError
```

Если объект `frozen=True` (неизменяемый), как тогда `__init__` вообще записывает значения `self.name = name`?

### Ответ: Dataclass использует object.__setattr__

Сгенерированный dataclass НЕ использует обычное `self.name = name`.

Dataclass генерирует примерно так:

```python
def __init__(self, name):
    object.__setattr__(self, 'name', name)
```

### Почему это работает

При `@dataclass(frozen=True)` dataclass генерирует переопределённый `__setattr__`:

```python
def __setattr__(self, name, value):
    raise FrozenInstanceError
```

Обычное присваивание вызывает эту ошибку:

```
self.name = value
    ↓
Person.__setattr__()
    ↓
FrozenInstanceError ❌
```

Но `object.__setattr__` — это прямой вызов базового механизма Python. Он НЕ проходит через переопределённый `Person.__setattr__`:

```
object.__setattr__(self, 'name', value)
    ↓
напрямую меняет объект ✅
```

### Это официальный механизм

Это НЕ "обход хака". Это официальный способ, который сам dataclass использует внутри при `frozen=True`.

### Практический случай: __post_init__ с frozen

```python
@dataclass(frozen=True)
class Point:
    x: float
    y: float
    distance: float = field(init=False)
    
    def __post_init__(self):
        # ❌ Это не сработает
        # self.distance = (self.x**2 + self.y**2)**0.5
        
        # ✅ Нужно использовать object.__setattr__
        object.__setattr__(self, 'distance', (self.x**2 + self.y**2)**0.5)

p = Point(3, 4)
print(p.distance)  # → 5.0 ✅

p.distance = 10  # ❌ FrozenInstanceError
```

---

## 💾 SLOTS=TRUE (Python 3.10+) — Оптимизация памяти

### Что это?

`__slots__` — это способ оптимизации памяти. Вместо словаря `__dict__` для хранения атрибутов используется кортеж.

### БЕЗ slots

```python
@dataclass
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
print(p.__dict__)  # → {'x': 1.0, 'y': 2.0}
print(hasattr(p, '__dict__'))  # → True
```

### С slots=True

```python
@dataclass(slots=True)
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
print(hasattr(p, '__dict__'))  # → False
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

Используй `slots=True` когда:
- Создаёшь миллионы объектов
- Критична производительность
- Не нужна динамическая добавление атрибутов

```python
# ✅ ИСПОЛЬЗУЙ slots=True
@dataclass(slots=True)
class Vector:
    x: float
    y: float
    z: float

v = Vector(1, 2, 3)
v.w = 4  # ❌ AttributeError: 'Vector' object has no attribute 'w'
```

---

## 📋 FIELD() ПОЛНОЕ ОПИСАНИЕ

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

## 🧠 Как dataclass формирует __init__

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
- `name: str` — это аннотация (НЕ атрибут!)
- `age: int` — это аннотация
- `city = "Moscow"` — это обычный атрибут класса

Python собирает аннотации в:

```python
Person.__annotations__ = {
    'name': str,
    'age': int
}
```

### Шаг 2: Что НЕ попадёт в dataclass fields

```python
city = "Moscow"
```

Это просто атрибут класса, не поле dataclass. Поэтому `city` НЕ попадёт в генерацию `__init__`.

### Шаг 3: Dataclass читает __annotations__

Dataclass смотрит на:

```python
{
    'name': str,
    'age': int
}
```

и формирует список полей: `name`, `age`.

### Шаг 4: Генерируется __init__

Dataclass автоматически создаёт:

```python
def __init__(self, name, age):
    self.name = name
    self.age = age
```

### Ключевая формула

```
аннотация (x: int)
    ↓
__annotations__
    ↓
dataclass читает поля
    ↓
генерирует __init__
    ↓
обычный атрибут класса (x = 10) игнорируется
```

### Важно помнить

✅ `x: int` — поле dataclass  
❌ `x = 10` — обычный атрибут класса

---

## ⚠️ НАСЛЕДОВАНИЕ: ПОРЯДОК ПОЛЕЙ И ПЕРЕОПРЕДЕЛЕНИЕ

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

Dataclass НЕ создаёт отдельный `__init__` для Dog. Он собирает **ОДИН общий `__init__`** по всей цепочке наследования.

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
def __init__(self, name, age=0, breed):  # ❌ ОШИБКА!
```

### ❌ Почему это ошибка

Python запрещает такой порядок параметров:

```python
def f(x=10, y):  # ❌ non-default argument follows default argument
    pass
```

Правило Python:

```
обычный параметр
    ↓
параметр со значением по умолчанию
```

В обратном порядке запрещено!

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
def __init__(self, name, age=0, breed="Unknown"):  # ✅
```

### 📌 Главное правило

После объединения ВСЕХ полей (родители → потомки) должен сохраняться порядок:

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

## 🔄 ПЕРЕОПРЕДЕЛЕНИЕ ПОЛЕЙ ПРИ НАСЛЕДОВАНИИ

### Ключевая идея

Когда в потомке переопределяешь поле, оно **остаётся на той же позиции**, на которой первый раз появилось в иерархии наследования.

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
name            # позиция 0 (из Animal)
age=5           # позиция 1 (переопределено, но позиция та же!)
weight          # позиция 2 (из Animal, обязательное!)
breed="Unknown" # из Dog
```

Несмотря на переопределение типа или значения в потомке, порядок аргументов конструктора диктуется первым появлением поля в иерархии.

### ❌ Проблема

Это создаст ошибку, потому что `weight` (без default) идёт после `age=5` (с default):

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

Позиция переопределяемого поля диктуется первым появлением в иерархии наследования, а не тем, где оно переопределено.

---

## 🎲 MAKE_DATACLASS()

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

### Три формата полей в make_dataclass()

`make_dataclass()` принимает поля в трёх форматах:

**Формат 1: только имя**

```python
'name'  # → name: Any
```

**Формат 2: имя и тип**

```python
('name', str)  # → name: str
```

**Формат 3: имя, тип и field()**

```python
('name', str, field(default=''))  # → name: str = ''
```

Все три можно комбинировать в одном списке:

```python
from dataclasses import make_dataclass, field

Person = make_dataclass('Person', [
    'id',                    # формат 1
    ('name', str),           # формат 2
    ('age', int, field(default=0)),  # формат 3
])

person = Person(123, 'Гвидо')
print(person)  # → Person(id=123, name='Гвидо', age=0)
```

### С defaults и методами

```python
from dataclasses import make_dataclass, field

Person = make_dataclass('Person',
    [('name', str), ('age', int, field(default=0))],
    frozen=True,
    namespace={'greet': lambda self: f"Hello, {self.name}!"}
)

person = Person('Гвидо')
print(person.greet())  # → Hello, Гвидо!
```

### default_factory: функция vs результат

В `default_factory` нужно передавать **саму функцию (callable)**, а не результат её вызова.

**Неправильно:**

```python
@dataclass
class Config:
    settings: dict = field(default_factory={})
```

❌ `{}` — это результат вызова (пустой словарь). Создаётся ОДИН РАЗ при определении класса. Все экземпляры разделяют ОДИН словарь!

**Правильно:**

```python
@dataclass
class Config:
    settings: dict = field(default_factory=dict)
```

✅ `dict` — это сама функция (конструктор). При создании каждого экземпляра вызывается `dict()` и создаётся НОВЫЙ словарь.

Демонстрация разницы:

```python
c1 = Config()
c2 = Config()

c1.settings['timeout'] = 30
print(c1.settings)  # → {'timeout': 30}
print(c2.settings)  # → {} ✅ Пусто, как надо!
```

Аналогично для других типов:

```python
field(default_factory=list)   # для списков
field(default_factory=set)    # для множеств
field(default_factory=dict)   # для словарей
```

---

## 📚 Как dataclass НЕ магия

### Ключевой момент

Dataclass — это просто **автоматический генератор кода**.

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
```

Примерно эквивалентно:

```python
class Person:
    def __init__(self, name, age=30, friends=None):
        self.name = name
        self.age = age
        self.friends = friends if friends is not None else []
    
    def __repr__(self):
        return f"Person(name={self.name!r}, age={self.age!r}, friends={self.friends!r})"
    
    def __eq__(self, other):
        if not isinstance(other, Person):
            return NotImplemented
        return (self.name == other.name and 
                self.age == other.age and
                self.friends == other.friends)
```

(реальная генерация немного сложнее, но принцип тот же)

### Если сгенерированный код невалиден

Python выбрасывает ошибку. Это НЕ проблема dataclass — это просто что код оказался невалидным.

---

## ✅ Ключевые правила продвинутого уровня

✅ Все параметры `@dataclass()` работают вместе  
✅ `frozen=True` использует `object.__setattr__` внутри  
✅ `slots=True` экономит память, но теряется гибкость  
✅ `field()` имеет 7 параметров для тонкой настройки  
✅ Формирование `__init__` подчиняется правилам Python  
✅ Наследование требует соблюдения порядка defaults  
✅ Переопределённые поля остаются на той же позиции  
✅ `metadata` позволяет добавлять свои данные к полям  
✅ Dataclass — это просто генератор кода, не магия  

---

## 🔗 Связанные темы

- [[00 — 📦 Основы dataclass]]
- [[35 — 🏷 Типизация (type hints)]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]

---

#python/dataclass #oop #advanced #internals
