---
tags:
  - python
  - типизация
  - generics
  - typevar
уровень: продвинутый
---

# 🧠 TypeVar и Generic

## 📌 Коротко

> `TypeVar` и `Generic` позволяют описывать обобщённый код: функции и классы, которые работают с разными типами, но при этом сохраняют связь между входом и выходом.

---

## Содержание

- [[#🎯 Зачем нужен TypeVar|Зачем нужен TypeVar]]
- [[#🔁 Один и тот же тип на входе и выходе|Один и тот же тип на входе и выходе]]
- [[#🧩 Несколько обобщённых типов|Несколько обобщённых типов]]
- [[#🔒 Ограничения и bound|Ограничения и bound]]
- [[#🏛 Generic для классов|Generic для классов]]
- [[#🆕 Новый синтаксис Python 3.12|Новый синтаксис Python 3.12]]

---

## 🎯 Зачем нужен TypeVar

`Any` говорит: "здесь может быть что угодно". Но часто нам нужно выразить более сильную мысль:

- на вход может прийти любой тип;
- и на выходе должен быть этот же тип.

Для этого используют `TypeVar`.

```python
from typing import TypeVar

T = TypeVar("T")
```

---

## 🔁 Один и тот же тип на входе и выходе

```python
from typing import TypeVar

T = TypeVar("T")


def identity(arg: T) -> T:
    return arg
```

Если передан `int`, вернётся `int`. Если передан `str`, вернётся `str`.

Это главное отличие от `Any`.

### Пример с последовательностью

```python
from collections.abc import Sequence
from typing import TypeVar

T = TypeVar("T")


def to_list(seq: Sequence[T]) -> list[T]:
    return list(seq)
```

---

## 🧩 Несколько обобщённых типов

```python
from typing import TypeVar

T1 = TypeVar("T1")
T2 = TypeVar("T2")


def to_tuple(arg1: T1, arg2: T2) -> tuple[T1, T2]:
    return arg1, arg2
```

Так удобно выражать связь между несколькими разными параметрами.

---

## 🔒 Ограничения и bound

### Ограниченный набор типов

```python
from typing import TypeVar

T = TypeVar("T", str, bytes)
```

Теперь `T` может быть только `str` или `bytes`.

### `bound`

```python
from typing import TypeVar

T = TypeVar("T", bound=str)
```

Это означает: `str` или любой его подтип.

`bound` полезен и с пользовательскими классами, и с протоколами.

```python
from typing import Protocol, TypeVar


class SupportsAdd(Protocol):
    def __add__(self, value):
        ...


T = TypeVar("T", bound=SupportsAdd)
```

---

## 🔒 Constraints vs bound

Это один из самых важных моментов в теме generics: constraints, ound и обычный Union похожи внешне, но означают разное.

### Ограничения через TypeVar(..., ...)

`python
from typing import TypeVar

T = TypeVar("T", int, str)

def pair(a: T, b: T) -> tuple[T, T]:
    return a, b
`

Здесь T может быть только int или str, но в рамках одного вызова это должен быть один и тот же согласованный тип.

- pair(1, 2) — корректно
- pair("a", "b") — корректно
- pair(1, "b") — некорректно

### ound

`python
from typing import TypeVar

T = TypeVar("T", bound=int)
`

ound означает: допустим базовый тип и любые его подтипы. Это не список разрешённых разных типов, а верхняя граница совместимости.

### Обычный Union

`python
def show(value: int | str) -> None:
    print(value)
`

Здесь нет идеи "один и тот же тип на нескольких позициях". Просто каждое отдельное значение может быть либо int, либо str.

### Новый синтаксис Python 3.12

Старой записи

`python
T = TypeVar("T", int, str)
`

соответствует запись

`python
def pair[T: (int, str)](a: T, b: T) -> tuple[T, T]:
    return a, b
`

Это именно constraints, а не Union.

---
## 🏛 Generic для классов

Если обобщённость нужна не функции, а классу, используют `Generic`.

```python
from typing import Generic, TypeVar

T = TypeVar("T", int, float)


class Box(Generic[T]):
    def __init__(self, value: T) -> None:
        self.value = value

    def get_value(self) -> T:
        return self.value
```

Можно создавать объект так:

```python
box1 = Box(42)
box2 = Box[int](42)
```

### Несколько параметров типа

```python
from typing import Generic, TypeVar

K = TypeVar("K")
V = TypeVar("V")


class Pair(Generic[K, V]):
    def __init__(self, key: K, value: V) -> None:
        self.key = key
        self.value = value
```

---

## 📊 Быстрая справка

| Инструмент | Для чего нужен | Пример |
| --- | --- | --- |
| `TypeVar("T")` | любой согласованный тип | `def f(x: T) -> T` |
| `TypeVar("T1"), TypeVar("T2")` | несколько независимых типов | `tuple[T1, T2]` |
| `TypeVar("T", str, bytes)` | ограничение набором типов | только `str` или `bytes` |
| `TypeVar("T", bound=Base)` | базовый тип или подтип | `bound=str` |
| `Generic[T]` | обобщённый класс | `class Box(Generic[T])` |

---

## 🆕 Новый синтаксис Python 3.12

В Python 3.12 generics стали короче.

### Обобщённая функция

```python
from collections.abc import Sequence


def to_list[T](seq: Sequence[T]) -> list[T]:
    return list(seq)
```

### Ограничение

```python
def only_even[T: int](seq: list[T]) -> list[T]:
    return [n for n in seq if n % 2 == 0]
```

### Несколько типов

```python
def to_tuple[T1, T2](arg1: T1, arg2: T2) -> tuple[T1, T2]:
    return arg1, arg2
```

### Обобщённый класс

```python
class Box[T]:
    def __init__(self, value: T) -> None:
        self.value = value
```

---

## 💡 Практические замечания

- Используй `TypeVar`, когда важно сохранить связь типов, а не просто "принять что угодно".
- Для словарей часто удобно использовать `K` и `V`.
- Не усложняй generics там, где достаточно обычной аннотации.
- Новый синтаксис Python 3.12 приятнее, но старый всё ещё важен для чтения существующего кода.

---

## ⚠️ Частые ошибки

### ❌ Использовать `Any` вместо связанного типа

Если вход и выход должны быть одного типа, `TypeVar` лучше.

### ❌ Слишком широко использовать `bound`

Если фактически ты всегда работаешь с конкретным типом, иногда проще честно указать его.

### ❌ Думать, что generic автоматически решает несовместимые операции

Если обобщённый тип допускает несовместимые операции, анализатор может ругаться вполне справедливо.

---

## 🔗 Связанные темы

- [[00 — 🏠 Главная]]
- [[03 — 🧩 Типы модуля typing]]
- [[04 — 🔗 Протоколы, Sequence и Callable]]
- [[06 — 🎯 ParamSpec и типизация декораторов]]

