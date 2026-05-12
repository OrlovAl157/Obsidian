---
tags:
  - python
  - тема/ооп
  - тема/магические_методы
  - статус/завершён
уровень: средний
---

# 📞 Вызываемые объекты и `__call__`

## 📌 Коротко

> Вызываемый объект (callable) — любой объект, который можно вызвать с помощью `()`. Это не только функции, но и классы, и экземпляры классов с реализованным `__call__()`. Проверить — `callable(obj)`.

---

## 📖 Теория

---

### Что такое вызываемый объект?

В Python вызываемым называется любой объект, который можно **вызвать с помощью круглых скобок** `()`:

```python
def add(a, b):
    return a + b

mul = lambda a, b: a * b

print(add(1, 2))       # → 3
print(mul(1, 2))       # → 2
print(len([1, 2, 3]))  # → 3
```

Функции — очевидный пример вызываемых объектов. Но вызываемыми являются и **классы**:

```python
print(map)      # → <class 'map'>
print(range)    # → <class 'range'>
print(filter)   # → <class 'filter'>
print(zip)      # → <class 'zip'>
```

> 💡 Мы часто называем `map()`, `range()`, `zip()` функциями — это не совсем точно. Правильнее — **вызываемые объекты**. Классы и функции используют один и тот же синтаксис вызова `()`, поэтому это различие часто несущественно. Важно лишь то, что объект *можно вызвать*.

Таким образом, вызываемыми в Python являются:
- функции и лямбды
- классы (вызов создаёт экземпляр)
- методы
- экземпляры классов с `__call__()`

---

### Магический метод `__call__()`

Определив в классе метод `__call__()`, мы делаем его экземпляры **вызываемыми**. Вызов `obj()` равносилен `obj.__call__()`:

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __call__(self):
        print('Меня зовут', self.name)


cat = Cat('Кемаль')
cat()   # → Меня зовут Кемаль
        # равнозначно cat.__call__()
```

`__call__()` — обычный метод экземпляра, может принимать любые аргументы:

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __call__(self, speech):
        print('Меня зовут', self.name, 'и я делаю', speech)


cat = Cat('Кемаль')
cat('Мяу')      # → Меня зовут Кемаль и я делаю Мяу
cat('Мяяяяяу')  # → Меня зовут Кемаль и я делаю Мяяяяяу
```

---

### Сценарии использования `__call__()`

---

#### Сценарий 1 — Альтернатива замыканиям

Замыкание — функция внутри функции, которая запоминает переменные внешней функции:

```python
# Замыкание:
def line_generator(k, b):
    def func(x):
        return k * x + b
    return func

line_func1 = line_generator(2, 5)   # y = 2x + 5
print(line_func1(10))               # → 25
```

Тот же результат через класс с `__call__()` — более явно и с доступом к состоянию:

```python
# Класс с __call__:
class LineGenerator:
    def __init__(self, k, b):
        self.k = k
        self.b = b

    def __call__(self, x):
        return self.k * x + self.b


line_func1 = LineGenerator(2, 5)    # y = 2x + 5
line_func2 = LineGenerator(-6, 9)   # y = -6x + 9

print(line_func1(10))   # → 25    (2*10 + 5)
print(line_func2(4))    # → -15   (-6*4 + 9)
```

> 💡 Объекты `line_func1` и `line_func2` не являются функциями, но ведут себя как функции — их можно передавать туда, где ожидается callable.

---

#### Сценарий 2 — Декоратор на основе класса

Обычный декоратор — функция, возвращающая функцию:

```python
# Декоратор-функция:
def uppercase_decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs).upper()
    return wrapper

@uppercase_decorator
def greet(name):
    return f'Привет, {name}'

print(greet('Кемаль'))   # → ПРИВЕТ, КЕМАЛЬ
```

Тот же декоратор через класс с `__call__()`:

```python
# Декоратор-класс:
class UppercaseDecorator:
    def __init__(self, func):
        self.func = func          # запоминаем декорируемую функцию

    def __call__(self, *args, **kwargs):
        return self.func(*args, **kwargs).upper()   # расширяем функционал


@UppercaseDecorator
def greet(name):
    return f'Привет, {name}'

print(greet('Кемаль'))   # → ПРИВЕТ, КЕМАЛЬ
```

> 💡 Принцип прост: в `__init__` запоминаем функцию, в `__call__` расширяем её поведение. Именно так работают `property`, `classmethod`, `staticmethod` внутри Python — они тоже классы, а не функции.

---

#### Сценарий 3 — Изменение состояния объекта

Вызов экземпляра может быть интуитивно понятным способом **изменить его состояние**:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __call__(self, x, y):
        self.x, self.y = x, y   # изменяем координаты через вызов


point = Point(1, 2)
print(point.x, point.y)   # → 1 2

point(3, 4)                # "вызываем" точку для изменения координат
print(point.x, point.y)   # → 3 4
```

---

## 📝 Примечания

### Проверка вызываемости — `callable()`

Чтобы проверить, является ли объект вызываемым, используется `callable()` — под капотом проверяет наличие `__call__`:

```python
print(callable(int))    # → True   (класс)
print(callable(len))    # → True   (функция)
print(callable(1))      # → False  (число — не вызываемое)

class Cat:
    def __call__(self): pass

cat = Cat()
print(callable(cat))    # → True   (экземпляр с __call__)
print(callable(Cat))    # → True   (сам класс тоже callable)
```

> 💡 `hasattr(obj, '__call__')` тоже работает, но `callable()` — более идиоматичный способ.

### Что на самом деле является классами, а не функциями

Многие встроенные "функции" Python — на самом деле классы:

```python
print(map)        # → <class 'map'>
print(filter)     # → <class 'filter'>
print(zip)        # → <class 'zip'>
print(range)      # → <class 'range'>
print(enumerate)  # → <class 'enumerate'>
print(reversed)   # → <class 'reversed'>
print(property)   # → <class 'property'>
print(classmethod) # → <class 'classmethod'>
print(staticmethod) # → <class 'staticmethod'>

print(len)        # → <built-in function len>  ← вот это действительно функция
```

> 💡 Это не ошибка в именовании — это демонстрация утиной типизации. Раз вызывается через `()` — значит, callable. Является ли это функцией или классом — часто просто деталь реализации.

### Замыкание vs `__call__` — когда что выбрать

| | Замыкание | Класс с `__call__` |
|---|---|---|
| Читаемость | Компактнее | Явнее, понятнее |
| Состояние | Скрыто во внешней функции | Доступно через `self` |
| Расширяемость | Сложнее | Проще — добавляем методы |
| Когда использовать | Простая логика | Сложное состояние, нужны методы |

---

## 💻 Быстрая шпаргалка

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, x):
        return x * self.factor


double = Multiplier(2)
triple = Multiplier(3)

print(double(5))     # → 10
print(triple(5))     # → 15
print(callable(double))   # → True

# Передаём как функцию — работает везде, где ожидается callable:
numbers = [1, 2, 3, 4, 5]
print(list(map(double, numbers)))   # → [2, 4, 6, 8, 10]
```

```python
# Декоратор-класс — шаблон:
class MyDecorator:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        # до вызова
        result = self.func(*args, **kwargs)
        # после вызова
        return result

@MyDecorator
def my_function():
    pass
```

---

## 🔗 Связанные темы

- [[00 Магические методы]]
- [[00 — Декоратор]]
- [[03 — 📦 Атрибуты объектов и классов]]

## ❓ Вопросы / Непонятное

- ...
