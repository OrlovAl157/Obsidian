---
tags:
  - python
  - тема/классы
  - тема/ооп
  - тема/dataclass
  - статус/завершён
уровень: средний
принимает_аргументы: true
---

# 📦 Data Classes (dataclass) — Полная памятка

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
| **Оптимизация памяти** | `@dataclass(slots=True)` | Python 3.10+, уменьшает память |

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

person = Person('Гвидо', 'ван Россум', 67)
print(person)  # → Person(name='Гвидо', surname='ван Россум', age=67)
```

### ✅ С dataclass (3 строки кода)

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    surname: str
    age: int

person = Person('Гвидо', 'ван Россум', 67)
print(person)  # → Person(name='Гвидо', surname='ван Россум', age=67)
```

**Что автоматически сгенерировалось:**
- ✅ `__init__` с параметрами в правильном порядке
- ✅ `__repr__` с красивым выводом
- ✅ `__eq__` для сравнения по содержимому

**Экономия: 12 строк кода!** 🎉

---

## 💡 БАЗОВЫЕ КОНЦЕПЦИИ

### Что это?

**Data Classes** (введены в Python 3.7) — это декоратор `@dataclass`, который автоматизирует создание классов для хранения данных. Он генерирует стандартные методы, чтобы вы не писали рутинный код.

### Три автоматических метода

#### 1. `__init__` — конструктор

```python
@dataclass
class Person:
    name: str
    age: int

# Автоматически создаётся:
# def __init__(self, name: str, age: int):
#     self.name = name
#     self.age = age

person = Person('Гвидо', 67)
```

#### 2. `__repr__` — красивый вывод

```python
person = Person('Гвидо', 67)
print(person)
# → Person(name='Гвидо', age=67)  ✅
# БЕЗ dataclass было бы: <Person object at 0x...>  ❌
```

#### 3. `__eq__` — сравнение по содержимому

```python
p1 = Person('Гвидо', 67)
p2 = Person('Гвидо', 67)
p3 = Person('Илон', 51)

print(p1 == p2)  # → True (одинаковое содержимое) ✅
print(p1 == p3)  # → False
print(p1 is p2)  # → False (разные объекты)
```

### ⚠️ ВАЖНО: Аннотация типов обязательна!

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    name: str
    age = 30  # ← БЕЗ аннотации!

# age это просто атрибут класса, НЕ поле dataclass
# person = Person('Гвидо')  → TypeError: missing argument 'age'

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    name: str
    age: int = 30  # ← с аннотацией!

person = Person('Гвидо')  # ✅ age = 30
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
    age: int = None  # значение по умолчанию

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
print(p1 >= p2)  # → False
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
print(user.password)  # → secret123 (атрибут есть, но не выводится)
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

#### `hash=True` — учитывать при вычислении хеша

```python
@dataclass
class Person:
    name: str
    age: int = field(hash=False)  # не учитывать в хеше
```

---

## 📝 ТИПИЗАЦИЯ И HINTS

### `typing.Any` — если тип неизвестен

Иногда нам неизвестен точный тип атрибута. Для этого используем `Any`:

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

### ⚠️ Когда использовать `Any`

- Если атрибут может быть разных типов
- При работе с динамическими данными
- Если точный тип уточнится позже

### Но лучше избегать и указывать конкретный тип

```python
from typing import Union

@dataclass
class Container:
    name: str
    data: Union[dict, list]  # ← конкретнее чем Any
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

# Попытка передать fullname → ошибка
person = Person('Гвидо', 'ван Россум', 'Гвидо ван Россум')
# TypeError: Person.__init__() takes 3 positional arguments but 4 were given
```

### `InitVar` — параметры только для `__post_init__`

```python
from dataclasses import dataclass, InitVar

@dataclass
class Rectangle:
    width: float
    height: float
    unit: InitVar[str] = 'cm'  # параметр только для __init__
    
    def __post_init__(self, unit):
        print(f"Прямоугольник в {unit}")
        # Но в объекте НЕ сохраняется как атрибут

r = Rectangle(10, 20, 'mm')
print(hasattr(r, 'unit'))  # → False (нет такого атрибута)
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

# __init__ сначала параметры Animal, потом Dog
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
    frozen=True,  # неизменяемый
    namespace={
        'greet': lambda self: f"Hello, {self.name}!"
    }
)

person = Person('Гвидо', 67)
print(person.greet())  # → Hello, Гвидо!
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

## 📊 ПАРАМЕТРЫ ДЕКОРАТОРА @dataclass

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

## 💾 SLOTS=TRUE (Python 3.10+) — Оптимизация памяти

### Что это?

`__slots__` — это способ оптимизации памяти. Вместо словаря `__dict__` для хранения атрибутов используется кортеж. Это **ускоряет** доступ и **экономит** память.

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
v.w = 4  # AttributeError: 'Vector' object has no attribute 'w'
```

---

## ⚠️ ЧАСТЫЕ ОШИБКИ И РЕШЕНИЯ

### Ошибка 1: Забыли аннотацию типа

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    name: str
    age = 30  # ← БЕЗ аннотации!

person = Person('Гвидо')  # ❌ TypeError: missing argument 'age'

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    name: str
    age: int = 30  # ← с аннотацией!

person = Person('Гвидо')  # ✅ age = 30
```

### Ошибка 2: Изменяемый тип как default

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Team:
    members: list = []

# ValueError: mutable default <class 'list'> for field members is not allowed

# ✅ ПРАВИЛЬНО
from dataclasses import field

@dataclass
class Team:
    members: list = field(default_factory=list)
```

### Ошибка 3: Обязательное поле после опционального

```python
# ❌ НЕПРАВИЛЬНО
@dataclass
class Person:
    name: str = None
    age: int  # ← обязательное после опционального

# TypeError: non-default argument 'age' follows default argument

# ✅ ПРАВИЛЬНО
@dataclass
class Person:
    name: str  # обязательное
    age: int = None  # опциональное
```

### Ошибка 4: Изменение поля в frozen классе

```python
# ❌ НЕПРАВИЛЬНО
@dataclass(frozen=True)
class Point:
    x: float
    y: float
    length: float = field(init=False)
    
    def __post_init__(self):
        self.length = (self.x**2 + self.y**2)**0.5  # ❌ ОШИБКА!

# FrozenInstanceError: cannot assign to field 'length'

# ✅ ПРАВИЛЬНО
@dataclass(frozen=True)
class Point:
    x: float
    y: float
    length: float = field(init=False)
    
    def __post_init__(self):
        object.__setattr__(self, 'length', (self.x**2 + self.y**2)**0.5)

p = Point(3, 4)
print(p)  # → Point(x=3, y=4, length=5.0) ✅
```

---

## 🎯 КОГДА ИСПОЛЬЗОВАТЬ dataclass

### ✅ ИСПОЛЬЗУЙ dataclass

| Случай | Пример |
|---|---|
| **Простое хранилище данных** | API response, конфиг, DTO |
| **Много атрибутов, простая логика** | User с именем, почтой, возрастом |
| **Нужна стандартная функциональность** | `__init__`, `__repr__`, `__eq__` |
| **Конфиги и настройки** | Database config, API settings |
| **Кэширование результатов** | Результаты запросов |

```python
@dataclass
class APIConfig:
    host: str
    port: int = 8080
    timeout: float = 30.0
    headers: dict = field(default_factory=dict)

@dataclass
class UserDTO:
    id: int
    name: str
    email: str
    created_at: str
```

### ❌ НЕ используй dataclass

| Случай | Почему | Альтернатива |
|---|---|---|
| **Сложная логика класса** | Слишком много методов | Обычный класс |
| **Часто переписываешь `__init__`** | Не экономит код | Обычный класс |
| **Сложное наследование** | Правила полей запутанные | Обычный класс |
| **Нужна валидация** | dataclass не валидирует | `pydantic` |
| **Нужна гибкость** | Всё жёстко | `attrs` или обычный класс |

```python
# ❌ ПЛОХО: слишком сложная логика
@dataclass
class BankAccount:
    balance: float
    
    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self.balance += amount
    
    def withdraw(self, amount):
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount
    
    def get_interest(self):
        # Сложный расчёт...
        pass

# ✅ ХОРОШО: обычный класс для сложной логики
class BankAccount:
    def __init__(self, balance):
        self.balance = balance
    
    def deposit(self, amount):
        # ...
```

---

## 🔮 ПРАКТИЧЕСКИЙ ПРИМЕР: APIConfig

```python
from dataclasses import dataclass, field
from typing import Dict, Optional

@dataclass
class APIConfig:
    """Конфигурация для API."""
    host: str
    port: int = 8080
    timeout: float = field(default=30.0)
    headers: Dict[str, str] = field(default_factory=dict)
    debug: bool = False
    base_url: Optional[str] = field(init=False)
    
    def __post_init__(self):
        # Валидация
        if self.port < 0 or self.port > 65535:
            raise ValueError(f"Invalid port: {self.port}")
        
        # Вычисление
        protocol = 'http' if self.debug else 'https'
        self.base_url = f"{protocol}://{self.host}:{self.port}"
    
    def get_url(self, endpoint: str) -> str:
        return f"{self.base_url}{endpoint}"

# Использование
config = APIConfig('api.example.com', port=443)
print(config)
# → APIConfig(host='api.example.com', port=443, timeout=30.0, 
#             headers={}, debug=False)
print(config.get_url('/users'))
# → https://api.example.com:443/users
```

---

## 💭 ИТОГОВАЯ ШПАРГАЛКА

### Самый простой dataclass

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
```

### С максимальным функционалом

```python
from dataclasses import dataclass, field
from typing import List, Optional

@dataclass(order=True, frozen=False)
class Person:
    # Обязательные поля
    name: str
    age: int
    
    # Опциональные поля
    email: Optional[str] = None
    
    # Изменяемый тип
    friends: List['Person'] = field(default_factory=list)
    
    # Скрыть из repr
    password: str = field(default='', repr=False)
    
    # Исключить из сравнения
    id: int = field(default=0, compare=False)
    
    # Вычисляемое поле
    full_info: str = field(init=False)
    
    def __post_init__(self):
        self.full_info = f"{self.name} ({self.age})"
```

### Наследование

```python
@dataclass
class Animal:
    name: str
    age: int = 0

@dataclass
class Dog(Animal):
    breed: str = "Unknown"
    
    def __post_init__(self):
        super().__post_init__()
        print(f"{self.name} is a {self.breed}")
```

### Динамическое создание

```python
from dataclasses import make_dataclass, field

Person = make_dataclass('Person', [
    ('name', str),
    ('age', int, field(default=0)),
    ('tags', list, field(default_factory=list)),
])
```

---

## 🔗 Ключевые правила

✅ Аннотации типов ОБЯЗАТЕЛЬНЫ  
✅ Обязательные поля ПЕРЕД опциональными  
✅ Для изменяемых типов используй `field(default_factory=...)`  
✅ `__post_init__` вызывается ПОСЛЕ `__init__`  
✅ `init=False` исключает поле из конструктора  
✅ `frozen=True` делает объекты неизменяемыми (используй `object.__setattr__` в `__post_init__`)  
✅ `order=True` добавляет методы сравнения  
✅ `slots=True` (Python 3.10+) оптимизирует память  
✅ Используй `astuple()` и `asdict()` для преобразования  
✅ При наследовании вызови `super().__post_init__()` если оверрайдишь  

---

## 🔗 Связанные темы

- [[00 — Признаки ООП]]
- [[01 — 🎓 ООП - Введение]]
- [[05 — 🏠 Свойства property() и @property]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]
- [[08 — 🎨 Декоратор-класс]]

---

#python/dataclass #oop #classes #advanced
