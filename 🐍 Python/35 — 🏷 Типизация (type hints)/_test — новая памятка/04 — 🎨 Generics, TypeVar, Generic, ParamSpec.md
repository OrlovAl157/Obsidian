---
tags: [python, typing, generics, typevar, paramspec]
difficulty: advanced
---

# 🎨 Generics: TypeVar, Generic, ParamSpec, NewType

> [!info] Коротко
> Generics нужны, когда мы не хотим жестко привязывать код к одному типу, но хотим сохранить точную связь между входом и выходом. Это уже не просто "подсказка ради красоты", а язык для описания зависимостей между типами.

## Содержание

- [[#Справка по теме|Справка по теме]]
- [[#Зачем нужны generics|Зачем нужны generics]]
- [[#TypeVar|TypeVar]]
- [[#Ограничения и bound|Ограничения и bound]]
- [[#Generic-классы|Generic-классы]]
- [[#NewType|NewType]]
- [[#ParamSpec и декораторы|ParamSpec и декораторы]]
- [[#Concatenate|Concatenate]]
- [[#Что важно про Python 312|Что важно про Python 3.12]]
- [[#Главные правила|Главные правила]]

---

## Справка по теме

| Инструмент | Пример | Для чего |
|---|---|---|
| `TypeVar` | `T = TypeVar("T")` | связать типы между аргументом и результатом |
| `bound=` | `TypeVar("T", bound=str)` | ограничить верхней границей |
| constraints | `TypeVar("T", str, bytes)` | разрешить только набор типов |
| `Generic[T]` | `class Box(Generic[T])` | generic-класс |
| `NewType` | `UserId = NewType("UserId", int)` | логически отделить одинаковые базовые типы |
| `ParamSpec` | `P = ParamSpec("P")` | сохранить сигнатуру функции |
| `Concatenate` | `Concatenate[Request, P]` | добавить аргумент в сигнатуру |

---

## Зачем нужны generics

Если функция работает с любым типом, но должна вернуть тот же самый тип, обычных аннотаций недостаточно.

Плохой путь:

```python
def identity(x: object) -> object:
    return x
```

Формально верно, но слишком грубо. Если передать `str`, анализатор увидит только `object`.

Лучший путь:

```python
from typing import TypeVar

T = TypeVar("T")

def identity(x: T) -> T:
    return x
```

Теперь между входом и выходом сохраняется связь: если передан `str`, вернется `str`; если `list[int]`, вернется `list[int]`.

Это и есть сердце generic-подхода.

---

## TypeVar

`TypeVar` — переменная типа.

```python
from typing import TypeVar

T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]
```

Здесь мы не говорим "любой объект", а говорим: "тип элемента списка и тип результата один и тот же".

Это намного точнее и полезнее для анализа.

Можно использовать несколько переменных:

```python
K = TypeVar("K")
V = TypeVar("V")

def get_pair(key: K, value: V) -> tuple[K, V]:
    return key, value
```

---

## Ограничения и bound

Иногда `TypeVar` не должен быть совсем произвольным.

### Constraints

```python
T = TypeVar("T", str, bytes)
```

Это значит: `T` может быть только `str` или `bytes`.

Такой вариант полезен, когда поддерживаются несколько конкретных типов, и связь между ними должна сохраняться.

### Bound

```python
T = TypeVar("T", bound=str)
```

Это значит: `T` должен быть `str` или его подтипом.

Важно не путать это с произвольным "числовым" ограничением. Например, `bound=int` не означает автоматически "любой numeric". Это именно верхняя граница по иерархии типов, а не расплывчатая математическая категория.

> [!warning] Частая ошибка
> `bound=int` не делает `float` допустимым просто потому, что оба числа. Это разные типы.

---

## Generic-классы

Generics работают не только для функций, но и для классов:

```python
from typing import Generic, TypeVar

T = TypeVar("T")


class Box(Generic[T]):
    def __init__(self, value: T) -> None:
        self.value = value

    def get(self) -> T:
        return self.value
```

Использование:

```python
name_box = Box("Alice")
age_box = Box(20)
```

Теперь тип содержимого коробки известен и сохраняется.

Это удобно для:

- контейнеров;
- репозиториев;
- оберток;
- кэшей;
- очередей и других инфраструктурных классов.

---

## NewType

`NewType` полезен, когда два значения имеют один базовый тип, но разный смысл.

```python
from typing import NewType

UserId = NewType("UserId", int)
OrderId = NewType("OrderId", int)
```

Хотя оба основаны на `int`, логически это разные сущности.

Такой прием помогает не перепутать идентификаторы:

```python
def load_user(user_id: UserId) -> None:
    ...
```

Для runtime это почти ничего не меняет, но для типизации и читаемости это очень полезный барьер.

---

## ParamSpec и декораторы

`TypeVar` умеет работать с типом значения, но не с полной сигнатурой функции. Для этого нужен `ParamSpec`.

```python
from collections.abc import Callable
from typing import ParamSpec, TypeVar

P = ParamSpec("P")
R = TypeVar("R")


def log_calls(func: Callable[P, R]) -> Callable[P, R]:
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
        print("call", func.__name__)
        return func(*args, **kwargs)

    return wrapper
```

Здесь декоратор сохраняет исходную сигнатуру функции, а не размывает ее до `Callable[..., Any]`.

Это одна из самых ценных тем в современной типизации Python, потому что декораторы без `ParamSpec` часто теряют точность.

---

## Concatenate

`Concatenate` используют, когда декоратор или обертка добавляет аргумент.

Идея такая: исходная функция принимает не просто `P`, а, например, сначала `Request`, а потом все остальное из `P`.

Это уже продвинутый уровень, но полезно знать как концепт. Он особенно нужен для middleware, dependency injection и инфраструктурных оберток.

---

## Что важно про Python 3.12

В Python 3.12 синтаксис generics стал короче и приятнее. Можно описывать параметры типов прямо в объявлении функции и класса, без отдельного `TypeVar` в верхней части файла.

Это делает код компактнее, но базовую модель все равно нужно понимать: без понимания `TypeVar`, связи типов и сигнатур новая запись мало что дает.

Для заметок и практики полезно знать оба стиля:

- классический через `TypeVar`, `Generic`, `ParamSpec`;
- современный синтаксис Python 3.12+.

---

## Главные правила

- Используй `TypeVar`, когда между типами аргументов и результата есть связь.
- `object` и `Any` не заменяют generics: они почти всегда грубее.
- Не путай `constraints` и `bound`: первое задает список допустимых типов, второе верхнюю границу.
- `NewType` полезен для логически разных сущностей с одинаковой базой.
- Для декораторов и оберток `ParamSpec` обычно лучше, чем `Callable[..., Any]`.

## Связанные темы

- [[00 — 📘 Аннотации типов — основы]]
- [[01 — 🧰 typing, Union, Optional и др.]]
- [[02 — 📦 Коллекции, TypedDict и аннотации объектов]]
- [[03 — 🔗 Callable, Protocol и структурная типизация]]
- [[05 — ✅ Проверка типов, mypy, pyright и практика]]
