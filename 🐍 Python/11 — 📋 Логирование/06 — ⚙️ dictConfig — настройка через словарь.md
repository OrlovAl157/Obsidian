---
tags: [python, logging, dictConfig, конфигурация]
difficulty: intermediate
---

# ⚙️ dictConfig — настройка логирования через словарь — памятка

> Способ настройки логирования через словарь конфигурации и функцию `dictConfig()`. Позволяет описать все компоненты (форматеры, фильтры, обработчики, логгеры) в одном месте и применить одним вызовом.

## Содержание

- [[#Справка|Справка]]
- [[#🎯 Что такое dictConfig и структура словаря|Что такое dictConfig и структура словаря]]
- [[#🎨 Форматеры|Форматеры]]
- [[#🔍 Фильтры|Фильтры]]
- [[#🔌 Обработчики|Обработчики]]
- [[#📋 Логгеры|Логгеры]]
- [[#🔧 Финальная сборка|Финальная сборка]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

---

## Справка

| Ключ словаря | Что описывает | Обязателен |
|---|---|---|
| `version` | Версия конфигурации | ✅ Да |
| `formatters` | Форматеры | ❌ Нет |
| `filters` | Фильтры | ❌ Нет |
| `handlers` | Обработчики | ❌ Нет |
| `loggers` | Логгеры | ❌ Нет |

| Спецсинтаксис | Где используется | Что означает |
|---|---|---|
| `'class'` | handlers | Встроенный класс (напр. `logging.StreamHandler`) |
| `'()'` | filters | Пользовательский класс |
| `'ext://...'` | везде | Реальный объект Python, не строка |

---

## 🎯 Что такое dictConfig и структура словаря

**Идея:** вместо того чтобы создавать логгеры, обработчики и форматеры по одному через код — описываешь всё в словаре и применяешь одним вызовом.

```python
import logging.config

logging.config.dictConfig(CONFIG)  # применяет всю конфигурацию сразу
```

**Минимальный словарь** — только обязательный ключ `version`:

```python
CONFIG = {'version': 1}
```

**Полная структура:**

```python
CONFIG = {
    'version': 1,          # обязательно!
    'formatters': { ... }, # описываем форматеры
    'filters':    { ... }, # описываем фильтры
    'handlers':   { ... }, # описываем обработчики
    'loggers':    { ... }, # описываем логгеры
}
```

**Зачем это лучше обычного кода:**
- Вся конфигурация в одном месте — легко читать и менять
- Можно хранить в отдельном файле и переиспользовать между модулями
- Не нужно помнить порядок вызовов `setFormatter()`, `addHandler()` и т.д.
- Особенно удобно в средних и больших проектах

---

## 🎨 Форматеры

**В обычном коде:**

```python
stream_formatter = logging.Formatter('{asctime}:{levelname}:{name}:{message}', style='{')
file_formatter   = logging.Formatter('{asctime}:{levelno}:{name}:{message}',
                                      style='{', datefmt='%d.%m.%Y %H:%M:%S')
```

**В словаре конфигурации:**

```python
'formatters': {
    'stream_formatter': {                          # имя форматера — любое
        'format': '{asctime}:{levelname}:{name}:{message}',
        'style': '{',
    },
    'file_formatter': {
        'format': '{asctime}:{levelno}:{name}:{message}',
        'style': '{',
        'datefmt': '%d.%m.%Y %H:%M:%S',
    },
},
```

**Важное:** ключи внутри словаря форматера совпадают с параметрами `logging.Formatter()`.

---

## 🔍 Фильтры

**В обычном коде:**

```python
class LevelFilter(logging.Filter):
    def __init__(self, level):
        super().__init__()
        self._level = level

    def filter(self, record):
        return record.levelno >= self._level

level_filter = LevelFilter(logging.INFO)
```

**В словаре конфигурации:**

```python
'filters': {
    'level_filter': {
        '()': '__main__.LevelFilter',  # путь к классу
        'level': 'ext://logging.INFO', # аргумент для конструктора
    },
},
```

**Ключ `()`** — используется для пользовательских классов (в отличие от `class` для встроенных).

**`ext://logging.INFO`** — префикс `ext://` говорит `dictConfig()`, что нужно передать реальный объект `logging.INFO` (число 20), а не строку `'logging.INFO'`.

```python
# Без ext:// — передаётся строка:
'level': 'logging.INFO'   # → LevelFilter получит строку 'logging.INFO' ❌

# С ext:// — передаётся объект:
'level': 'ext://logging.INFO'  # → LevelFilter получит число 20 ✅
```

---

## 🔌 Обработчики

**В обычном коде:**

```python
stream_handler = logging.StreamHandler(stream=sys.stdout)
file_handler   = logging.FileHandler('logs.log', encoding='utf-8')

file_handler.setLevel(logging.WARNING)
stream_handler.setFormatter(stream_formatter)
stream_handler.addFilter(level_filter)
file_handler.setFormatter(file_formatter)
```

**В словаре конфигурации:**

```python
'handlers': {
    'stream_handler': {
        'class': 'logging.StreamHandler',      # встроенный класс → ключ 'class'
        'formatter': 'stream_formatter',        # имя форматера из 'formatters'
        'stream': 'ext://sys.stdout',           # ext:// — реальный объект sys.stdout
        'filters': ['level_filter'],            # список имён фильтров
    },
    'file_handler': {
        'class': 'logging.FileHandler',
        'level': 'WARNING',                    # уровень без ext:// — встроенный класс сам разберёт
        'formatter': 'file_formatter',
        'filename': 'logs.log',
        'encoding': 'utf-8',
    },
},
```

**`class` vs `()`:**

| Ключ | Когда | Пример |
|---|---|---|
| `'class'` | Встроенный класс logging | `'logging.StreamHandler'` |
| `'()'` | Пользовательский класс | `'__main__.MyHandler'` |

**`ext://sys.stdout`** — нужен потому что `sys.stdout` это объект, а не строка. Без `ext://` передастся строка `'sys.stdout'` и возникнет ошибка.

**Уровень без `ext://`** — встроенные обработчики принимают уровень как строку `'WARNING'` и сами конвертируют. Пользовательские классы — нет, им нужен `ext://`.

---

## 📋 Логгеры

**В обычном коде:**

```python
module_logger  = logging.getLogger(__name__)
beegeek_logger = logging.getLogger('beegeek')

module_logger.setLevel(logging.DEBUG)
beegeek_logger.setLevel(logging.DEBUG)

module_logger.addHandler(stream_handler)
module_logger.addHandler(file_handler)
beegeek_logger.addHandler(stream_handler)
```

**В словаре конфигурации:**

```python
'loggers': {
    __name__: {                                    # имя логгера — не имя переменной!
        'level': 'DEBUG',
        'handlers': ['stream_handler', 'file_handler'],
    },
    'beegeek': {
        'level': 'DEBUG',
        'handlers': ['stream_handler'],
    },
},
```

**Важное:** в словаре указывается **имя логгера** (`__name__`, `'beegeek'`), а не имя переменной (`module_logger`, `beegeek_logger`). После `dictConfig()` логгеры получают через `getLogger()` как обычно — они уже будут настроены.

```python
logging.config.dictConfig(CONFIG)

module_logger  = logging.getLogger(__name__)   # уже настроен ✅
beegeek_logger = logging.getLogger('beegeek') # уже настроен ✅
```

---

## 🔧 Финальная сборка

```python
import logging
import logging.config


class LevelFilter:
    def __init__(self, level):
        self._level = level

    def filter(self, record):
        return record.levelno >= self._level


CONFIG = {
    'version': 1,

    'formatters': {
        'stream_formatter': {
            'format': '{asctime}:{levelname}:{name}:{message}',
            'style': '{',
        },
        'file_formatter': {
            'format': '{asctime}:{levelno}:{name}:{message}',
            'style': '{',
            'datefmt': '%d.%m.%Y %H:%M:%S',
        },
    },

    'filters': {
        'level_filter': {
            '()': '__main__.LevelFilter',
            'level': 'ext://logging.INFO',
        },
    },

    'handlers': {
        'stream_handler': {
            'class': 'logging.StreamHandler',
            'formatter': 'stream_formatter',
            'stream': 'ext://sys.stdout',
            'filters': ['level_filter'],
        },
        'file_handler': {
            'class': 'logging.FileHandler',
            'level': 'WARNING',
            'formatter': 'file_formatter',
            'filename': 'logs.log',
            'encoding': 'utf-8',
        },
    },

    'loggers': {
        __name__: {
            'level': 'DEBUG',
            'handlers': ['stream_handler', 'file_handler'],
        },
        'beegeek': {
            'level': 'DEBUG',
            'handlers': ['stream_handler'],
        },
    },
}

logging.config.dictConfig(CONFIG)

module_logger = logging.getLogger(__name__)

module_logger.info('Событие уровня INFO')
module_logger.warning('Событие уровня WARNING')
module_logger.error('Событие уровня ERROR')
```

Вывод в консоль:
```
2025-05-05 17:15:46,584:INFO:__main__:Событие уровня INFO
2025-05-05 17:15:46,584:WARNING:__main__:Событие уровня WARNING
2025-05-05 17:15:46,585:ERROR:__main__:Событие уровня ERROR
```

Файл `logs.log` (WARNING и выше, формат с датой):
```
05.05.2025 17:15:46:30:__main__:Событие уровня WARNING
05.05.2025 17:15:46:40:__main__:Событие уровня ERROR
```

---

## ⚠️ Частые ошибки

**❌ Забыл `ext://` для объекта Python:**
```python
'stream': 'sys.stdout'          # ❌ передаётся строка — ошибка!
'stream': 'ext://sys.stdout'    # ✅ передаётся объект sys.stdout
```

**❌ Использовал `class` вместо `()` для своего класса:**
```python
'class': '__main__.LevelFilter'  # ❌ для пользовательского класса
'()': '__main__.LevelFilter'     # ✅ правильно
```

**❌ Указал имя переменной вместо имени логгера:**
```python
'loggers': {
    'module_logger': { ... }  # ❌ это имя переменной, не логгера
    __name__: { ... }         # ✅ это имя логгера
}
```

**❌ Забыл `version` в словаре:**
```python
CONFIG = {'formatters': { ... }}           # ❌ KeyError: version
CONFIG = {'version': 1, 'formatters': { ... }}  # ✅
```

---

## ✅ Главные правила

✅ `version` — единственный обязательный ключ  
✅ `'class'` — для встроенных классов, `'()'` — для пользовательских  
✅ `ext://` — когда нужен реальный объект Python, а не строка  
✅ В `loggers` указывай имя логгера, а не имя переменной  
✅ После `dictConfig()` логгеры получаешь через `getLogger()` как обычно  
✅ Уровни для встроенных классов можно указывать строкой без `ext://`  
✅ `filters` в обработчике — список строк с именами фильтров  

---

## 🔗 Связанные темы

- [[03 — 🔧 Обработчики и форматеры]]
- [[04 — 🔍 Фильтрация событий]]
- [[03.1 — Устройство Обработчики и форматеры]]

---

#python/logging #dictConfig #конфигурация
