-----

## tags: [python, типизация, Union, Optional, typing]
difficulty: beginner

# 🔄 Union, Optional и типы из typing

> **Union** — тип для значений, которые могут быть одного из нескольких типов. **Optional** — частный случай Union для типа и None. **Literal** — когда значение может быть только одним из конкретных значений. **Final** — для констант. **ClassVar** — для атрибутов класса. Эти типы из модуля `typing` расширяют возможности аннотаций для более сложных сценариев.

## Содержание

- [[#Справка|Справка]]
- [[#💡 Union — несколько типов|Union — несколько типов]]
- [[#❓ Optional — тип или None|Optional — тип или None]]
- [[#🎲 Any — неизвестный тип|Any — неизвестный тип]]
- [[#📝 Literal — конкретные значения|Literal — конкретные значения]]
- [[#🔒 Final — неизменяемые значения|Final — неизменяемые значения]]
- [[#👥 ClassVar — атрибуты класса|ClassVar — атрибуты класса]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Тип             |Синтаксис        |Пример                    |Когда                     |
|----------------|-----------------|--------------------------|--------------------------|
|**Union**       |`Union[T1, T2]`  |`Union[int, str]`         |Несколько возможных типов |
|**Optional**    |`Optional[T]`    |`Optional[str]`           |Тип или None              |
|**Python 3.10+**|`T1 | T2`        |`int | str`               |Новый синтаксис Union     |
|**Any**         |`Any`            |`def func(x: Any)`        |Когда тип неизвестен      |
|**Literal**     |`Literal[v1, v2]`|`Literal['on', 'off']`    |Только конкретные значения|
|**Final**       |`Final[T]`       |`MAX: Final[int] = 100`   |Константы                 |
|**ClassVar**    |`ClassVar[T]`    |`count: ClassVar[int] = 0`|Атрибуты класса           |
|**NoReturn**    |`NoReturn`       |`def quit() -> NoReturn:` |Функция не возвращает     |

-----

## 💡 Union — несколько типов

Когда функция может принять несколько типов:

```python
from typing import Union

def process(value: Union[int, str]) -> Union[int, str]:
    if isinstance(value, int):
        return value * 2
    return value.upper()

result1 = process(5)        # 10 (int)
result2 = process("hello")  # "HELLO" (str)
```

**Python 3.10+** — можно писать проще:

```python
def process(value: int | str) -> int | str:
    if isinstance(value, int):
        return value * 2
    return value.upper()
```

Union указывает, что функция может работать с одним из типов, но не обязательно с одним и тем же каждый раз.

-----

## ❓ Optional — тип или None

Часто функция может вернуть значение или `None` (если не найдено, произошла ошибка и т.д.):

```python
from typing import Optional

def find_user(user_id: int) -> Optional[str]:
    """Возвращает имя пользователя или None"""
    users = {1: "Alice", 2: "Bob"}
    return users.get(user_id)

def parse_int(text: str) -> Optional[int]:
    """Пытается парсить строку в число"""
    try:
        return int(text)
    except ValueError:
        return None
```

**Это эквивалентно:**

```python
def find_user(user_id: int) -> str | None:  # Python 3.10+
    users = {1: "Alice", 2: "Bob"}
    return users.get(user_id)

def find_user(user_id: int) -> Union[str, None]:  # Старый стиль
    users = {1: "Alice", 2: "Bob"}
    return users.get(user_id)
```

**Всегда проверяй на None перед использованием:**

```python
user = find_user(1)
if user is not None:
    print(user.upper())  # ✅ Безопасно
else:
    print("User not found")
```

-----

## 🎲 Any — неизвестный тип

Когда совсем неизвестно, какой тип может быть:

```python
from typing import Any

def debug_print(value: Any) -> None:
    """Печатает любое значение для отладки"""
    print(f"Value: {value}, Type: {type(value)}")

def merge_dicts(dict1: Any, dict2: Any) -> dict:
    """Объединяет два словаря (если они словари)"""
    if isinstance(dict1, dict) and isinstance(dict2, dict):
        return {**dict1, **dict2}
    raise TypeError("Both arguments must be dicts")
```

**⚠️ Any убирает всю пользу от type hints!** Используй только в исключительных случаях.

-----

## 📝 Literal — конкретные значения

Когда параметр может быть только одним из нескольких конкретных значений:

```python
from typing import Literal

def set_log_level(level: Literal['DEBUG', 'INFO', 'WARNING', 'ERROR']) -> None:
    """Устанавливает уровень логирования"""
    print(f"Log level set to {level}")

def fetch_data(format: Literal['json', 'csv', 'xml']) -> str:
    """Получает данные в одном из форматов"""
    if format == 'json':
        return '{"data": "value"}'
    elif format == 'csv':
        return 'col1,col2'
    else:
        return '<data></data>'

# Правильное использование
set_log_level('DEBUG')      # ✅ OK
fetch_data('json')          # ✅ OK
fetch_data('yaml')          # ❌ mypy: ошибка — 'yaml' не в Literal
```

-----

## 🔒 Final — неизменяемые значения

Для констант, которые не должны меняться:

```python
from typing import Final

# На уровне модуля
MAX_USERS: Final = 100
PI: Final[float] = 3.14159
API_TIMEOUT: Final[int] = 30

# В классе
class Config:
    DATABASE_URL: Final[str] = "postgresql://localhost/mydb"
    DEBUG_MODE: Final[bool] = False

    def setup(self):
        # ❌ mypy скажет "ошибка" — нельзя переопределять Final
        Config.DEBUG_MODE = True
```

-----

## 👥 ClassVar — атрибуты класса

Когда атрибут принадлежит классу, а не экземплярам:

```python
from typing import ClassVar

class Animal:
    # Это атрибут класса — общий для всех животных
    species_count: ClassVar[int] = 0
    
    # Это атрибут экземпляра — у каждого животного своё имя
    name: str
    
    def __init__(self, name: str):
        self.name = name
        Animal.species_count += 1

dog = Animal("Rex")
cat = Animal("Whiskers")

print(Animal.species_count)  # 2 — класс
print(dog.name)              # "Rex" — экземпляр
```

ClassVar показывает разницу:

- `ClassVar[int]` — атрибут класса, не экземпляра
- `name: str` — атрибут экземпляра

-----

## 💻 Примеры

### Пример 1: Union для фильтрации

```python
from typing import Union

def get_value(data: Union[dict, list], key: Union[str, int]) -> Union[str, int, None]:
    """Получает значение из словаря или списка"""
    if isinstance(data, dict) and isinstance(key, str):
        return data.get(key)
    elif isinstance(data, list) and isinstance(key, int):
        return data[key] if 0 <= key < len(data) else None
    return None

# Использование
result1 = get_value({'name': 'Alice'}, 'name')  # 'Alice'
result2 = get_value([1, 2, 3], 1)               # 2
result3 = get_value({'name': 'Alice'}, 0)       # None
```

### Пример 2: Optional для поиска

```python
from typing import Optional

class User:
    def __init__(self, user_id: int, name: str):
        self.user_id = user_id
        self.name = name

class UserRepository:
    def __init__(self):
        self.users = [
            User(1, "Alice"),
            User(2, "Bob"),
        ]
    
    def find_by_id(self, user_id: int) -> Optional[User]:
        """Ищет пользователя по ID"""
        for user in self.users:
            if user.user_id == user_id:
                return user
        return None

# Использование
repo = UserRepository()
user = repo.find_by_id(1)

if user is not None:
    print(f"Found: {user.name}")
else:
    print("User not found")
```

### Пример 3: Literal для конфигурации

```python
from typing import Literal

class LoggerConfig:
    def __init__(self, level: Literal['DEBUG', 'INFO', 'WARNING', 'ERROR']):
        valid_levels = ['DEBUG', 'INFO', 'WARNING', 'ERROR']
        if level not in valid_levels:
            raise ValueError(f"Invalid level: {level}")
        self.level = level
    
    def log(self, message: str) -> None:
        print(f"[{self.level}] {message}")

# Использование
config = LoggerConfig('DEBUG')
config.log("Starting app")

# ❌ mypy скажет ошибка перед запуском
# config = LoggerConfig('INVALID')
```

### Пример 4: Final для глобальных констант

```python
from typing import Final

# Глобальные константы
MAX_CONNECTIONS: Final[int] = 100
API_KEY: Final[str] = "secret123"
DATABASE_URL: Final[str] = "postgresql://localhost/db"

class AppConfig:
    # Константы класса
    VERSION: Final[str] = "1.0.0"
    AUTHOR: Final[str] = "Company"
    
    # ❌ Это вызовет ошибку при анализе типов
    # VERSION = "2.0.0"  # Попытка переопределить Final
```

-----

## ⚠️ Частые ошибки

```python
from typing import Union, Optional, Any, Literal, Final

# ❌ Union с одним типом (не имеет смысла)
value: Union[int]  # Просто используй int!

# ✅ Правильно
value: int

# ❌ Optional с Union (лишнее)
result: Optional[Union[int, str]]  # Избыточно!

# ✅ Правильно
result: Union[int, str, None]
# или Python 3.10+
result: int | str | None

# ❌ Забыли проверить None перед использованием
user: Optional[str] = get_user()
print(user.upper())  # ❌ Может быть None!

# ✅ Правильно
user: Optional[str] = get_user()
if user is not None:
    print(user.upper())

# ❌ Literal с переменной (работает только с литералами)
mode = 'fast'
def process(m: Literal[mode]) -> None:  # ❌ Ошибка!
    pass

# ✅ Правильно
def process(m: Literal['fast', 'slow']) -> None:
    pass

# ❌ Any везде (теряется весь смысл типов)
def process(data: Any) -> Any:
    return data

# ✅ Будь конкретнее
def process(data: dict[str, int]) -> list[int]:
    return list(data.values())
```

-----

## ✅ Главные правила

✅ **Union для множественных типов** — когда переменная может быть разных типов  
✅ **Optional для “может быть None”** — очень частый случай  
✅ **Python 3.10+ синтаксис** — `int | str` вместо `Union[int, str]`  
✅ **Всегда проверяй Optional на None** — перед использованием  
✅ **Any только в крайних случаях** — он убирает пользу от типов  
✅ **Literal для конкретных значений** — когда список вариантов известен  
✅ **Final для констант** — показывает намерение не менять значение  
✅ **ClassVar для атрибутов класса** — отличает от атрибутов экземпляра

-----

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[02 — 📦 Generics и контейнеры]]
- [[04 — ✅ Type Checking с mypy и pyright]]
- [[05 — 🎨 TypeVar и Generic]]