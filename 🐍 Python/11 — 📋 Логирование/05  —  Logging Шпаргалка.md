# 📝 Python Logging Шпаргалка

#python #logging #шпаргалка

---

## 🚀 Быстрый старт

### Минимальный вариант
```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='[%(levelname)s] %(message)s'
)

logger = logging.getLogger(__name__)
logger.info('Привет!')
```

### Что это делает?
- Создаёт логгер
- Устанавливает уровень DEBUG (логирует ВСЁ)
- Выводит в консоль

---

## 📊 Уровни логирования

| Уровень | Значение | Когда использовать |
|---------|----------|-------------------|
| **DEBUG** | 10 | Отладка, значения переменных |
| **INFO** | 20 | Обычная информация, события |
| **WARNING** | 30 | Предупреждения (по умолчанию) |
| **ERROR** | 40 | Ошибки, но приложение работает |
| **CRITICAL** | 50 | Критичные сбои |

### Пример использования
```python
logger.debug('Отладочная информация')      # Уровень 10
logger.info('Информация')                  # Уровень 20
logger.warning('Предупреждение')           # Уровень 30
logger.error('Ошибка')                     # Уровень 40
logger.critical('Критическая ошибка')      # Уровень 50
```

---

## 🎨 Форматирование

### Основные переменные
```
%(levelname)s    → INFO, DEBUG, WARNING, ERROR, CRITICAL
%(message)s      → Сообщение логирования
%(asctime)s      → Время: 2024-01-15 14:30:45
%(name)s         → Имя логгера: module.submodule
%(filename)s     → Имя файла: main.py
%(lineno)d       → Номер строки: 42
%(funcName)s     → Имя функции: my_function
```

### Примеры форматов

#### Простой формат
```python
format='[%(levelname)s] %(message)s'
```
**Вывод:** `[INFO] Приложение запущено`

#### Полный формат
```python
format='[%(asctime)s] %(levelname)s | %(name)s:%(lineno)d | %(message)s'
```
**Вывод:** `[2024-01-15 14:30:45] INFO | main:42 | Приложение запущено`

#### Для файлов
```python
format='%(asctime)s | %(name)s | %(levelname)s | %(message)s'
```

---

## 🔌 Хендлеры (Handlers)

### FileHandler (Логирование в файл)
```python
from logging import FileHandler

handler = FileHandler(
    'logs.log',           # Имя файла
    mode='a',             # 'a' = добавлять, 'w' = перезаписывать
    encoding='UTF-8'      # Кодировка
)
```

> [!danger] ⚠️ Важно!
> Используй **'a'** (append) чтобы не потерять старые логи!

### StreamHandler (Логирование в консоль)
```python
from logging import StreamHandler
import sys

handler = StreamHandler(sys.stdout)  # Выводит в консоль
```

### RotatingFileHandler (Ротирующиеся файлы)
```python
from logging.handlers import RotatingFileHandler

handler = RotatingFileHandler(
    'logs.log',
    maxBytes=10*1024*1024,  # 10 МБ
    backupCount=5           # Хранить 5 файлов
)
# Когда файл превышает 10 МБ, создаётся новый
# logs.log.1, logs.log.2, ..., logs.log.5
```

---

## 🔗 Полная конфигурация (Рекомендуемый паттерн)

### config.py
```python
import logging
from logging import Logger, FileHandler, StreamHandler, Formatter
import sys

def setup_logger(name: str, log_file: str = 'logs.log') -> Logger:
    """
    Создаёт и возвращает настроенный логгер.
    
    Args:
        name: Имя логгера (обычно __name__)
        log_file: Путь к файлу логов
    
    Returns:
        Logger: Настроенный логгер
    """
    # 1️⃣ Создаём логгер
    logger: Logger = logging.getLogger(name)
    logger.setLevel(logging.DEBUG)
    
    # 2️⃣ Форматтер (один для всех)
    formatter: Formatter = Formatter(
        '[%(levelname)s] | %(asctime)s | %(name)s | %(message)s'
    )
    
    # 3️⃣ Файловый хендлер
    file_handler: FileHandler = FileHandler(log_file, 'a', encoding='UTF8')
    file_handler.setLevel(logging.INFO)      # Только INFO и выше
    file_handler.setFormatter(formatter)
    
    # 4️⃣ Консольный хендлер
    stream_handler: StreamHandler = StreamHandler(sys.stdout)
    stream_handler.setLevel(logging.WARNING) # Только WARNING и выше
    stream_handler.setFormatter(formatter)
    
    # 5️⃣ Подключаем хендлеры
    logger.addHandler(file_handler)
    logger.addHandler(stream_handler)
    
    return logger
```

### main.py
```python
from config import setup_logger

logger = setup_logger(__name__)

logger.debug('Отладка')           # Только в файл
logger.info('Информация')         # Только в файл
logger.warning('Предупреждение')  # В файл И консоль
logger.error('Ошибка')            # В файл И консоль
```

---

## 🎯 Методы логирования

### Основные методы
```python
logger.debug('Отладка')
logger.info('Информация')
logger.warning('Предупреждение')
logger.error('Ошибка')
logger.critical('Критическая ошибка')
```

### Логирование с исключением (traceback)

#### Способ 1: exception()
```python
try:
    1 / 0
except ZeroDivisionError:
    logger.exception('Произошла ошибка!')  # Автоматически добавляет traceback
```

#### Способ 2: error() с exc_info
```python
try:
    1 / 0
except ZeroDivisionError:
    logger.error('Произошла ошибка!', exc_info=True)
```

### Логирование с контекстом
```python
def parse_date(date_str: str) -> date:
    extra_data = {'date': date_str}
    try:
        result = datetime.strptime(date_str, '%d.%m.%Y').date()
        logger.info('Успешно', extra=extra_data)
        return result
    except ValueError:
        logger.error('Ошибка парсинга', extra=extra_data)
        raise InvalidDateError('Неверный формат')
```

---

## 🔒 Типизация Logging

```python
from logging import Logger, FileHandler, StreamHandler, Formatter

logger: Logger = logging.getLogger(__name__)
fh: FileHandler = FileHandler('logs.log')
sh: StreamHandler = StreamHandler()
fmt: Formatter = Formatter('[%(levelname)s] %(message)s')
```

> [!success] Бонус
> Проверка типов с mypy:
> ```bash
> pip install mypy
> mypy script.py
> ```

---

## ⚠️ Частые ошибки

### ❌ Ошибка 1: Неопределённая переменная
```python
logger = logging.getLogger(name)  # ❌ name не определена!
```

**✅ Исправление:**
```python
logger = logging.getLogger(__name__)  # ✅
```

---

### ❌ Ошибка 2: Режим 'w' перезаписывает логи
```python
FileHandler('logs.log', 'w')  # ❌ Удаляет старые логи!
```

**✅ Исправление:**
```python
FileHandler('logs.log', 'a')  # ✅ append mode
```

---

### ❌ Ошибка 3: Слишком широкий except
```python
try:
    result = datetime.strptime(date_str, '%d.%m.%Y')
except Exception:  # ❌ Ловит ВСЕ ошибки!
    logger.error('Ошибка парсинга')
    raise InvalidDateError('Неверный формат')
```

**✅ Исправление:**
```python
try:
    result = datetime.strptime(date_str, '%d.%m.%Y')
except ValueError:  # ✅ Конкретное исключение
    logger.exception('Ошибка парсинга')
    raise InvalidDateError('Неверный формат') from None
```

---

### ❌ Ошибка 4: Потеря traceback
```python
except ValueError:
    logger.error('Ошибка')  # ❌ Без деталей
```

**✅ Исправление:**
```python
except ValueError:
    logger.exception('Ошибка')  # ✅ Автоматически добавляет traceback
```

---

## 📚 Docstring для функций с логированием

```python
from datetime import datetime, date

class InvalidDateError(Exception):
    """Ошибка при парсинге даты"""
    pass

def parse_date(date_str: str) -> date:
    """
    Парсит строку даты в формате DD.MM.YYYY.
    
    Args:
        date_str: Строка с датой в формате 'DD.MM.YYYY'
    
    Returns:
        date: Объект datetime.date
    
    Raises:
        InvalidDateError: Если дата имеет некорректный формат
    
    Examples:
        >>> parse_date('25.12.2023')
        datetime.date(2023, 12, 25)
        
        >>> parse_date('invalid')
        InvalidDateError: Некорректная дата
    """
    try:
        result: date = datetime.strptime(date_str, '%d.%m.%Y').date()
        logger.info(f'Дата успешно преобразована: {date_str}')
        return result
    except ValueError:
        logger.exception(f'Ошибка парсинга даты: {date_str}')
        raise InvalidDateError('Некорректная дата') from None
```

---

## 🎓 Рекомендуемая структура проекта

```
my_project/
├── config.py          # Конфигурация логгера
├── main.py            # Главный файл
├── utils.py           # Утилиты
├── logs/              # Папка для логов
│   └── logs.log
└── README.md
```

### config.py
```python
import logging
from logging import Logger

def get_logger(name: str) -> Logger:
    """Возвращает настроенный логгер для модуля"""
    # ... полная конфигурация из примера выше ...
    return logger
```

### Использование в других модулях
```python
# main.py
from config import get_logger

logger = get_logger(__name__)

# utils.py
from config import get_logger

logger = get_logger(__name__)
```

---

## 🔖 Быстрая справка

| Что нужно | Код |
|-----------|-----|
| Логирование в файл + консоль | `setup_logger(__name__)` |
| Логирование с временем | `%(asctime)s` в формате |
| Логирование исключения | `logger.exception('Ошибка')` |
| Ротирующиеся файлы | `RotatingFileHandler(...)` |
| Разные уровни для разных хендлеров | `fh.setLevel()` и `sh.setLevel()` |
| Типизация логгера | `logger: Logger = ...` |
| Правильное имя логгера | `logging.getLogger(__name__)` |
| Режим добавления в файл | `FileHandler(..., 'a')` |

---

## 💡 Полезные трюки

### Форматирование с разными стилями
```python
# Style 1: % (старый стиль)
format='%(levelname)s - %(message)s'

# Style 2: {} (новый)
format='{levelname} - {message}'

# Style 3: $ (шаблоны)
format='${levelname} - ${message}'
```

### Отключение логирования библиотеки
```python
logging.getLogger('requests').setLevel(logging.WARNING)
logging.getLogger('urllib3').setLevel(logging.WARNING)
```

### Время в разных форматах
```python
# ISO format
datefmt='%Y-%m-%d %H:%M:%S'

# Другой формат
datefmt='%d.%m.%Y %H:%M'
```

---

## 📖 Больше информации

```bash
# Документация Python
python -m pydoc logging

# Официальная документация
# https://docs.python.org/3/library/logging.html
```

---

**Последнее обновление:** 2024  
**Версия Python:** 3.8+  
**Статус:** ✅ Готово к использованию
