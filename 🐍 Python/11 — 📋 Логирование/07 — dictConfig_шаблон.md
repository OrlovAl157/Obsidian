# 📋 Полный шаблон dictConfig для logging

## **ВЕРХНИЙ УРОВЕНЬ — CONFIG**

```python
CONFIG = {
    'version': 1,                          # ✅ ОБЯЗАТЕЛЕН (всегда 1)
    'disable_existing_loggers': False,     # Опционально (обычно False)
    'incremental': False,                  # Опционально (обычно False)
    
    'formatters': {...},                   # Опционально (но обычно нужны)
    'filters': {...},                      # Опционально
    'handlers': {...},                     # ✅ ОБЯЗАТЕЛЕН (минимум один)
    'loggers': {...},                      # Опционально
    'root': {...},                         # Опционально
}
```

---

## **1️⃣ FORMATTERS — как форматировать сообщения**

### Общая структура:
```python
'formatters': {
    'имя_форматера': {
        'format': 'строка_формата',        # ✅ ОБЯЗАТЕЛЕН
        'style': '%' | '{' | '$',          # Опционально (по умолч. '%')
        'datefmt': 'формат_даты',         # Опционально
        'validate': True | False,          # Опционально (по умолч. True)
    },
}
```

### Доступные переменные при `style: '%'`:
```
%(asctime)s      — время логирования
%(levelname)s    — уровень (DEBUG, INFO, WARNING, ERROR, CRITICAL)
%(levelno)d      — номер уровня (10, 20, 30, 40, 50)
%(name)s         — имя логгера
%(message)s      — само сообщение
%(funcName)s     — имя функции
%(lineno)d       — номер строки кода
%(filename)s     — имя файла
%(pathname)s     — полный путь файла
%(module)s       — имя модуля
%(process)d      — ID процесса
%(thread)d       — ID потока
%(threadName)s   — имя потока
%(msecs)d        — миллисекунды
%(exc_info)s     — информация об исключении
```

### Примеры:
```python
'formatters': {
    'simple': {
        'format': '%(levelname)s - %(message)s',
        'style': '%',
    },
    'detailed': {
        'format': '{asctime} | {levelname} | {name} | {funcName}:{lineno} | {message}',
        'style': '{',
        'datefmt': '%d.%m.%Y %H:%M:%S',
    },
}
```

---

## **2️⃣ FILTERS — фильтрация логов**

### Общая структура:
```python
'filters': {
    'имя_фильтра': {
        '()': 'путь.до.Класса',            # ✅ ОБЯЗАТЕЛЕН
        'параметр1': значение,             # Опционально
    },
}
```

### Примеры:
```python
class LevelFilter:
    def __init__(self, level):
        self._level = level
    
    def filter(self, record):
        return record.levelno >= self._level

'filters': {
    'level_filter': {
        '()': '__main__.LevelFilter',
        'level': 'ext://logging.INFO',
    },
}
```

---

## **3️⃣ HANDLERS — куда писать логи**

### STREAMHANDLER (консоль)
```python
'stream_handler': {
    'class': 'logging.StreamHandler',
    'level': 'DEBUG',
    'formatter': 'simple',
    'stream': 'ext://sys.stdout',          # sys.stdout или sys.stderr
    'filters': ['level_filter'],
}
```

### FILEHANDLER (файл)
```python
'file_handler': {
    'class': 'logging.FileHandler',
    'level': 'WARNING',
    'formatter': 'detailed',
    'filename': 'app.log',                 # ✅ ОБЯЗАТЕЛЕН
    'mode': 'a',                           # 'a' = append, 'w' = write
    'encoding': 'utf-8',
}
```

### ROTATINGFILEHANDLER (ротация по размеру)
```python
'rotating_file_handler': {
    'class': 'logging.handlers.RotatingFileHandler',
    'level': 'INFO',
    'formatter': 'detailed',
    'filename': 'app.log',                 # ✅ ОБЯЗАТЕЛЕН
    'maxBytes': 1024 * 1024,               # 1 МБ
    'backupCount': 5,                      # Количество backup-файлов
}
```

### TIMEDROTATINGFILEHANDLER (ротация по времени)
```python
'timed_rotating_handler': {
    'class': 'logging.handlers.TimedRotatingFileHandler',
    'level': 'INFO',
    'formatter': 'detailed',
    'filename': 'app.log',                 # ✅ ОБЯЗАТЕЛЕН
    'when': 'midnight',                    # ✅ ОБЯЗАТЕЛЕН
    'interval': 1,
    'backupCount': 7,
    'utc': False,
}
```

---

## **4️⃣ LOGGERS — конфиг отдельных логгеров**

### Общая структура:
```python
'loggers': {
    'имя_логгера': {
        'level': 'DEBUG' | 'INFO' | ...,   # Опционально
        'handlers': ['handler1', 'handler2'],  # ✅ ОБЯЗАТЕЛЕН
        'propagate': True | False,         # Опционально
        'filters': ['filter1'],             # Опционально
        'disabled': False,                  # Опционально
    },
}
```

### Примеры:
```python
'loggers': {
    __name__: {
        'level': 'DEBUG',
        'handlers': ['stream_handler', 'file_handler'],
        'propagate': False,
    },
    'myapp.database': {
        'level': 'INFO',
        'handlers': ['file_handler'],
        'propagate': True,
    },
}
```

---

## **5️⃣ ROOT — корневой логгер**

### Структура:
```python
'root': {
    'level': 'WARNING' | 'INFO' | ...,    # ✅ ОБЯЗАТЕЛЕН
    'handlers': ['handler1', 'handler2'], # ✅ ОБЯЗАТЕЛЕН
    'filters': ['filter1'],                # Опционально
    'disabled': False,                     # Опционально
}
```

### Пример:
```python
'root': {
    'level': 'INFO',
    'handlers': ['console', 'file'],
}
```

---

## **📊 ТАБЛИЦА УРОВНЕЙ**

| Уровень  | Значение | Когда использовать |
|----------|----------|-------------------|
| DEBUG    | 10       | Подробная диагностика |
| INFO     | 20       | Нормальный ход работы |
| WARNING  | 30       | Что-то неожиданное |
| ERROR    | 40       | Серьёзная проблема |
| CRITICAL | 50       | Критическая ошибка |

---

## **⚡ ОБЯЗАТЕЛЬНЫЕ И ОПЦИОНАЛЬНЫЕ**

### CONFIG:
- ✅ `version` — ОБЯЗАТЕЛЕН
- ✅ `handlers` — ОБЯЗАТЕЛЕН (минимум один)
- ✅ `loggers` ИЛИ `root` — хотя бы один ОБЯЗАТЕЛЕН
- ❌ остальное — опционально

### Formatter:
- ✅ `format` — ОБЯЗАТЕЛЕН
- ❌ `style`, `datefmt` — опционально

### Handler:
- ✅ `class` — ОБЯЗАТЕЛЕН
- ✅ `filename` — ОБЯЗАТЕЛЕН для File/Rotating/TimedRotating
- ✅ `when` — ОБЯЗАТЕЛЕН для TimedRotating
- ❌ остальное — опционально

### Logger:
- ✅ `handlers` — ОБЯЗАТЕЛЕН (минимум один)
- ❌ остальное — опционально

### Root:
- ✅ `level` — ОБЯЗАТЕЛЕН
- ✅ `handlers` — ОБЯЗАТЕЛЕН (минимум один)
- ❌ остальное — опционально

---

## **💾 ПОЛНЫЙ РАБОЧИЙ ПРИМЕР**

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
    'disable_existing_loggers': False,
    
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

---

## **🎯 КАК ИСПОЛЬЗОВАТЬ ШАБЛОН**

1. Скопируй нужную структуру (например, STREAMHANDLER)
2. Замени `'имя_...'` на реальные имена
3. Заполни обязательные ключи (✅)
4. Добавь опциональные (❌) если нужно
5. Используй `logging.config.dictConfig(CONFIG)`