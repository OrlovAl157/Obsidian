---
tags:
  - python
  - тема/ооп
  - тема/под_капотом
  - тема/метаклассы
  - статус/завершён
уровень: продвинутый
---

# Памятка: type и object в Python

## ⚡ БЫСТРАЯ ШПАРГАЛКА

```python
# Проверка типов и классов
type(x)                 # Узнать тип/класс объекта
isinstance(x, Class)    # Проверить, является ли x экземпляром Class
issubclass(A, B)        # Проверить, наследуется ли A от B

# Ключевые факты
type(5)                 # <class 'int'>
type(int)               # <class 'type'>
type(object)            # <class 'type'>
type(type)              # <class 'type'>

isinstance(5, int)      # True
isinstance(int, type)   # True
isinstance(type, object)# True
issubclass(int, object) # True
```

### Главное правило

- **object** — базовый класс для ВСЕХ объектов
- **type** — метакласс, который создаёт ВСЕ классы
- Они образуют **замкнутую систему** на вершине Python

---

## 🧭 ИЕРАРХИЯ ОСНОВ PYTHON

```
        ┌──────────────┐
        │   type       │  ← метакласс (создаёт классы)
        └─────▲────────┘
              │
              │ создает
              ▼
        ┌──────────────┐
        │   object     │  ← базовый класс для всех объектов
        └─────▲────────┘
              │
              │ наследуются от него
              ▼
   ┌────────────┬──────────────┬──────────────┐
   │   int      │    str       │   MyClass    │  ← классы
   └────▲───────┴──────▲───────┴──────▲───────┘
        │              │              │
        │ создают      │ создают      │ создают
        ▼              ▼              ▼
        5           "hello"        my_obj       ← экземпляры
```

---

## 📚 ПОДРОБНО

### 1. object — базовый класс для всех

**object** — это корень всей иерархии классов в Python.

```python
# Все классы наследуются от object
class MyClass:
    pass

print(issubclass(MyClass, object))  # True
print(issubclass(int, object))      # True
print(issubclass(str, object))      # True
print(issubclass(list, object))     # True
```

#### Что даёт object?

Минимальный набор методов для всех объектов:

```python
# Методы из object
obj = object()

obj.__init__()      # Инициализация
obj.__repr__()      # Строковое представление
obj.__str__()       # Строка для пользователя
obj.__eq__(other)   # Сравнение ==
obj.__ne__(other)   # Сравнение !=
obj.__hash__()      # Хеш объекта
obj.__class__       # Класс объекта
```

#### Неявное наследование

```python
# Эти записи эквивалентны:
class MyClass:
    pass

class MyClass(object):
    pass

# object добавляется автоматически!
```

---

### 2. type — метакласс, создающий классы

**type** — это класс, который создаёт другие классы.

```python
# type создаёт все классы
print(type(int))        # <class 'type'>
print(type(str))        # <class 'type'>
print(type(list))       # <class 'type'>
print(type(object))     # <class 'type'>

# Даже пользовательские классы
class MyClass:
    pass

print(type(MyClass))    # <class 'type'>
```

#### type как конструктор классов

```python
# Создание класса динамически
MyClass = type('MyClass', (), {'x': 5})

# Эквивалентно:
class MyClass:
    x = 5

print(MyClass.x)        # 5
```

#### Синтаксис type для создания класса

```python
# type(name, bases, dict)
# name - имя класса
# bases - кортеж базовых классов
# dict - словарь атрибутов

Point = type('Point', (), {
    'x': 0,
    'y': 0,
    'move': lambda self, dx, dy: setattr(self, 'x', self.x + dx)
})

p = Point()
print(p.x)              # 0
```

---

### 3. Замкнутый круг: type ↔ object

Вот где начинается магия Python:

```python
# object создан type
print(type(object))             # <class 'type'>

# type наследуется от object
print(isinstance(type, object)) # True
print(issubclass(type, object)) # True

# type создаёт сам себя!
print(type(type))               # <class 'type'>

# object — это экземпляр type
print(isinstance(object, type)) # True
```

#### Визуализация замкнутого круга

```
    type(object) → type     (type создаёт object)
         ↓                  
    type(type) → type       (type создаёт сам себя)
         ↓
    isinstance(type, object) → True    (type наследуется от object)
         ↓
    isinstance(object, type) → True    (object создан type)
    
🔁 Они ссылаются друг на друга!
```

---

## 🎯 ПРИМЕРЫ ИСПОЛЬЗОВАНИЯ

### Проверка типов

```python
x = 5

# Узнать тип объекта
print(type(x))                  # <class 'int'>

# Проверить, является ли экземпляром
print(isinstance(x, int))       # True
print(isinstance(x, object))    # True (всё является object!)

# Проверить наследование
print(issubclass(int, object))  # True
print(issubclass(bool, int))    # True
```

### Создание классов динамически

```python
# Создать класс во время выполнения
def create_class(name, attrs):
    return type(name, (), attrs)

Dog = create_class('Dog', {
    'sound': 'Woof!',
    'bark': lambda self: print(self.sound)
})

dog = Dog()
dog.bark()              # Woof!
```

### Метаклассы (продвинутое)

```python
# Создание своего метакласса
class MyMeta(type):
    def __new__(cls, name, bases, dct):
        # Добавить атрибут ко всем классам
        dct['created_by'] = 'MyMeta'
        return super().__new__(cls, name, bases, dct)

class MyClass(metaclass=MyMeta):
    pass

print(MyClass.created_by)   # 'MyMeta'
```

---

## 📊 СРАВНИТЕЛЬНАЯ ТАБЛИЦА

|Вопрос|object|type|
|---|---|---|
|Что это?|Базовый класс|Метакласс|
|Для чего?|Наследование объектов|Создание классов|
|Тип|`type`|`type`|
|Родитель|`type`|`object`|
|Создаёт|-|Все классы|
|От кого наследуются|Все классы|Все метаклассы|

```python
# Доказательства
type(object)                # <class 'type'>
type(type)                  # <class 'type'>
isinstance(type, object)    # True
isinstance(object, type)    # True
```

---

## 💡 КЛЮЧЕВЫЕ КОНЦЕПЦИИ

### 1. Всё — это объект

```python
# Даже числа, строки, функции
print(isinstance(5, object))            # True
print(isinstance("hello", object))      # True
print(isinstance(lambda x: x, object))  # True
print(isinstance(int, object))          # True (даже классы!)
```

### 2. Всё имеет тип

```python
# И тип — это type (или его подкласс)
print(type(5).__name__)         # 'int'
print(type(int).__name__)       # 'type'
print(type(type).__name__)      # 'type'
```

### 3. Иерархия наследования

```python
# Проверка цепочки наследования
print(issubclass(bool, int))    # True
print(issubclass(int, object))  # True
print(issubclass(bool, object)) # True

# bool → int → object
```

### 4. type vs isinstance

```python
x = 5

# type() возвращает точный класс
print(type(x) == int)           # True
print(type(x) == object)        # False

# isinstance() проверяет цепочку наследования
print(isinstance(x, int))       # True
print(isinstance(x, object))    # True
```

---

## 🔍 ПРАКТИЧЕСКИЕ СОВЕТЫ

### Когда использовать type?

```python
# 1. Узнать тип объекта
x = [1, 2, 3]
print(type(x))                  # <class 'list'>

# 2. Проверка конкретного типа (без наследования)
if type(x) == list:
    print("Это именно список")

# 3. Создание классов динамически
MyClass = type('MyClass', (), {'value': 42})
```

### Когда использовать isinstance?

```python
# 1. Проверка с учётом наследования
x = True
print(isinstance(x, bool))      # True
print(isinstance(x, int))       # True (bool наследуется от int!)

# 2. Проверка нескольких типов
if isinstance(x, (int, float)):
    print("Это число")

# 3. В большинстве случаев лучше type()
```

### Когда использовать issubclass?

```python
# Проверка наследования между классами
print(issubclass(bool, int))    # True
print(issubclass(list, object)) # True

# Проверка своих классов
class Dog:
    pass

class Husky(Dog):
    pass

print(issubclass(Husky, Dog))   # True
```

---

## 🎓 РЕЗЮМЕ

**Запомни главное:**

1. **object** — прародитель всех **объектов** (экземпляров)
2. **type** — прародитель всех **классов**
3. Они **взаимозависимы**: type создаёт object, а type наследуется от object
4. Эта пара образует **фундамент Python** — всё остальное строится поверх них

```python
# Доказательство взаимосвязи
print(f"type(object) = {type(object)}")         # type
print(f"type(type) = {type(type)}")             # type
print(f"isinstance(type, object) = {isinstance(type, object)}")   # True
print(f"isinstance(object, type) = {isinstance(object, type)}")   # True
```

**Практический вывод:**

- В 99% случаев используй `isinstance()` для проверки типов
- `type()` используй когда нужен точный тип без наследования
- Метаклассы (type) — для продвинутых сценариев

## 🔗 Связанные темы

- [[02 — 🧬 type, object, классы и экземпляры]]
- [[Объяснение Замкнутый круг type и object]]
- [[Шаг за шагом]]
