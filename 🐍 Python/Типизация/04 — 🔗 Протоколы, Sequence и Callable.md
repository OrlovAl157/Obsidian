---
tags:
  - python
  - типизация
  - protocol
  - collections_abc
уровень: средний
---

# 🔗 Протоколы, Sequence и Callable

## 📌 Коротко

> В Python типы можно описывать не только через наследование, но и через ожидаемое поведение объекта. Для этого используют абстрактные классы из `collections.abc`, протоколы `Protocol` и специальные интерфейсы вроде `Callable` и `SupportsAbs`.

---

## Содержание

- [[#🏛 Номинальная и структурная типизация|Номинальная и структурная типизация]]
- [[#📚 collections.abc в аннотациях|collections.abc в аннотациях]]
- [[#📊 Таблица интерфейсов collections.abc|Таблица интерфейсов collections.abc]]
- [[#⚙️ Supports... протоколы|Supports... протоколы]]
- [[#🧩 Протоколы Protocol|Протоколы Protocol]]
- [[#📞 Callable|Callable]]
- [[#✅ runtime_checkable|runtime_checkable]]

---

## 🏛 Номинальная и структурная типизация

### Номинальная типизация

Тип считается совместимым, если есть нужное наследование.

```python
class Animal:
    pass


class Cat(Animal):
    pass
```

Если функция ждёт `Animal`, то `Cat` подходит как наследник.

### Структурная типизация

Тип считается совместимым, если объект реализует нужный интерфейс.

Идея близка к утиной типизации:

- если объект умеет нужные операции, он подходит;
- наследование может быть вообще не связано.

---

## 📚 collections.abc в аннотациях

Вместо слишком конкретных типов вроде `list[int]` иногда лучше описывать именно возможности объекта.

Например:

```python
from collections.abc import Sequence


def first(seq: Sequence[int]) -> int:
    return seq[0]
```

Функции не важно, список это или кортеж. Ей важно, что объект ведёт себя как последовательность.

Полезные интерфейсы:

- `Iterable` — объект можно перебирать;
- `Iterator` — объект можно итерировать с `next()`;
- `Sequence` — индексируемая последовательность;
- `Callable` — вызываемый объект.

---

## 📊 Таблица интерфейсов collections.abc

| Класс | Наследуется от | Ключевые методы | Вид типизации |
| --- | --- | --- | --- |
| `Container` | — | `__contains__()` | Структурная |
| `Hashable` | — | `__hash__()` | Структурная |
| `Iterable` | — | `__iter__()` | Структурная |
| `Iterator` | `Iterable` | `__next__()` | Структурная |
| `Reversible` | `Iterable` | `__reversed__()` | Структурная |
| `Generator` | `Iterator` | генераторный интерфейс | Структурная |
| `Sized` | — | `__len__()` | Структурная |
| `Callable` | — | `__call__()` | Структурная |
| `Collection` | `Sized`, `Iterable`, `Container` | комбинированный интерфейс | Структурная |
| `Sequence` | `Reversible`, `Collection` | `__getitem__()`, `__len__()` | Номинальная |
| `MutableSequence` | `Sequence` | `__setitem__()`, `__delitem__()`, `insert()` | Номинальная |
| `Set` | `Collection` | интерфейс множества | Номинальная |
| `MutableSet` | `Set` | `add()`, `discard()` | Номинальная |
| `Mapping` | `Collection` | `__getitem__()` | Номинальная |
| `MutableMapping` | `Mapping` | `__setitem__()`, `__delitem__()` | Номинальная |

Важно: объект должен соответствовать не только самому интерфейсу, но и его родителям.

---

## ⚙️ Supports... протоколы

В `typing` есть готовые маленькие протоколы для отдельных операций.

| Тип | Какой метод требует |
| --- | --- |
| `SupportsAbs` | `__abs__()` |
| `SupportsBytes` | `__bytes__()` |
| `SupportsComplex` | `__complex__()` |
| `SupportsFloat` | `__float__()` |
| `SupportsIndex` | `__index__()` |
| `SupportsInt` | `__int__()` |
| `SupportsRound` | `__round__()` |

Пример:

```python
from typing import SupportsAbs


def distance(value: SupportsAbs) -> None:
    print(abs(value))
```

---

## 🧩 Протоколы Protocol

Если готового интерфейса нет, его можно описать через `Protocol`.

```python
from typing import Protocol


class Animal(Protocol):
    def sound(self) -> str:
        ...

    def move(self) -> str:
        ...
```

Теперь функция может ожидать не конкретный класс, а любой объект с такими методами.

```python
from typing import Protocol


class Animal(Protocol):
    def sound(self) -> str:
        ...

    def move(self) -> str:
        ...


class Cat:
    def sound(self) -> str:
        return "мяу"

    def move(self) -> str:
        return "кот движется"


def get_sound(animal: Animal) -> None:
    print(animal.sound())
```

`Cat` не наследуется от `Animal`, но всё равно подходит по структуре.

---

## 📞 Callable

`Callable` используют для аннотации вызываемых объектов: функций, лямбд, экземпляров с `__call__`.

```python
from collections.abc import Callable


def apply(func: Callable[[int], int], value: int) -> int:
    return func(value)
```

Примеры:

- `Callable[[int], int]` — принимает `int`, возвращает `int`
- `Callable[[str, float], bool]` — принимает два аргумента, возвращает `bool`
- `Callable[[], None]` — без аргументов, возвращает `None`
- `Callable[..., int]` — произвольная сигнатура, возвращает `int`

---

## ✅ runtime_checkable

По умолчанию `Protocol` нужен в первую очередь для статической типизации.

Если хочется использовать `isinstance()` и `issubclass()`, нужен декоратор `@runtime_checkable`.

```python
from typing import Protocol, runtime_checkable


@runtime_checkable
class Animal(Protocol):
    def sound(self) -> str:
        ...

    def move(self) -> str:
        ...
```

Важно: такая проверка смотрит только на наличие методов, а не на точность их сигнатур.

---

## 💡 Практические замечания

- Если функции важна именно возможность итерироваться, используй `Iterable`, а не `list`.
- Если нужен доступ по индексу, смотри в сторону `Sequence`.
- Если требуется просто вызываемый объект, используй `Callable`.
- Если нужен свой интерфейс без жёсткого наследования, описывай `Protocol`.

---

## ⚠️ Частые ошибки

### ❌ Аннотировать слишком конкретно

```python
def first(seq: list[int]) -> int:
    return seq[0]
```

Если достаточно интерфейса последовательности, лучше:

```python
from collections.abc import Sequence


def first(seq: Sequence[int]) -> int:
    return seq[0]
```

### ❌ Путать protocol-проверку и обычное наследование

Объект может подходить `Protocol`, вообще не наследуясь от него.

### ❌ Ждать от `runtime_checkable` полной проверки типов

Он не анализирует точные аннотации параметров.

---

## 🔗 Связанные темы

- [[00 — 🏠 Главная]]
- [[03 — 🧩 Типы модуля typing]]
- [[05 — 🧠 TypeVar и Generic]]
- [[06 — 🎯 ParamSpec и типизация декораторов]]
