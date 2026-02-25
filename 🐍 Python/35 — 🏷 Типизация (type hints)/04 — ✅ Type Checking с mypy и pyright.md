---
tags:
  - python
  - тема/типизация
  - статус/в-разработке
---

# ✅ Type Checking с mypy и pyright

## 📌 Коротко

> **Type checkers** — инструменты которые проверяют типы в коде без запуска. **mypy** — самый популярный, **pyright** — более быстрый от Microsoft.

---

## 💡 Полезные советы

- **Запускайте mypy перед commit** — отловит ошибки типов
- **Начните с `--ignore-missing-imports`** — если много старого кода
- **Используйте `# type: ignore`** — только для исключительных случаев
- **pyright быстрее mypy** — хороший выбор для больших проектов
- **IDE интеграция** — VSCode поддерживает оба из коробки

---

## 🚀 Быстрая справка

| Инструмент | Установка | Команда |
|---|---|---|
| **mypy** | `pip install mypy` | `mypy file.py` |
| **pyright** | `pip install pyright` | `pyright file.py` |
| **mypy все файлы** | - | `mypy .` |
| **Игнорировать ошибку** | - | `x = 5  # type: ignore` |

---

## 📖 Теория

### Установка и использование mypy

```bash
# Установка
pip install mypy

# Проверить один файл
mypy hello.py

# Проверить папку
mypy .

# С опциями
mypy --strict hello.py
mypy --ignore-missing-imports hello.py
```

### Пример с ошибками типов

```python
# hello.py
def greet(name: str) -> str:
    return f"Hello, {name}!"

# ✅ Правильное использование
msg = greet("Alice")

# ❌ Ошибка типа
msg = greet(42)  # mypy: error: Argument 1 to "greet" has incompatible type "int"; expected "str"
```

### Type: ignore комментарий

```python
# Когда mypy ошибается или мы знаем что делаем
def legacy_function():
    # Возвращает любой тип
    return some_weird_code()

value: int = legacy_function()  # type: ignore
```

### mypy конфигурация

```ini
# mypy.ini или setup.cfg
[mypy]
python_version = 3.9
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
disallow_untyped_calls = True
```

### Pyright

```bash
# Установка
pip install pyright

# Проверить файл
pyright hello.py

# Более строгая проверка
pyright --warnings hello.py
```

---

## 💻 Примеры

### Реальные ошибки которые поймёт mypy

```python
from typing import List, Dict, Optional

# ❌ Ошибка 1: неправильный тип аргумента
def process_numbers(nums: List[int]) -> int:
    return sum(nums)

result = process_numbers([1, 2, "3"])  # Error: "3" is str, not int

# ❌ Ошибка 2: None проблемы
def get_user(user_id: int) -> Optional[str]:
    if user_id == 1:
        return "Alice"
    return None

user = get_user(1)
print(user.upper())  # Error: "None" has no attribute "upper"

# ✅ Правильно
user = get_user(1)
if user is not None:
    print(user.upper())
```

### Type narrowing

```python
from typing import Union

def process(value: Union[int, str]) -> str:
    # Без сужения типа - ошибка
    # return value.upper()  # Error!
    
    # С сужением типа - OK
    if isinstance(value, str):
        return value.upper()
    else:
        return str(value)
```

### Использование в CI/CD

```bash
#!/bin/bash
# run_tests.sh

# Запустить type checker
mypy src/

if [ $? -ne 0 ]; then
    echo "Type checking failed!"
    exit 1
fi

# Запустить тесты
pytest tests/

# Запустить линтер
pylint src/
```

---

## ⚙️ Лучшие практики

### Постепенное внедрение

```python
# Этап 1: Аннотируйте сигнатуры функций
def add(x: int, y: int) -> int:
    return x + y

# Этап 2: Аннотируйте переменные
names: List[str] = ["Alice", "Bob"]
age: int = 25

# Этап 3: Используйте type checker
# mypy --ignore-missing-imports .

# Этап 4: Исправьте ошибки
# Этап 5: Включите более строгие опции
# mypy --strict .
```

### Pre-commit hook

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v0.991
    hooks:
      - id: mypy
        additional_dependencies: [types-all]
```

---

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[01 — 🔄 Union и Optional]]
- [[TypeVar и Custom types]]

## ❓ Вопросы / Непонятное

- ...
