---
tags:
  - python
  - типизация
  - typing
уровень: средний
---

# 🧩 Типы модуля typing

## 📌 Коротко

> Модуль `typing` добавляет типы, которых не хватает базовым аннотациям: объединения типов, необязательные значения, литералы, псевдонимы, константы и атрибуты класса.

---

## Содержание

- [[#📦 Когда нужен typing|Когда нужен typing]]
- [[#🔀 Union и оператор | |Union и оператор |]]
- [[#⭕ Optional|Optional]]
- [[#🌍 Any|Any]]
- [[#⛔ NoReturn|NoReturn]]
- [[#🚫 Never|Never]]
- [[#🏷 TypeAlias|TypeAlias]]
- [[#🎯 Literal|Literal]]
- [[#🔒 Final|Final]]
- [[#🏛 ClassVar|ClassVar]]
- [[#↪️ Forward reference и оператор | |Forward reference и оператор |]]
- [[#🆕 Современный синтаксис|Современный синтаксис]]

---

## 📦 Когда нужен typing

Базовых типов вроде `int`, `str` и `list[int]` часто недостаточно. На практике нужно уметь выразить:

- значение одного из нескольких типов;
- значение или `None`;
- любые данные;
- константы;
- строго допустимые литералы;
- атрибуты класса.

---

## 🔀 Union и оператор |

Если значение может быть одного из нескольких типов, используют `Union` или современный оператор `|`.

```python
def add_or_concatenate(a: int | float | str, b: int | float | str) -> int | float | str:
    return a + b
```

Старый синтаксис:

```python
from typing import Union

def add_or_concatenate(a: Union[int, float, str], b: Union[int, float, str]) -> Union[int, float, str]:
    return a + b
```

### Важно

- порядок типов не имеет значения;
- одинаковые типы повторять бессмысленно;
- вложенный `Union` можно разворачивать.

---

## ⭕ Optional

`Optional[T]` означает `T | None`.

```python
def add_ten(lst: list[int] | None = None) -> None:
    if lst is None:
        lst = []
    lst.append(10)
```

Старый вариант:

```python
from typing import Optional

def add_ten(lst: Optional[list[int]] = None) -> None:
    ...
```

---

## 🌍 Any

`Any` означает: сюда можно передать что угодно, и отсюда может прийти что угодно.

```python
from typing import Any


def identity(arg: Any) -> Any:
    return arg
```

### Когда полезен

- при постепенной типизации;
- на границе со слабо типизированным кодом;
- во временных переходных местах.

### Минус

Чрезмерный `Any` почти выключает пользу типизации.

---

## ⛔ NoReturn

`NoReturn` используют для функций, которые гарантированно не возвращают значение.

```python
from typing import NoReturn


def stop() -> NoReturn:
    raise RuntimeError("no way")
```

Не путай:

- `-> None` означает "функция возвращает `None`";
- `-> NoReturn` означает "функция вообще не доходит до возврата".

---

## 🚫 Never

`Never` появился в Python 3.11+ и по смыслу используется как современная альтернатива `NoReturn` в местах, где нужно показать невозможность нормального возврата значения.

```python
from typing import Never


def fail() -> Never:
    raise RuntimeError("stop")
```

На практике в подобных примерах ты можешь встретить и `NoReturn`, и `Never`.

---

## 🏷 TypeAlias

Если аннотация длинная и повторяется, её удобно вынести в псевдоним.

```python
NumberOrStr = int | float | str


def add_or_concatenate(a: NumberOrStr, b: NumberOrStr) -> NumberOrStr:
    return a + b
```

Старый вариант:

```python
from typing import TypeAlias

NumberOrStr: TypeAlias = int | float | str
```

Новый вариант в Python 3.12+:

```python
type NumberOrStr = int | float | str
```

---

## 🎯 Literal

`Literal` нужен, когда допускаются не любые значения типа, а только конкретные.

```python
from typing import Literal


def set_gender(gender: Literal["male", "female"]) -> None:
    pass
```

Это уже не просто `str`, а только две конкретные строки.

### Допустимые значения для `Literal`

| Тип значения | Можно использовать в `Literal` |
| --- | --- |
| `None` | Да |
| `int` | Да |
| `bool` | Да |
| `str` | Да |
| `bytes` | Да |
| `Enum` | Да |

---

## 🔒 Final

`Final` используют для констант и значений, которые не должны переопределяться.

```python
from typing import Final


PI: Final = 3.14159
```

Можно уточнить тип:

```python
PI: Final[float] = 3.14159
```

---

## 🏛 ClassVar

`ClassVar` показывает, что атрибут принадлежит классу, а не экземпляру.

```python
from typing import ClassVar


class Cat:
    night_vision: ClassVar[bool] = True
```

Это помогает отличать:

- общие свойства класса;
- обычные поля экземпляра.

```python
from typing import ClassVar


class Cat:
    breed: str = "Британский"
    name: str
    night_vision: ClassVar[bool] = True
```

---

## 📊 Краткая справка

| Тип | Для чего нужен | Современная запись |
| --- | --- | --- |
| `Union` | одно из нескольких типов | `int | str` |
| `Optional` | тип или `None` | `str | None` |
| `Any` | любой тип | `Any` |
| `NoReturn` | функция не возвращает управление | `NoReturn` |
| `TypeAlias` | псевдоним типа | `type Alias = ...` |
| `Literal` | строго заданные значения | `Literal["a", "b"]` |
| `Final` | константа | `Final[int]` |
| `ClassVar` | атрибут класса | `ClassVar[str]` |

---

## ↪️ Forward reference и оператор |

Есть тонкий момент: запись с `|` и ссылкой на класс, объявленный позже, в старых версиях Python может требовать строковую аннотацию.

```python
class Cat:
    def play_with(self, other: "int | Cat") -> None:
        pass
```

Иногда хочется написать так:

```python
class Cat:
    def play_with(self, other: int | "Cat") -> None:
        pass
```

Но в старых версиях Python такая запись может ломаться из-за вычисления аннотаций.

### Что изменилось в Python 3.14

Начиная с Python 3.14 вычисление аннотаций стало отложенным. Из-за этого подобные записи стали работать гибче, чем раньше.

## 🆕 Современный синтаксис

Для нового кода лучше предпочитать:

- `list[int]`, а не `List[int]`;
- `str | None`, а не `Optional[str]`;
- `int | float`, а не `Union[int, float]`;
- `type Alias = ...`, а не `TypeAlias`, если версия Python позволяет.

---

## ⚠️ Частые ошибки

### ❌ Слишком много `Any`

```python
def process(data: Any) -> Any:
    return data
```

Это допустимо, но часто бесполезно.

### ❌ Путать `None` и `NoReturn`

```python
def stop() -> None:
    raise RuntimeError
```

Если функция гарантированно не возвращает управление, точнее будет `NoReturn`.

### ❌ Не отделять атрибут класса от атрибута экземпляра

`ClassVar` полезен не ради красоты, а ради ясной модели данных.

---

## 🔗 Связанные темы

- [[00 — 🏠 Главная]]
- [[01 — 📖 Основы аннотаций типов]]
- [[04 — 🔗 Протоколы, Sequence и Callable]]
- [[05 — 🧠 TypeVar и Generic]]

