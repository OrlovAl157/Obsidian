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

---

## 📖 Таблица классов

| Класс | Наследуется от | Абстрактные методы | Mixin методы |
|---|---|---|---|
| `Container` | — | `__contains__()` | — |
| `Hashable` | — | `__hash__()` | — |
| `Iterable` | — | `__iter__()` | — |
| `Iterator` | Iterable | `__next__()` | `__iter__()` |
| `Sized` | — | `__len__()` | — |
| `Callable` | — | `__call__()` | — |
| `Collection` | Sized, Iterable, Container | `__contains__()`, `__iter__()`, `__len__()` | — |
| `Sequence` | Reversible, Collection | `__getitem__()`, `__len__()` | `__contains__()`, `__iter__()`, `__reversed__()`, `index()`, `count()` |
| `MutableSequence` | Sequence | + `__setitem__()`, `__delitem__()`, `insert()` | все Sequence + `append()`, `extend()`, `pop()`, `remove()` |
| `Set` | Collection | `__contains__()`, `__iter__()`, `__len__()` | `__or__()`, `__and__()`, `__sub__()`, `isdisjoint()` ... |
| `MutableSet` | Set | + `add()`, `discard()` | все Set + `clear()`, `pop()`, `remove()` |
| `Mapping` | Collection | `__getitem__()`, `__iter__()`, `__len__()` | `keys()`, `values()`, `items()`, `get()` ... |
| `MutableMapping` | Mapping | + `__setitem__()`, `__delitem__()` | все Mapping + `pop()`, `update()`, `setdefault()` |

---

## 📖 Логика именования

- `Mutable` = **изменяемый**. Всё просто:
  - `list` → `MutableSequence` ✅ (можно менять)
  - `tuple` → только `Sequence` ❌ (нельзя менять)
  - `str` → только `Sequence` ❌ (нельзя менять)
  - `dict` → `MutableMapping` ✅ (можно менять)

---

## 📖 Sequence vs Mapping — разная механика

Оба имеют `__getitem__()`, но смысл разный:

```python
# Sequence — индекс это ПОЗИЦИЯ (целое число)
lst = [10, 20, 30]
lst[0]    # 10 — иди на нулевую позицию

# Mapping — ключ это ХЕШ
d = {'a': 1, 'b': 2}
d['a']    # 1 — найди по хешу
d[0]      # KeyError! — нет такого ключа
```

Поэтому `dict` — не `Sequence`, даже несмотря на наличие `__getitem__()`.

---

## 📖 isinstance() и issubclass() с abc

```python
from collections.abc import Sequence, MutableSequence, Mapping, MutableMapping

# list — изменяемая последовательность
print(isinstance([1, 2, 3], Sequence))        # True
print(isinstance([1, 2, 3], MutableSequence)) # True

# tuple — неизменяемая последовательность
print(isinstance((1, 2, 3), Sequence))        # True
print(isinstance((1, 2, 3), MutableSequence)) # False

# str — неизменяемая последовательность
print(isinstance('abc', Sequence))            # True
print(isinstance('abc', MutableSequence))     # False

# dict — изменяемое отображение
print(isinstance({}, Mapping))                # True
print(isinstance({}, MutableMapping))         # True
print(isinstance({}, MutableSequence))        # False
```

---

## 📖 Пример: ListBasedSet

Реализуем неизменяемое множество на основе списка. Достаточно 3 методов — остальные придут бесплатно:

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

print(s1 | s2)  # объединение  — БЕСПЛАТНО
print(s1 & s2)  # пересечение — БЕСПЛАТНО
print(s1 - s2)  # разность    — БЕСПЛАТНО
```

Реализовали 3 метода → получили `|`, `&`, `-`, `^`, `<=`, `>=` и другие автоматически.

---

## 📖 isinstance() vs hasattr() — почему abc лучше

```python
# ❌ Старый способ — некрасиво
if hasattr(obj, '__len__'):
    size = len(obj)

# ✅ Новый способ — типизировано и читаемо
from collections.abc import Sized

if isinstance(obj, Sized):
    size = len(obj)
```

---

## 📖 Утиная типизация vs abc

Python не **требует** наследования от abc для работы протоколов — достаточно иметь нужные методы:

```python
class MySeq:
    def __getitem__(self, i): ...
    def __len__(self): ...

# Работает без наследования от Sequence!
my = MySeq()
my[0]   # ✅
len(my) # ✅
```

Но наследование от `collections.abc` даёт два бонуса:
1. **Проверка** через `isinstance()` / `issubclass()`
2. **Mixin методы** бесплатно

---

## 📖 Когда использовать collections.abc

```python
# Нужно сильно изменить поведение → наследуй от abc
from collections.abc import MutableSequence

class MyList(MutableSequence):
    def __getitem__(self, i): ...
    def __setitem__(self, i, v): ...
    def __delitem__(self, i): ...
    def __len__(self): ...
    def insert(self, i, v): ...
    # append, extend, pop и др. — бесплатно!

# Немного отличается от базового → наследуй от list/dict/UserList
from collections import UserList

class StringList(UserList):
    def append(self, item):
        self.data.append(str(item))
```

---

## 📊 Quick Reference

| Тип | Sequence | MutableSequence | Mapping | MutableMapping |
|---|---|---|---|---|
| `list` | ✅ | ✅ | ❌ | ❌ |
| `tuple` | ✅ | ❌ | ❌ | ❌ |
| `str` | ✅ | ❌ | ❌ | ❌ |
| `dict` | ❌ | ❌ | ✅ | ✅ |

---

## 🔗 Связанные темы

- [[01 — 🔒 Абстрактные классы (abc)]]
- [[04 — 📦 Наследование от dict и list]]
- [[Абстракция]] — принцип ООП
