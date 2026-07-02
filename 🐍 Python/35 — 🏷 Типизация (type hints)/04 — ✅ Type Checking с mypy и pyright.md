-----

## tags: [python, типизация, mypy, pyright, type checking]
difficulty: intermediate

# ✅ Type Checking с mypy и pyright

> **Type checkers** — инструменты, которые анализируют код и проверяют аннотации типов **без запуска программы**. **mypy** — самый популярный и авторитетный. **pyright** — более быстрый, от Microsoft. Type checker находит ошибки типов до запуска кода. Python сам их не проверяет, но mypy/pyright помогут вам.

## Содержание

- [[#Справка|Справка]]
- [[#🚀 Установка и базовое использование|Установка и базовое использование]]
- [[#📍 Простой пример|Простой пример]]
- [[#🔴 Ошибки, которые ловит mypy|Ошибки, которые ловит mypy]]
- [[#🎯 Type narrowing|Type narrowing]]
- [[#🔕 Type ignore комментарий|Type ignore комментарий]]
- [[#⚙️ Опции mypy|Опции mypy]]
- [[#⚔️ mypy vs pyright|mypy vs pyright]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Инструмент         |Установка            |Команда                 |Скорость |Строгость   |
|-------------------|---------------------|------------------------|---------|------------|
|**mypy**           |`pip install mypy`   |`mypy file.py`          |Медленнее|Авторитетнее|
|**pyright**        |`pip install pyright`|`pyright file.py`       |Быстрее  |Хороша      |
|**Проверить папку**|-                    |`mypy .` или `pyright .`|-        |-           |
|**Со strict’ом**   |-                    |`mypy --strict file.py` |-        |Максимум    |

-----

## 🚀 Установка и базовое использование

```bash
# Установить mypy
pip install mypy

# Проверить один файл
mypy hello.py

# Проверить всю папку
mypy .

# С опциями
mypy --strict hello.py
mypy --ignore-missing-imports hello.py
```

-----

## 📍 Простой пример

```python
# main.py
def greet(name: str) -> str:
    return f"Hello, {name}!"

print(greet("Alice"))  # ✅ OK — передали str
print(greet(42))       # ❌ Error — передали int, ожидали str
```

Запускаем mypy:

```bash
$ mypy main.py
main.py:5: error: Argument 1 to "greet" has incompatible type "int"; expected "str"
Found 1 error in 1 file (checked 1 source file)
```

Mypy **нашёл ошибку**, не запуская код!

-----

## 🔴 Ошибки, которые ловит mypy

```python
from typing import Optional, Union

# ❌ Неправильный тип аргумента
def add(a: int, b: int) -> int:
    return a + b

result = add("1", "2")  # Error: Expected int, got str

# ❌ None проблемы
def get_user(user_id: int) -> Optional[str]:
    if user_id == 1:
        return "Alice"
    return None

user = get_user(1)
print(user.upper())  # Error: Optional[str] might be None

# ✅ Правильно
user = get_user(1)
if user is not None:
    print(user.upper())

# ❌ Несовместимый return type
def multiply(x: int, y: int) -> str:  # ❌ Сказали str
    return x * y  # ✅ Но вернули int

# ❌ Операция не поддерживается
def concatenate(a: int | str, b: int | str) -> str:
    return a + b  # ❌ str + int = Error!
```

-----

## 🎯 Type narrowing (сужение типов)

mypy понимает `isinstance()` и сужает типы:

```python
from typing import Union

def process(value: Union[int, str]) -> str:
    # Без сужения типа — ошибка
    # return value.upper()  # ❌ Error! int нет upper()
    
    # С сужением типа — OK
    if isinstance(value, str):
        return value.upper()  # ✅ mypy знает, что это str
    else:
        return str(value)     # ✅ Это int, преобразуем в str
```

-----

## 🔕 Type ignore комментарий

Когда mypy ошибается или вы знаете, что делаете:

```python
# ❌ mypy будет ругаться
def legacy_function():
    return some_weird_code()  # Нет типов

value: int = legacy_function()  # ❌ Error: Any is not int

# ✅ Отключить проверку для строки
value: int = legacy_function()  # type: ignore

# ✅ Отключить конкретную ошибку
value: int = legacy_function()  # type: ignore[assignment]

# ⚠️ Использовать редко! Это скрывает ошибки
```

-----

## ⚙️ Опции mypy

```bash
# Базовые опции
mypy --strict file.py              # Максимально строгая проверка
mypy --ignore-missing-imports .    # Игнорировать отсутствующие импорты
mypy --no-implicit-optional .      # Запретить неявные Optional

# Отлаживание
mypy --show-traceback file.py      # Показать traceback при ошибке
mypy --verbose file.py             # Подробный вывод
mypy --report mypy .               # Создать отчёты в папке mypy/
```

-----

## ⚔️ mypy vs pyright

|Критерий          |mypy               |pyright                  |
|------------------|-------------------|-------------------------|
|**Автор**         |Гвидо ван Россум   |Microsoft                |
|**Скорость**      |Медленнее          |Быстрее                  |
|**Строгость**     |Более консервативен|Несколько более либерален|
|**Документация**  |Лучше              |Хороша                   |
|**IDE интеграция**|VSCode, PyCharm    |VSCode, PyCharm          |
|**Рекомендация**  |Для production     |Для скорости             |

-----

## 💻 Примеры

### Пример 1: Реальная ошибка типов

```python
# database.py
from typing import Optional

class User:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

def get_user(user_id: int) -> Optional[User]:
    if user_id == 1:
        return User("Alice", 30)
    return None

def print_user_info(user: Optional[User]) -> None:
    # ❌ mypy скажет: Optional[User] might not have attribute "name"
    print(f"Name: {user.name}, Age: {user.age}")

# ✅ Правильно
def print_user_info(user: Optional[User]) -> None:
    if user is not None:
        print(f"Name: {user.name}, Age: {user.age}")
    else:
        print("User not found")
```

Запускаем:

```bash
$ mypy database.py
database.py:14: error: Item "None" has no attribute "name"
Found 1 error in 1 file
```

### Пример 2: Несовместимый return type

```python
# math_utils.py
def divide(a: float, b: float) -> float:
    if b == 0:
        return "Error: Division by zero"  # ❌ Вернули str вместо float
    return a / b
```

mypy:

```bash
$ mypy math_utils.py
math_utils.py:4: error: Incompatible return value type (got "str", expected "float")
Found 1 error in 1 file
```

**Исправление:**

```python
from typing import Union

def divide(a: float, b: float) -> Union[float, str]:
    if b == 0:
        return "Error: Division by zero"
    return a / b
```

### Пример 3: Отсутствие аннотаций

```bash
# Без флага --disallow-untyped-defs
$ mypy main.py
Success: no issues found

# С флагом
$ mypy --disallow-untyped-defs main.py
main.py:1: error: Function is missing a type annotation
Found 1 error
```

-----

## ⚠️ Частые ошибки

```python
# ❌ Думать, что Python проверяет типы при выполнении
def add(a: int, b: int) -> int:
    return a + b

add("hello", "world")  # ✅ Python выполнит это нормально!
                       # ❌ Но mypy скажет "ошибка"

# ✅ Типы проверяются только статическими анализаторами

# ❌ `# type: ignore` везде (теряется весь смысл!)
result = some_function()  # type: ignore
value = another_func()    # type: ignore

# ✅ Используй редко, только где правда нужно

# ❌ Думать, что `# type: ignore` скрывает все ошибки
import requests
data = requests.get(url)  # type: ignore
print(data.json())        # ❌ mypy не предупредит, но IDE может!

# ❌ Не запускать type checker вообще
# Даже если код "работает"!

# ✅ Запускай в CI/CD
```

-----

## ✅ Главные правила

✅ **Используйте type checker перед production** — mypy или pyright  
✅ **Запускайте в CI/CD** — автоматизируйте проверку  
✅ **`# type: ignore` только в крайних случаях** — не халтурьте  
✅ **Проверяйте Optional перед использованием** — mypy это сразу найдёт  
✅ **Аннотируйте функции в первую очередь** — это даст максимум пользы  
✅ **Читайте сообщения об ошибках** — они очень информативны  
✅ **Настраивайте через конфиг** — не через флаги в каждой команде  
✅ **mypy более консервативен** — используйте его по умолчанию

-----

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[01 — 🔄 Union, Optional и типы из typing]]
- [[04.1 — 🔧 Конфиги mypy и best practices]]