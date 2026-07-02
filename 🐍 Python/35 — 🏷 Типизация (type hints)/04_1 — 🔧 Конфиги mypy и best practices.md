-----

## tags: [python, типизация, mypy, конфиг, pyproject.toml]
difficulty: intermediate

# 🔧 Конфиги mypy и best practices

> Вместо передачи флагов в команду можно создать **конфиг-файл** (mypy.ini, pyproject.toml или setup.cfg) с настройками. Это удобнее, воспроизводимо и правильнее для команды. В этом файле указываются опции строгости, игнорируемые модули и многое другое. Правильная конфигурация делает type checking частью разработки, а не мучением.

## Содержание

- [[#Справка|Справка]]
- [[#📋 Структура конфига (mypy.ini)|Структура конфига (mypy.ini)]]
- [[#🎯 pyproject.toml (новый стандарт)|pyproject.toml (новый стандарт)]]
- [[#📈 Постепенное внедрение типов|Постепенное внедрение типов]]
- [[#🚫 Игнорирование ошибок в коде|Игнорирование ошибок в коде]]
- [[#🔌 Специальные плагины|Специальные плагины]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Файл конфига      |Поддержка     |Современность|Универсальность   |
|------------------|--------------|-------------|------------------|
|**mypy.ini**      |Только mypy   |Старая       |Только mypy       |
|**setup.cfg**     |Старый формат |Устарело     |Многие инструменты|
|**pyproject.toml**|Новый стандарт|Современная  |Все инструменты   |

|Опция                     |Значение|Эффект                           |
|--------------------------|--------|---------------------------------|
|`python_version`          |“3.10”  |Проверять для конкретной версии  |
|`warn_return_any`         |true    |Предупреждение на Any возврат    |
|`disallow_untyped_defs`   |true    |Обязательны типы на всех функциях|
|`disallow_incomplete_defs`|true    |Запретить неполные типы          |
|`no_implicit_optional`    |true    |Явный None в Optional            |
|`check_untyped_defs`      |true    |Проверять функции без типов      |
|`strict`                  |true    |Включить все строгие проверки    |

-----

## 📋 Структура конфига (mypy.ini)

```ini
[mypy]
# Версия Python для проверки
python_version = 3.10

# Строгость
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
disallow_incomplete_defs = True
disallow_untyped_calls = True
no_implicit_optional = True
check_untyped_defs = True

# Импорты
ignore_missing_imports = True

# Вывод
show_error_codes = True
show_error_context = True

# Модули для игнорирования
[mypy-old_module.*]
ignore_errors = True

[mypy-third_party_lib]
ignore_missing_imports = True
```

-----

## 🎯 pyproject.toml (новый стандарт)

```toml
[tool.mypy]
# Версия Python
python_version = "3.10"

# Основные строгие опции
strict = true

# Отключить strict для некоторых модулей
[[tool.mypy.overrides]]
module = "old_code.*"
ignore_errors = true

[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false

# Исключить папки
exclude = [
    "venv/",
    "build/",
    "dist/",
    ".git/"
]

# Импорты
ignore_missing_imports = true

# Вывод
show_error_codes = true
show_error_context = true
```

-----

## 📈 Постепенное внедрение типов

**Этап 1: Базовые настройки**

```ini
[mypy]
python_version = 3.10
ignore_missing_imports = True
```

Запускаем, смотрим сколько ошибок.

**Этап 2: Добавляем требования к новому коду**

```ini
[mypy]
python_version = 3.10
ignore_missing_imports = True
disallow_untyped_defs = True  # Новые функции ДО ЛЖНЫ быть типизированы

# Но старый код без типов — OK
[mypy-old_module.*]
disallow_untyped_defs = False
```

**Этап 3: Усиливаем для всех**

```ini
[mypy]
strict = True  # Включить все строгие опции
```

-----

## 🚫 Игнорирование ошибок в коде

Когда mypy ошибается или вы знаете, что делаете:

```python
# Отключить проверку для всей строки
value: int = some_function()  # type: ignore

# Отключить конкретную ошибку
result = risky_operation()  # type: ignore[assignment]

# Для файла (в начало)
# mypy: ignore-errors

def old_function():
    pass  # Весь файл не будет проверяться
```

**⚠️ Используй редко!**

-----

## 🔌 Специальные плагины

mypy поддерживает плагины для популярных библиотек:

```toml
[tool.mypy]
plugins = [
    "pydantic.mypy",
    "django.db.models.aggregates.Sum",
    "sqlalchemy.orm",
]

# Для pydantic
[tool.pydantic-mypy]
init_forbid_extra = true
init_typed = true
warn_required_dynamic_aliases = true
```

-----

## 💻 Примеры

### Пример 1: Полный pyproject.toml с комментариями

```toml
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "myapp"
version = "1.0.0"

[tool.mypy]
# ============ ОСНОВНОЕ ============
python_version = "3.10"
warn_return_any = true
warn_unused_configs = true

# ============ СТРОГОСТЬ ============
disallow_untyped_defs = true
disallow_incomplete_defs = true
disallow_untyped_calls = true
check_untyped_defs = true
no_implicit_optional = true

# ============ ИМПОРТЫ ============
ignore_missing_imports = true
exclude = [
    "venv/",
    ".venv/",
    "build/",
    "dist/",
    ".tox/",
]

# ============ ИСКЛЮЧЕНИЯ ============
# Старый код
[[tool.mypy.overrides]]
module = "legacy.*"
ignore_errors = true

# Тесты (менее строгие требования)
[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false
disallow_incomplete_defs = false

# ============ ВЫВОД ============
show_error_codes = true
show_error_context = true
pretty = true

[tool.pytest.ini_options]
testpaths = ["tests"]
```

### Пример 2: Интеграция в CI/CD (GitHub Actions)

```yaml
# .github/workflows/typing.yml
name: Type Checking

on: [push, pull_request]

jobs:
  mypy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: "3.10"
      
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install mypy
      
      - name: Run mypy
        run: mypy src/
```

### Пример 3: Pre-commit hook

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.5.0
    hooks:
      - id: mypy
        additional_dependencies:
          - types-all
          - pydantic
        args: [--config-file=pyproject.toml]
```

Установка:

```bash
pip install pre-commit
pre-commit install
```

Теперь mypy запускается автоматически перед commit’ом!

### Пример 4: Постепенное включение типов

```toml
# Этап 1: Базовая конфигурация
[tool.mypy]
python_version = "3.10"
ignore_missing_imports = true

# Запускаем: mypy .
# Видим: 500 ошибок 😅

# ============ ЭТАП 2 ============
# Включаем требования только для новых файлов

[tool.mypy]
python_version = "3.10"
ignore_missing_imports = true
warn_return_any = true

# Но старый код — OK
[[tool.mypy.overrides]]
module = "src.legacy.*"
ignore_errors = true

# Запускаем: mypy .
# Видим: 50 ошибок (только в new code)

# ============ ЭТАП 3 ============
# Постепенно усиливаем

[tool.mypy]
python_version = "3.10"
ignore_missing_imports = true
disallow_untyped_defs = true

[[tool.mypy.overrides]]
module = "src.legacy.*"
disallow_untyped_defs = false

# ============ ЭТАП 4 ============
# Со временем рефакторим старый код и убираем исключения
```

-----

## ⚠️ Частые ошибки

```toml
# ❌ Неправильный синтаксис TOML
[tool.mypy]
strict = true
disallow_untyped_defs = "true"  # Строка вместо булево!

# ✅ Правильно
[tool.mypy]
strict = true
disallow_untyped_defs = true

# ❌ `strict = true` + много исключений = нет смысла
[tool.mypy]
strict = true

[[tool.mypy.overrides]]
module = "src.*"
strict = false

# ✅ Лучше не включать strict, если нужны исключения
[tool.mypy]
disallow_untyped_defs = true
check_untyped_defs = true

# ❌ Исключить папку в ini
[mypy]
exclude = src/legacy/  # Error!

# ✅ Правильно (regex)
[mypy]
exclude = ^src/legacy/

# ❌ Забыли включить в pyproject.toml но запускаете с флагом
mypy --config-file=mypy.ini .

# ✅ Убедитесь, что конфиг используется
mypy .  # Автоматически найдёт pyproject.toml или mypy.ini
```

-----

## ✅ Главные правила

✅ **Используйте pyproject.toml** — это современный стандарт  
✅ **Начните с базовых настроек** — не `strict` сразу  
✅ **Постепенно усиливайте строгость** — по этапам  
✅ **Исключайте старый код** — через overrides  
✅ **Интегрируйте в CI/CD** — автоматизируйте  
✅ **Pre-commit hooks** — запуск перед commit’ом  
✅ **Документируйте конфиг** — в README  
✅ **Не используйте `strict` везде** — разумно выбирайте уровень

-----

## 🔗 Связанные темы

- [[04 — ✅ Type Checking с mypy и pyright]]
- [[00 — 📖 Основы типизации]]