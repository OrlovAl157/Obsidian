---
tags:
  - python
  - типизация
  - paramspec
  - decorators
уровень: продвинутый
---

# 🎯 ParamSpec и типизация декораторов

## 📌 Коротко

> `ParamSpec` нужен тогда, когда важно сохранить сигнатуру чужой функции, например, в декораторах. Он позволяет передать дальше те же позиционные и именованные аргументы, а `Concatenate` — добавить к ним новые.

---

## Содержание

- [[#🎯 Зачем нужен ParamSpec|Зачем нужен ParamSpec]]
- [[#❌ Почему одного Callable часто мало|Почему одного Callable часто мало]]
- [[#✅ Декоратор с ParamSpec|Декоратор с ParamSpec]]
- [[#➕ Concatenate|Concatenate]]
- [[#🆕 Python 3.12|Python 3.12]]

---

## 🎯 Зачем нужен ParamSpec

Если мы типизируем декоратор, нам обычно важно выразить:

- какую функцию он принимает;
- что обёртка принимает те же аргументы;
- что возвращаемое значение тоже согласовано.

`Callable` сам по себе не всегда умеет это выразить удобно.

```python
from typing import ParamSpec

P = ParamSpec("P")
```

Обычно вместе с ним используют `TypeVar` для возвращаемого значения.

---

## ❌ Почему одного Callable часто мало

```python
from collections.abc import Callable
from typing import Any


def doubled(func: Callable) -> Callable:
    def wrapper(*args, **kwargs) -> Any:
        result = func(*args, **kwargs)
        return result * 2
    return wrapper
```

Такой код слишком расплывчатый:

- неясно, какие аргументы у `func`;
- неясно, какие аргументы у `wrapper`;
- `Any` делает возвращаемый тип размытым.

---

## ✅ Декоратор с ParamSpec

```python
import functools

from collections.abc import Callable
from typing import ParamSpec, TypeVar

P = ParamSpec("P")
T = TypeVar("T", int, float)


def doubled(func: Callable[P, T]) -> Callable[P, T]:
    @functools.wraps(func)
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> T:
        result = func(*args, **kwargs)
        return result * 2

    return wrapper
```

Теперь видно:

- `func` — вызываемый объект с параметрами `P`;
- `wrapper` принимает те же `args` и `kwargs`;
- возвращаемое значение связано с `T`.

---

## ➕ Concatenate

`Concatenate` нужен, когда декоратор добавляет обязательный параметр.

```python
import functools

from collections.abc import Callable
from typing import Concatenate


def decorator[T, **P](func: Callable[Concatenate[int, P], T]) -> Callable[Concatenate[int, P], T]:
    @functools.wraps(func)
    def wrapper(user_id: int, *args: P.args, **kwargs: P.kwargs) -> T:
        return func(user_id, *args, **kwargs)

    return wrapper
```

Идея здесь такая:

- первым аргументом всегда идёт `user_id: int`;
- после него остаются все остальные параметры исходной функции.

---

## 📊 Краткая справка

| Инструмент | Для чего нужен | Где чаще встречается |
| --- | --- | --- |
| `ParamSpec` | сохранить параметры функции | декораторы, обёртки |
| `P.args` | позиционные аргументы исходной функции | внутри wrapper |
| `P.kwargs` | именованные аргументы исходной функции | внутри wrapper |
| `Callable[P, T]` | функция с параметрами `P` и return `T` | сигнатуры декораторов |
| `Concatenate` | добавить обязательные параметры | декораторы с доп. аргументом |

---

## 🆕 Python 3.12

Новый синтаксис выглядит короче:

```python
import functools

from collections.abc import Callable


def doubled[T: (int, float), **P](func: Callable[P, T]) -> Callable[P, T]:
    @functools.wraps(func)
    def wrapper(*args: P.args, **kwargs: P.kwargs) -> T:
        result = func(*args, **kwargs)
        return result * 2

    return wrapper
```

Здесь `**P` в квадратных скобках и есть `ParamSpec`.

---

## 💡 Практические замечания

- `ParamSpec` нужен не в каждой функции, а в основном в инфраструктурном коде.
- Если обёртка просто принимает `*args, **kwargs`, это ещё не значит, что она хорошо типизирована.
- Для декораторов `ParamSpec` часто полезнее, чем просто `Callable[..., Any]`.
- `Concatenate` удобен, когда декоратор гарантированно добавляет параметр в начало сигнатуры.

---

## ⚠️ Частые ошибки

### ❌ Использовать `Callable[..., Any]` там, где важна сигнатура

Так теряется главное преимущество типизации декоратора.

### ❌ Указывать только `P.args` или только `P.kwargs`

Обычно эти части работают вместе.

### ❌ Пытаться сохранить `T`, когда декоратор реально меняет тип результата

Если возвращаемое значение по смыслу уже другое, сигнатура должна это честно отражать.

---

## 🔗 Связанные темы

- [[🐍 Python/35 — 🏷 Типизация (type hints)/00 — 🏠 Главная]]
- [[04 — 🔗 Протоколы, Sequence и Callable]]
- [[05 — 🧠 TypeVar и Generic]]
- [[07 — 📋 TypedDict, NewType и прикладные типы]]
