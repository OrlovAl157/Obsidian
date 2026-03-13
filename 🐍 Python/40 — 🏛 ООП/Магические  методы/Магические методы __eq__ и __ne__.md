---
tags:
  - python
  - тема/ооп
  - тема/магические_методы
  - статус/завершён
уровень: средний
принцип_ООП:
  - Полиморфизм
---

# ⚖️ Сравнение объектов

## 📌 Коротко

> Операторы сравнения (`==`, `!=`, `<`, `>`, `<=`, `>=`) реализуются через магические методы. По умолчанию `==` работает как `is` (сравнение по идентичности). Декоратор `@total_ordering` позволяет не реализовывать все методы вручную — достаточно `__eq__()` и одного из методов сравнения.

---

## 📖 Теория

### `__eq__()` и `__ne__()` — равенство и неравенство

По умолчанию `==` сравнивает объекты по **идентичности** (как `is`):

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p1 = Point(1, 2)
p2 = Point(1, 2)

print(p1 == p2)   # → False  (разные объекты, хотя атрибуты одинаковые)
print(p1 == p1)   # → True   (один и тот же объект)
```

Чтобы изменить логику сравнения — определяем `__eq__()`. Выражение `p1 == p2` равносильно `p1.__eq__(p2)`:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if isinstance(other, Point):
            return self.x == other.x and self.y == other.y
        return NotImplemented

p1 = Point(1, 2)
p2 = Point(1, 2)
p3 = Point(3, 4)

print(p1 == p2)   # → True
print(p1 == p3)   # → False
```

> 💡 Если определён `__eq__()`, то `__ne__()` (оператор `!=`) Python реализует **автоматически** как его отрицание. Однако если нужна нестандартная логика — можно определить `__ne__()` вручную.

> ⚠️ Обратное не работает: при реализованном `__ne__()` метод `__eq__()` автоматически **не** реализуется.

---

### Константа `NotImplemented`

Когда Python выполняет `p1 == p2`, он действует по следующему алгоритму:

1. Вызывает `p1.__eq__(p2)`
2. Если результат `True` или `False` — это и есть ответ
3. Если результат `NotImplemented` — вызывает `p2.__eq__(p1)`
4. Если и второй вернул `NotImplemented` — сравнение происходит **по идентичности** (`is`)

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if isinstance(other, Point):
            return self.x == other.x and self.y == other.y
        return NotImplemented       # ← правильный способ для неподдерживаемых типов

p1 = Point(1, 2)

print(p1 == (1, 2))   # → False  (tuple вернёт NotImplemented → сравнение по is)
print(p1 == None)     # → False
```

> 💡 Рекомендуется возвращать именно `NotImplemented` (а не `False`) для неподдерживаемых типов — это даёт Python шанс попробовать вызвать метод у второго объекта.

> ⚠️ Важный нюанс: два `NotImplemented` не всегда дают `False`. Если оба объекта одного типа и `__eq__()` не определён — Python сравнивает **по идентичности**. Поэтому `p1 == p1` даёт `True` (один объект), а `p1 == p2` даёт `False` (разные объекты), даже если `__eq__()` напрямую возвращает `NotImplemented`.

```python
class Point:
    def __init__(self, color):
        self.color = color

p1 = Point('black')
p2 = Point('black')

print(p1 == p1)        # → True   (тот же объект → is → True)
print(p1 == p2)        # → False  (разные объекты → is → False)
print(p1.__eq__(p2))   # → NotImplemented  (прямой вызов метода — без fallback)
```

---

### `__lt__()` и `__gt__()` — строгое сравнение

Для операторов `<` и `>` реализуем методы `__lt__()` и `__gt__()`:

```python
class Fruit:
    def __init__(self, name, mass):
        self.name = name
        self.mass = mass

    def __eq__(self, other):
        if isinstance(other, Fruit):
            return self.mass == other.mass
        return NotImplemented

    def __lt__(self, other):
        if isinstance(other, Fruit):
            return self.mass < other.mass    # fruit1 < fruit2
        return NotImplemented

    def __gt__(self, other):
        if isinstance(other, Fruit):
            return self.mass > other.mass    # fruit1 > fruit2
        return NotImplemented

fruit1 = Fruit('банан', 150)
fruit2 = Fruit('яблоко', 180)

print(fruit1 < fruit2)   # → True
print(fruit1 > fruit2)   # → False
```

> 💡 Если реализован только `__lt__()`, то `fruit1 > fruit2` Python вычислит как `fruit2 < fruit1` автоматически. Но явное определение всех методов — хорошая практика.

---

### `__le__()` и `__ge__()` — нестрогое сравнение

Для операторов `<=` и `>=`:

```python
class Fruit:
    def __init__(self, name, mass):
        self.name = name
        self.mass = mass

    def __eq__(self, other):
        if isinstance(other, Fruit):
            return self.mass == other.mass
        return NotImplemented

    def __le__(self, other):
        if isinstance(other, Fruit):
            return self.mass <= other.mass   # fruit1 <= fruit2
        return NotImplemented

    def __ge__(self, other):
        if isinstance(other, Fruit):
            return self.mass >= other.mass   # fruit1 >= fruit2
        return NotImplemented
```

---

### Декоратор `@total_ordering`

Реализовывать все 6 методов сравнения вручную — многословно. Декоратор `@total_ordering` из модуля `functools` решает эту проблему: достаточно определить `__eq__()` и **один** из методов (`__lt__`, `__le__`, `__gt__`, `__ge__`) — остальные он реализует сам.

```python
from functools import total_ordering

@total_ordering
class Fruit:
    def __init__(self, name, mass):
        self.name = name
        self.mass = mass

    def __repr__(self):
        return f'Fruit({repr(self.name)}, {self.mass})'

    def __eq__(self, other):
        if isinstance(other, Fruit):
            return self.mass == other.mass
        return NotImplemented

    def __lt__(self, other):
        if isinstance(other, Fruit):
            return self.mass < other.mass
        return NotImplemented

fruits = [Fruit('яблоко', 180), Fruit('груша', 160), Fruit('банан', 150)]

print(sorted(fruits))    # → [Fruit('банан', 150), Fruit('груша', 160), Fruit('яблоко', 180)]
print(min(fruits))       # → Fruit('банан', 150)
print(max(fruits))       # → Fruit('яблоко', 180)
```

> 💡 После реализации операторов сравнения объекты можно сортировать через `sorted()`, и находить `min()` / `max()`.

---

## 📝 Примечания

### `__eq__()` вызывается при проверке `in`

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if isinstance(other, Point):
            return self.x == other.x and self.y == other.y
        return False

p = Point(2, 2)
points = [Point(1, 1), Point(2, 2), Point(3, 3)]

print(p in points)   # → True  (Python перебирает список и вызывает __eq__() для каждого)
```

### Если `__lt__()` не определён — будет исключение

```python
class Fruit:
    def __init__(self, name, mass):
        self.name = name
        self.mass = mass

fruit1 = Fruit('банан', 150)
fruit2 = Fruit('яблоко', 180)

print(fruit1 < fruit2)   # → TypeError: '<' not supported between instances of 'Fruit' and 'Fruit'
```

---

## 💻 Быстрая шпаргалка

|Метод|Оператор|EN|RU|
|---|---|---|---|
|`__eq__(self, other)`|`==`|equal|равно|
|`__ne__(self, other)`|`!=`|not equal|не равно|
|`__lt__(self, other)`|`<`|less than|меньше|
|`__le__(self, other)`|`<=`|less than or equal|меньше или равно|
|`__gt__(self, other)`|`>`|greater than|больше|
|`__ge__(self, other)`|`>=`|greater than or equal|больше или равно|

```python
from functools import total_ordering

@total_ordering
class MyClass:
    def __init__(self, value):
        self.value = value

    def __eq__(self, other):
        if isinstance(other, MyClass):
            return self.value == other.value
        return NotImplemented

    def __lt__(self, other):
        if isinstance(other, MyClass):
            return self.value < other.value
        return NotImplemented

    # __ne__, __gt__, __le__, __ge__ — @total_ordering реализует автоматически
```

**🧮 Алгоритм `NotImplemented` при `p1 == p2`:**

1. `p1.__eq__(p2)` → если `True`/`False` → результат
2. → если `NotImplemented` → пробует `p2.__eq__(p1)`
3. → если снова `NotImplemented` → сравнение **по идентичности** (`is`)

---

## 🔗 Связанные темы

- [[00 Магические методы]]
- [[Тонкости сравнений]]
- [[Все про Хеш-функции]]
- [[03 — ⚡ Множества (set) 🔹]]

## ❓ Вопросы / Непонятное

- ...