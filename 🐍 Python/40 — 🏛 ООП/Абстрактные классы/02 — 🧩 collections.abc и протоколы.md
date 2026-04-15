---
tags:
  - python
  - ООП
  - абстрактные_классы
  - статус/завершён
---

# 🧩 collections.abc и протоколы

## 📌 Коротко

> `collections.abc` — модуль с абстрактными базовыми классами для проверки интерфейсов. Реализуешь минимум абстрактных методов → получаешь mixin методы бесплатно.

---

## 📖 Два вида методов

**Абстрактные методы** — то что ты **обязан** реализовать сам. Без них экземпляр не создать.

**Mixin методы** — то что Python **достраивает автоматически** на основе твоих абстрактных методов.

> [!tip] Аналогия
> Как химические реакции: огонь + вода = пар, вода + земля = грязь.
> Чем мощнее методы ты реализовал — тем больше "реакций" (mixin методов) Python построит из них.
> Например, из одного `__len__()` ничего не построить. А из `__getitem__()` + `__len__()` Python уже может построить `__iter__()`, `__contains__()`, `__reversed__()` и другие.

---

## 📖 Таблица классов

| Класс | Наследуется от | Абстрактные методы | Mixin методы |
|---|---|---|---|
| `Container` | — | `__contains__()` | — |
| `Hashable` | — | `__hash__()` | — |
| `Iterable` | — | `__iter__()` | — |
| `Iterator` | Iterable | `__next__()` | `__iter__()` |
| `Reversible` | Iterable | `__reversed__()` | — |
| `Generator` | Iterator | `send()`, `throw()` | `close()`, `__iter__()`, `__next__()` |
| `Sized` | — | `__len__()` | — |
| `Callable` | — | `__call__()` | — |
| `Collection` | Sized, Iterable, Container | `__contains__()`, `__iter__()`, `__len__()` | — |
| `Sequence` | Reversible, Collection | `__getitem__()`, `__len__()` | `__contains__()`, `__iter__()`, `__reversed__()`, `index()`, `count()` |
| `MutableSequence` | Sequence | `__getitem__()`, `__setitem__()`, `__delitem__()`, `__len__()`, `insert()` | все Sequence + `append()`, `reverse()`, `extend()`, `pop()`, `remove()`, `__iadd__()` |
| `Set` | Collection | `__contains__()`, `__iter__()`, `__len__()` | `__le__()`, `__lt__()`, `__eq__()`, `__ne__()`, `__gt__()`, `__ge__()`, `__and__()`, `__or__()`, `__sub__()`, `__xor__()`, `isdisjoint()` |
| `MutableSet` | Set | `__contains__()`, `__iter__()`, `__len__()`, `add()`, `discard()` | все Set + `clear()`, `pop()`, `remove()`, `__ior__()`, `__iand__()`, `__ixor__()`, `__isub__()` |
| `Mapping` | Collection | `__getitem__()`, `__iter__()`, `__len__()` | `__contains__()`, `keys()`, `items()`, `values()`, `get()`, `__eq__()`, `__ne__()` |
| `MutableMapping` | Mapping | `__getitem__()`, `__setitem__()`, `__delitem__()`, `__iter__()`, `__len__()` | все Mapping + `pop()`, `popitem()`, `clear()`, `update()`, `setdefault()` |

---

## 📖 Логика именования

`Mutable` = **изменяемый**. Неизменяемые типы получают базовый класс, изменяемые — `Mutable`-версию:

| Тип | Sequence | MutableSequence | Mapping | MutableMapping |
|---|---|---|---|---|
| `list` | ✅ | ✅ | ❌ | ❌ |
| `tuple` | ✅ | ❌ | ❌ | ❌ |
| `str` | ✅ | ❌ | ❌ | ❌ |
| `dict` | ❌ | ❌ | ✅ | ✅ |

---

## 📖 Sequence vs Mapping — разная механика __getitem__

Оба имеют `__getitem__()`, но смысл принципиально разный:

```python
# Sequence — индекс это ПОЗИЦИЯ (целое число)
lst = [10, 20, 30]
lst[0]    # 10 — иди на нулевую позицию в памяти

# Mapping — ключ это ХЕШ
d = {'a': 1, 'b': 2}
d['a']    # 1 — найди по хешу ключа
d[0]      # KeyError! — нет такого ключа
```

Поэтому `dict` не является `Sequence` — несмотря на наличие `__getitem__()`. Механика поиска разная: у последовательности — по целочисленной позиции, у отображения — по хешу.

---

## 📖 isinstance() и issubclass() с abc

```python
from collections.abc import Sequence, MutableSequence

# list — изменяемая последовательность
print(isinstance([1, 2, 3], Sequence))        # True
print(isinstance([1, 2, 3], MutableSequence)) # True
print(issubclass(list, Sequence))             # True
print(issubclass(list, MutableSequence))      # True

# tuple — неизменяемая последовательность
print(isinstance((1, 2, 3), Sequence))        # True
print(isinstance((1, 2, 3), MutableSequence)) # False
print(issubclass(tuple, MutableSequence))     # False

# str — неизменяемая последовательность
print(isinstance('beegeek', Sequence))        # True
print(isinstance('beegeek', MutableSequence)) # False

# dict — изменяемое отображение, не последовательность
from collections.abc import Mapping, MutableMapping
print(isinstance({}, MutableSequence))        # False
print(isinstance({}, Mapping))                # True
print(isinstance({}, MutableMapping))         # True
```

---

## 📖 Пример: ListBasedSet

Реализуем неизменяемое множество на основе списка.
Такая реализация медленнее обычного `set`, но потребляет меньше памяти.

Достаточно реализовать 3 метода — остальные придут бесплатно:

```python
from collections.abc import Set

class ListBasedSet(Set):
    def __init__(self, iterable):
        lst = []
        for value in iterable:
            if value not in lst:
                lst.append(value)
        self.elements = lst

    def __iter__(self):
        return iter(self.elements)

    def __contains__(self, value):
        return value in self.elements

    def __len__(self):
        return len(self.elements)

    def __repr__(self):
        return f'{type(self).__name__}({self.elements})'


s1 = ListBasedSet('abcdef')
s2 = ListBasedSet('defghi')

print(s1 | s2)  # объединение  — ListBasedSet(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i'])
print(s1 & s2)  # пересечение — ListBasedSet(['d', 'e', 'f'])
print(s1 - s2)  # разность    — ListBasedSet(['a', 'b', 'c'])
```

Реализовали только `__iter__()`, `__contains__()`, `__len__()` — а `|`, `&`, `-`, `^`, `<=`, `>=`, `isdisjoint()` и другие работают **автоматически** через mixin методы `Set`.

---

## 📖 isinstance() vs hasattr() — почему abc лучше

```python
# ❌ Старый способ — некрасиво и нетипизировано
size = None
if hasattr(size, '__len__'):
    size = len(size)

# ✅ Новый способ — читаемо и типизировано
from collections.abc import Sized

size = None
if isinstance(size, Sized):
    size = len(size)
```

---

## 📖 Утиная типизация vs abc

Python **не требует** наследования от abc для работы протоколов — достаточно иметь нужные методы (утиная типизация):

```python
class MyClass:
    def __getitem__(self, i):
        return i * 2

    def __len__(self):
        return 10

obj = MyClass()
obj[3]    # 6 — работает без наследования от Sequence!
len(obj)  # 10 — тоже работает
```

Но наследование от `collections.abc` даёт два важных бонуса:

1. **Проверка** через `isinstance()` / `issubclass()`
2. **Mixin методы** бесплатно — не нужно писать `__contains__()`, `__iter__()` и другие вручную

---

## 📖 Когда что использовать

> [!tip] Правило выбора (из курса)
> - Нужно **сильно изменить** поведение встроенного типа → наследуй от `collections.abc`
> - Класс **немного отличается** от встроенного → наследуй от `list`, `str`, `dict`, `UserList`, `UserString`, `UserDict`

```python
# Нужно полностью своё поведение → abc
from collections.abc import MutableSequence

class MyList(MutableSequence):
    def __getitem__(self, i): ...
    def __setitem__(self, i, v): ...
    def __delitem__(self, i): ...
    def __len__(self): ...
    def insert(self, i, v): ...
    # append, extend, pop, remove и др. — БЕСПЛАТНО через mixin!

# Небольшое отличие от list → UserList
from collections import UserList

class StringList(UserList):
    def append(self, item):
        self.data.append(str(item))
```

---

## 📖 Наследник обязан реализовать все абстрактные методы

```python
from collections.abc import Set

class BrokenSet(Set):
    def __iter__(self):
        return iter([])

    def __contains__(self, value):
        return False

    # __len__ не реализован!

broken = BrokenSet()
# TypeError: Can't instantiate abstract class BrokenSet
# with abstract method __len__
```

> [!warning] Важно
> Ошибка возникает при создании **экземпляра**. Забытый абстрактный метод сразу виден в сообщении об ошибке.

---

## 📊 Quick Reference

| Интерфейс | Минимум методов для реализации |
|---|---|
| `Iterable` | `__iter__()` |
| `Iterator` | `__next__()`, `__iter__()` |
| `Sized` | `__len__()` |
| `Collection` | `__contains__()`, `__iter__()`, `__len__()` |
| `Sequence` | `__getitem__()`, `__len__()` |
| `MutableSequence` | `__getitem__()`, `__setitem__()`, `__delitem__()`, `__len__()`, `insert()` |
| `Set` | `__contains__()`, `__iter__()`, `__len__()` |
| `Mapping` | `__getitem__()`, `__iter__()`, `__len__()` |
| `MutableMapping` | `__getitem__()`, `__setitem__()`, `__delitem__()`, `__iter__()`, `__len__()` |

---

## 🔗 Связанные темы

- [[01 — 🔒 Абстрактные классы (abc)]]
- [[04 — 📦 Наследование от dict и list]]
- [[03 — 🏗 Наследование от неизменяемых типов]]
- [[Абстракция]] — принцип ООП
