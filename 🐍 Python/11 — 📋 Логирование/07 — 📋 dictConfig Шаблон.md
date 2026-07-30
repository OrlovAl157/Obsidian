---
tags: [python, logging, dictConfig, справочник, шаблон]
difficulty: reference
---

# 📋 dictConfig — полный справочник всех ключей — памятка

> Полный шаблон и справочник всех ключей словаря конфигурации логирования. Какие ключи обязательны, какие опциональны, что может быть в каждом разделе, примеры для каждого типа обработчика.

## Содержание

- [[#Справка|Справка]]
- [[#🎯 Структура CONFIG|Структура CONFIG]]
- [[#🎨 Formatters — форматеры|Formatters — форматеры]]
- [[#🔍 Filters — фильтры|Filters — фильтры]]
- [[#🔌 Handlers — обработчики|Handlers — обработчики]]
- [[#📋 Loggers — логгеры|Loggers — логгеры]]
- [[#🔧 Root — корневой логгер|Root — корневой логгер]]
- [[#📊 Таблица уровней|Таблица уровней]]
- [[#⚡ Обязательные ключи (сводка)|Обязательные ключи (сводка)]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

### Топ-уровневые ключи CONFIG

| Ключ | Что описывает | Обязателен | Примечание |
|---|---|---|---|
| `version` | Версия конфигурации | ✅ Да | Всегда `1` |
| `disable_existing_loggers` | Отключать ли существующие логгеры | ❌ Нет | По умолч. `False` |
| `incremental` | Дополнять ли, или перезаписать | ❌ Нет | По умолч. `False` |
| `formatters` | Форматеры | ❌ Нет | Словарь |
| `filters` | Фильтры | ❌ Нет | Словарь |
| `handlers` | Обработчики | ✅ Да* | *Если нет root, обязателен handlers |
| `loggers` | Логгеры | ✅ Да* | *Хотя бы один: loggers ИЛИ root |
| `root` | Корневой логгер | ✅ Да* | *Хотя бы один: root ИЛИ loggers |

### Спецсинтаксис

| Синтаксис | Где | Для чего |
|---|---|---|
| `'class'` | handlers | Встроенный класс (`logging.StreamHandler`) |
| `'()'` | filters, handlers | Пользовательский класс (`__main__.MyFilter`) |
| `'ext://...'` | везде | Реальный объект Python, не строка (`ext://sys.stdout`) |

---

## 🎯 Структура CONFIG

**Минимальный (только версия):**
```python
CONFIG = {
    'version': 1,
}
```

**Реалистичный минимум:**
```python
CONFIG = {
    'version': 1,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
        }
    },
    'root': {
        'level': 'INFO',
        'handlers': ['console'],
    }
}
```

**Полная структура:**
```python
CONFIG = {
    'version': 1,                          # ✅ ОБЯЗАТЕЛЕН
    'disable_existing_loggers': False,     # Опционально
    'incremental': False,                  # Опционально
    
    'formatters': {...},                   # Опционально
    'filters': {...},                      # Опционально
    'handlers': {...},                     # ✅ Обычно нужен
    'loggers': {...},                      # Опционально
    'root': {...},                         # Опционально
}
```

---

## 🎨 Formatters — форматеры

### Структура одного форматера:
```python
'formatters': {
    'имя_форматера': {
        'format': 'строка_формата',        # ✅ ОБЯЗАТЕЛЕН
        'style': '%' | '{' | '$',          # Опционально (по умолч. '%')
        'datefmt': 'строка_формата',      # Опционально
        'validate': True | False,          # Опционально (по умолч. True)
    },
}
```

### Переменные форматирования

**При `style: '%'`:**
```
%(asctime)s      — время логирования
%(levelname)s    — уровень (DEBUG, INFO, WARNING, ERROR, CRITICAL)
%(levelno)d      — номер уровня (10, 20, 30, 40, 50)
%(name)s         — имя логгера
%(message)s      — само сообщение
%(funcName)s     — имя функции
%(lineno)d       — номер строки
%(filename)s     — имя файла
%(pathname)s     — полный путь
%(module)s       — имя модуля
%(process)d      — ID процесса
%(thread)d       — ID потока
```

**При `style: '{'`:**
```
{asctime}  {levelname}  {name}  {message}  {funcName}  {lineno}  {filename}  и т.д.
```

### Примеры

**Простой формат:**
```python
'formatters': {
    'simple': {
        'format': '%(levelname)s - %(message)s',
        'style': '%',
    },
}
```

**Подробный формат:**
```python
'formatters': {
    'detailed': {
        'format': '{asctime} | {levelname} | {name} | {funcName}:{lineno} | {message}',
        'style': '{',
        'datefmt': '%d.%m.%Y %H:%M:%S',
    },
}
```

---

## 🔍 Filters — фильтры

### Структура:
```python
'filters': {
    'имя_фильтра': {
        '()': 'путь.до.Класса',            # ✅ ОБЯЗАТЕЛЕН (спецключ для класса)
        'параметр1': значение,             # Опционально
        'параметр2': значение,             # Зависит от класса
    },
}
```

### Встроенный фильтр по имени:
```python
'filters': {
    'only_myapp': {
        '()': 'logging.Filter',
        'name': 'myapp',  # пропускает логи с этим именем и подимен
    },
}
```

### Кастомный класс фильтра:
```python
class LevelFilter:
    def __init__(self, level):
        self._level = level
    
    def filter(self, record):
        return record.levelno >= self._level  # True = пропустить

'filters': {
    'level_filter': {
        '()': '__main__.LevelFilter',         # путь к классу
        'level': 'ext://logging.INFO',        # параметр конструктора
    },
}
```

**`'()'` — спецключ** для вызова конструктора пользовательского класса  
**`'ext://'` — префикс** для передачи реального объекта, а не строки

---

## 🔌 Handlers — обработчики

### Общая структура:
```python
'handlers': {
    'имя_обработчика': {
        'class': 'logging.ИмяHandler',     # ✅ ОБЯЗАТЕЛЕН
        'level': 'DEBUG' | 'INFO' | ...,   # Опционально
        'formatter': 'имя_форматера',      # Опционально
        'filters': ['имя_фильтра1', ...],  # Опционально (список)
        # специфические ключи для конкретного типа
    },
}
```

### StreamHandler (консоль)
```python
'stream_handler': {
    'class': 'logging.StreamHandler',
    'level': 'DEBUG',                     # Уровень обработчика
    'formatter': 'simple',                # Какой форматер использовать
    'stream': 'ext://sys.stdout',         # sys.stdout или sys.stderr
    'filters': ['level_filter'],          # Список фильтров
}
```

**Специфические ключи:**
- `stream`: `'ext://sys.stdout'` или `'ext://sys.stderr'`

---

### FileHandler (файл)
```python
'file_handler': {
    'class': 'logging.FileHandler',
    'level': 'WARNING',
    'formatter': 'detailed',
    'filename': 'app.log',                # ✅ ОБЯЗАТЕЛЕН
    'mode': 'a',                          # 'a' = append, 'w' = write
    'encoding': 'utf-8',                  # Кодировка файла
    'delay': False,                       # Открывать файл сразу
}
```

**Специфические ключи:**
- `filename`: путь к файлу ✅
- `mode`: `'a'` (добавлять) или `'w'` (перезаписывать)
- `encoding`: кодировка (`'utf-8'`, `'cp1251'` и т.д.)
- `delay`: если `True` — файл откроется при первом логе

---

### RotatingFileHandler (ротация по размеру)
```python
'rotating_file_handler': {
    'class': 'logging.handlers.RotatingFileHandler',
    'level': 'INFO',
    'formatter': 'detailed',
    'filename': 'app.log',                # ✅ ОБЯЗАТЕЛЕН
    'maxBytes': 1024 * 1024,              # 1 МБ — при достижении создаётся backup
    'backupCount': 5,                     # Сколько старых файлов хранить
    'mode': 'a',
    'encoding': 'utf-8',
}
```

**Результат:** `app.log`, `app.log.1`, `app.log.2`, ..., `app.log.5`

---

### TimedRotatingFileHandler (ротация по времени)
```python
'timed_rotating_handler': {
    'class': 'logging.handlers.TimedRotatingFileHandler',
    'level': 'INFO',
    'formatter': 'detailed',
    'filename': 'app.log',                # ✅ ОБЯЗАТЕЛЕН
    'when': 'midnight',                   # ✅ ОБЯЗАТЕЛЕН
    'interval': 1,                        # Ротировать каждый N-й период
    'backupCount': 7,                     # Хранить 7 старых файлов
    'encoding': 'utf-8',
    'utc': False,                         # Локальное время или UTC
}
```

**Значения `when`:**
- `'S'` — каждую секунду
- `'M'` — каждую минуту
- `'H'` — каждый час
- `'D'` — каждый день (полночь)
- `'midnight'` — тоже полночь
- `'W0'`...`'W6'` — понедельник...воскресенье

---

### SysLogHandler (системный журнал)
```python
'syslog_handler': {
    'class': 'logging.handlers.SysLogHandler',
    'level': 'INFO',
    'formatter': 'simple',
    'address': '/dev/log',                # ✅ ОБЯЗАТЕЛЕН (Linux)
    # или на других ОС: 'address': ('localhost', 514),
    'facility': 'LOG_USER',               # Опционально
}
```

---

### NTEventLogHandler (Windows Event Log)
```python
'eventlog_handler': {
    'class': 'logging.handlers.NTEventLogHandler',
    'level': 'WARNING',
    'formatter': 'simple',
    'appname': 'MyApp',                   # ✅ ОБЯЗАТЕЛЕН
}
```

---

### HTTPHandler (отправка по HTTP)
```python
'http_handler': {
    'class': 'logging.handlers.HTTPHandler',
    'level': 'ERROR',
    'formatter': 'simple',
    'host': 'localhost:8000',             # ✅ ОБЯЗАТЕЛЕН
    'url': '/logging',                    # ✅ ОБЯЗАТЕЛЕН
    'method': 'GET' | 'POST',             # Опционально
}
```

---

## 📋 Loggers — логгеры

### Структура:
```python
'loggers': {
    'имя_логгера': {
        'level': 'DEBUG' | 'INFO' | ...,  # Опционально
        'handlers': ['handler1', ...],    # ✅ ОБЯЗАТЕЛЕН (минимум один)
        'propagate': True | False,        # Опционально
        'filters': ['filter1', ...],      # Опционально
        'disabled': False,                # Опционально
    },
}
```

### Примеры конфигурации

**Логгер текущего модуля:**
```python
'loggers': {
    __name__: {
        'level': 'DEBUG',
        'handlers': ['stream_handler', 'file_handler'],
        'propagate': False,  # Не пробрасывать родителю
    },
}
```

**Логгер подмодуля (иерархия):**
```python
'loggers': {
    'myapp.database': {
        'level': 'INFO',
        'handlers': ['file_handler'],
        'propagate': True,  # Пробросить в родителя (в root)
    },
    'myapp.api': {
        'level': 'WARNING',
        'handlers': ['console'],
        'propagate': False,
    },
}
```

### Параметры

| Параметр | Что означает |
|---|---|
| `level` | Минимальный уровень для этого логгера (DEBUG=10, INFO=20, WARNING=30, ERROR=40, CRITICAL=50) |
| `handlers` | Список обработчиков, которые будут обрабатывать логи этого логгера |
| `propagate` | `True` = пробросить родительскому логгеру, `False` = использовать только указанные handlers |
| `filters` | Список фильтров, которые применятся к этому логгеру |
| `disabled` | `True` = отключить этот логгер (не будет логировать ничего) |

---

## 🔧 Root — корневой логгер

### Структура:
```python
'root': {
    'level': 'WARNING' | 'INFO' | ...,    # ✅ Обычно нужен
    'handlers': ['handler1', 'handler2'], # ✅ ОБЯЗАТЕЛЕН
    'filters': ['filter1', ...],          # Опционально
    'disabled': False,                    # Опционально
}
```

### Пример:
```python
'root': {
    'level': 'INFO',
    'handlers': ['console', 'file'],
}
```

### Назначение

**Root — это fallback для всех логгеров:**
- Если логгер НЕ найден в секции `loggers` → используется `root`
- Если `propagate=True` в логгере → логи идут в `root` после обработки логгером
- `root` — это родитель всей иерархии логгеров

---

## 📊 Таблица уровней

| Уровень | Значение | Когда использовать |
|---|---|---|
| DEBUG | 10 | Подробная информация для диагностики разработчиком |
| INFO | 20 | Подтверждение правильного хода программы |
| WARNING | 30 | Что-то неожиданное произошло, но программа работает |
| ERROR | 40 | Серьёзная проблема, часть функции не работает |
| CRITICAL | 50 | Очень серьёзная проблема, программа может упасть |

---

## ⚡ Обязательные ключи (сводка)

### На уровне CONFIG:
- ✅ `version` — ОБЯЗАТЕЛЕН (всегда `1`)
- ✅ `handlers` — ОБЯЗАТЕЛЕН (минимум один)
- ✅ `loggers` ИЛИ `root` — хотя бы один ОБЯЗАТЕЛЕН
- ❌ остальное — опционально

### На уровне formatter:
- ✅ `format` — ОБЯЗАТЕЛЕН
- ❌ `style`, `datefmt`, `validate` — опционально

### На уровне handler:
- ✅ `class` — ОБЯЗАТЕЛЕН
- ✅ `filename` — ОБЯЗАТЕЛЕН ТОЛЬКО для FileHandler, RotatingFileHandler, TimedRotatingFileHandler
- ✅ `when` — ОБЯЗАТЕЛЕН ТОЛЬКО для TimedRotatingFileHandler
- ✅ `appname` — ОБЯЗАТЕЛЕН ТОЛЬКО для NTEventLogHandler
- ✅ `host`, `url` — ОБЯЗАТЕЛЕНЫ ТОЛЬКО для HTTPHandler
- ❌ `level`, `formatter`, `filters`, `stream`, `mode`, `encoding` — опционально

### На уровне logger:
- ✅ `handlers` — ОБЯЗАТЕЛЕН (минимум один)
- ❌ `level`, `propagate`, `filters`, `disabled` — опционально

### На уровне root:
- ✅ `level` — ОБЯЗАТЕЛЕН
- ✅ `handlers` — ОБЯЗАТЕЛЕН (минимум один)
- ❌ `filters`, `disabled` — опционально

---

## ✅ Главные правила

✅ **`version: 1`** — единственный обязательный ключ в CONFIG  
✅ **`'class'`** — для встроенных классов (`logging.StreamHandler`)  
✅ **`'()'`** — для пользовательских классов (`__main__.MyFilter`)  
✅ **`'ext://'`** — когда нужен реальный объект Python (`ext://sys.stdout`)  
✅ **`handlers` минимум один** — иначе логи куда-то писать нечему  
✅ **`loggers` ИЛИ `root`** — хотя бы один из них ОБЯЗАТЕЛЕН  
✅ **`propagate=True`** — логи идут в родителя (обычно в root)  
✅ **`propagate=False`** — логи идут ТОЛЬКО в указанные handlers этого логгера  
✅ **`handlers` в logger** — это СПИСОК имён обработчиков  
✅ **Уровни для встроенных классов** можно указывать строкой (`'WARNING'`)  
✅ **После `dictConfig()`** логгеры получаешь через `getLogger()` как обычно  

---

## 🔗 Связанные темы

- [[06 — ⚙️ dictConfig — настройка через словарь]]
- [[03 — 🔧 Обработчики и форматеры]]
- [[04 — 🔍 Фильтрация событий]]
- [[01 — 🐍 Модуль logging]]

---

#python/logging #dictConfig #справочник #шаблон