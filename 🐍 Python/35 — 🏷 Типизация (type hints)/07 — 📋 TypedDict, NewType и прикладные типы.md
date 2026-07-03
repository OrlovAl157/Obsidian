---
tags:
  - python
  - типизация
  - typeddict
  - newtype
уровень: средний
---

# 📋 TypedDict, NewType и прикладные типы

## 📌 Коротко

> Некоторые типы из `typing` особенно полезны в прикладном коде: `TypedDict` помогает типизировать словари фиксированной формы, а `NewType` — различать одинаковые по базе, но разные по смыслу значения.

---

## Содержание

- [[#🏷 NewType|NewType]]
- [[#🗂 TypedDict|TypedDict]]
- [[#📌 NamedTuple|NamedTuple]]
- [[#🏷 Annotated|Annotated]]
- [[#❓ NotRequired и Required|NotRequired и Required]]
- [[#⚙️ total=False|total=False]]
- [[#🧪 Runtime и реальные примеры|Runtime и реальные примеры]]
- [[#💡 Когда это особенно полезно|Когда это особенно полезно]]

---

## 🏷 NewType

`NewType` позволяет создать именованный тип на основе уже существующего.

```python
from typing import NewType

UserId = NewType("UserId", int)
```

Теперь можно отличать:

- просто `int`;
- `UserId` как отдельное понятие в коде.

### Пример

```python
from typing import NewType

UserId = NewType("UserId", int)


def load_user(user_id: UserId) -> None:
    pass
```

Такой код передаёт смысл лучше, чем голый `int`.

---

## 🗂 TypedDict

`TypedDict` нужен, когда словарь должен иметь заранее известные ключи.

```python
from typing import TypedDict


class User(TypedDict):
    name: str
    surname: str
```

Теперь функция может ожидать именно такую структуру:

```python
def full_name(user: User) -> str:
    return user["name"] + " " + user["surname"]
```

### Что это даёт

- понятную форму словаря;
- подсказки по ключам;
- проверку обязательных полей.

---

## 📌 NamedTuple

`NamedTuple` — это типизированный именованный кортеж. В отличие от `TypedDict`, он ведёт себя как кортеж, а не как словарь.

```python
from typing import NamedTuple


class Point(NamedTuple):
    x: int
    y: int
```

Такой объект позволяет обращаться к значениям:

- по индексу: `point[0]`
- по имени: `point.x`

Коротко:

- `NamedTuple` — больше похож на кортеж
- `TypedDict` — больше похож на словарь

---

## 🏷 Annotated

`Annotated` позволяет дополнить тип метаданными.

```python
from typing import Annotated
```

Сам по себе Python не использует эти метаданные напрямую, но они часто важны для экосистемы вроде Pydantic, FastAPI и других библиотек, которые читают аннотации и строят на них валидацию или схемы.

---

## ❓ NotRequired и Required

По умолчанию все ключи в `TypedDict` обязательны.

Если отдельный ключ может отсутствовать:

```python
from typing import NotRequired, TypedDict


class User(TypedDict):
    name: str
    surname: NotRequired[str]
```

Если почти всё необязательно, но один ключ обязателен:

```python
from typing import Required, TypedDict


class User(TypedDict, total=False):
    name: Required[str]
    surname: str
```

---

## ⚙️ total=False

Если все ключи можно сделать необязательными, используют `total=False`.

```python
from typing import TypedDict


class User(TypedDict, total=False):
    name: str
    surname: str
```

Теперь словарь может содержать:

- оба ключа;
- только один;
- ни одного.

---

## 📊 Краткая справка

| Тип | Для чего нужен | Пример |
| --- | --- | --- |
| `NewType` | новый смысл поверх старого типа | `UserId = NewType("UserId", int)` |
| `TypedDict` | словарь фиксированной формы | `class User(TypedDict): ...` |
| `NotRequired` | необязательный ключ | `surname: NotRequired[str]` |
| `Required` | обязательный ключ при `total=False` | `name: Required[str]` |
| `total=False` | все ключи необязательны по умолчанию | `class User(TypedDict, total=False)` |

---

## 🧪 Runtime и реальные примеры

### `TypedDict` в runtime

Важно помнить: во время выполнения `TypedDict` ведёт себя как обычный `dict`.

```python
from typing import TypedDict


class User(TypedDict):
    name: str
    surname: str
```

Для Python это всё ещё словарь, просто с более точным описанием структуры для анализатора типов.

### `TypedDict` для API-ответа

```python
from typing import TypedDict


class UserResponse(TypedDict):
    id: int
    name: str
    email: str
```

### `NewType` для разных ID

```python
from typing import NewType

UserId = NewType("UserId", int)
OrderId = NewType("OrderId", int)


def load_user(user_id: UserId) -> None:
    pass
```

Теперь анализатор сможет поймать ситуацию, когда перепутали разные идентификаторы.

```python
load_user(UserId(1))   # корректно
# load_user(OrderId(1))  # ошибка типов
```

---

## 💡 Когда это особенно полезно

### `NewType`

Полезен для сущностей, которые имеют одинаковую базу, но разный смысл:

- `UserId`
- `OrderId`
- `Email`
- `Meters`

### `TypedDict`

Полезен для:

- JSON-подобных структур;
- настроек;
- API-ответов;
- промежуточных словарей между слоями приложения.

---

## ⚠️ Частые ошибки

### ❌ Использовать обычный `dict[str, Any]` для фиксированной структуры

Если форма словаря известна заранее, `TypedDict` обычно лучше.

### ❌ Считать `NewType` новым реальным runtime-классом

Он нужен прежде всего для типизации и смысла, а не как полноценная новая структура данных.

### ❌ Читать необязательный ключ как обязательный

Если поле `NotRequired`, обращаться к нему лучше через `.get()` или после проверки.

---

## 🔗 Связанные темы

-[[🐍 Python/35 — 🏷 Типизация (type hints)/00 — 🏠 Главная]]]
- [[03 — 🧩 Типы модуля typing]]
- [[05 — 🧠 TypeVar и Generic]]
- [[06 — 🎯 ParamSpec и типизация декораторов]]

