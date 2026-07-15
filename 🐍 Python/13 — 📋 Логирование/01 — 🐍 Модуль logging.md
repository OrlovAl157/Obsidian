---
tags: [python, logging, модуль, логгер, basicConfig]
difficulty: beginner
---

# 🐍 Модуль logging в Python — памятка

> Полное руководство по модулю logging: логгеры (корневой и пользовательские), базовая конфигурация, уровни логирования, метод setLevel(), константа NOTSET, и как правильно настроить логирование в приложении.

## Содержание

- [[#Справка по логированию|Справка по логированию]]
- [[#🎯 Почему logging, а не print|Почему logging]]
- [[#📦 Импорт и логгеры|Логгеры]]
- [[#🔧 Корневой логгер|Корневой логгер]]
- [[#👤 Пользовательские логгеры|Пользовательские логгеры]]
- [[#⚙️ Базовая конфигурация|basicConfig()]]
- [[#📊 Уровни логирования|Уровни логирования]]
- [[#🔀 Метод setLevel()|setLevel()]]
- [[#0️⃣ Константа NOTSET|NOTSET]]
- [[#📝 Примечания|Примечания]]

---

## Справка по логированию

| Элемент | Назначение | Когда использовать |
|---------|-----------|-------------------|
| **Logger** | Инструмент для логирования | Создаёшь в каждом модуле (по `__name__`) |
| **basicConfig()** | Быстрая настройка | Маленькие проекты, скрипты |
| **setLevel()** | Установка уровня | Изменение уровня после basicConfig() |
| **DEBUG/INFO/WARNING/ERROR/CRITICAL** | Уровни логирования | Для методов логирования и настройки |

---

## 🎯 Почему logging, а не print

### Проблема с print()

```python
# ❌ Плохо
print("Ошибка!")  # где? когда? что произошло?
print("Успех")     # как это заканчивается?
```

### Решение — logging

```python
# ✅ Хорошо
import logging
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.error("Ошибка при обработке данных")
logger.info("Успешно загруженно 100 записей")
```

**Преимущества:**

✅ Форматирование (дата, время, уровень, имя модуля)  
✅ Разные уровни серьёзности  
✅ Легко включить/отключить по уровню  
✅ Запись в файл, консоль, email, и т.д.  
✅ Гибкая настройка  
✅ Профессиональный подход  

---

## 📦 Импорт и логгеры

### Самый простой способ

```python
import logging

# Способ 1: используем корневой логгер
logging.basicConfig(level=logging.INFO)
logging.info("Событие уровня INFO")
```

### Способ 2: через объект logger (правильный)

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

logger.info("Событие уровня INFO")
```

**Разница:** второй способ позволяет видеть в логах где произошло событие (какой модуль).

---

## 🔧 Корневой логгер

### Что это?

Корневой логгер (root logger) — это встроенный логгер, который уже существует в модуле logging. Ему не нужно ничего создавать.

### Как получить?

```python
import logging

root = logging.getLogger()  # без аргументов!

print(root)   # <RootLogger root (WARNING)>
print(type(root))  # <class 'logging.RootLogger'>
```

**По умолчанию:**
- Имя: `root`
- Уровень: `WARNING`

### Методы логирования

Логгер имеет методы для каждого уровня:

```python
import logging

root = logging.getLogger()

root.debug('Событие DEBUG')      # не выводится (по умолчанию WARNING)
root.info('Событие INFO')         # не выводится
root.warning('Событие WARNING')   # ✅ выводится
root.error('Событие ERROR')       # ✅ выводится
root.critical('Событие CRITICAL') # ✅ выводится
```

Вывод:
```
Событие уровня WARNING
Событие уровня ERROR
Событие уровня CRITICAL
```

### Почему только WARNING и выше?

Потому что это **уровень по умолчанию**. Нужно его изменить через `basicConfig()`.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
root = logging.getLogger()

root.debug('Событие DEBUG')       # ✅ теперь выводится!
root.info('Событие INFO')         # ✅ теперь выводится!
root.warning('Событие WARNING')   # ✅ выводится
```

Вывод:
```
DEBUG:root:Событие DEBUG
INFO:root:Событие INFO
WARNING:root:Событие WARNING
```

**Заметь:** формат изменился! Теперь видно уровень и имя логгера.

---

## 👤 Пользовательские логгеры

### Проблема с корневым логгером

```python
import logging

logging.basicConfig(level=logging.DEBUG)

logging.debug("Ошибка в файле 1")
logging.debug("Ошибка в файле 2")
logging.debug("Ошибка в файле 3")
```

Вывод:
```
DEBUG:root:Ошибка в файле 1
DEBUG:root:Ошибка в файле 2
DEBUG:root:Ошибка в файле 3
```

**Проблема:** все события имеют имя `root`, непонятно в каком файле ошибка!

### Решение — пользовательский логгер

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.debug("Ошибка в файле 1")
```

Вывод:
```
DEBUG:__main__:Ошибка в файле 1
```

Теперь видно что событие из `__main__` (главный модуль).

### Использование `__name__`

**В главном файле (main.py):**

```python
import logging

logger = logging.getLogger(__name__)
print(logger.name)  # __main__
```

**В импортируемом модуле (utils.py):**

```python
import logging

logger = logging.getLogger(__name__)
print(logger.name)  # utils
```

**В пакете (my_package/database.py):**

```python
import logging

logger = logging.getLogger(__name__)
print(logger.name)  # my_package.database
```

**Вывод:** `__name__` автоматически указывает полный путь к модулю!

### Правильная практика

```python
# ✅ В КАЖДОМ файле где используешь логирование:

import logging

logger = logging.getLogger(__name__)

logger.info("Это событие из [имя текущего модуля]")
```

---

## ⚙️ Базовая конфигурация

### Что это?

`basicConfig()` — это функция, которая быстро настраивает основные параметры логирования. Вызывается **один раз в начале программы**.

### Основные параметры

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,           # минимальный уровень
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    datefmt='%d.%m.%Y %H:%M:%S',  # формат даты
    filename='app.log'             # файл для логов (опционально)
)
```

### Пример 1: логирование в консоль

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

logger.debug('DEBUG')      # ❌ не выводится
logger.info('INFO')        # ✅ выводится
logger.warning('WARNING')  # ✅ выводится
```

### Пример 2: логирование в файл

```python
import logging

logging.basicConfig(
    filename='logs.log',
    level=logging.WARNING
)
logger = logging.getLogger(__name__)

logger.warning('Это сохранится в logs.log')
logger.error('И это тоже')
```

Файл `logs.log`:
```
WARNING:__main__:Это сохранится в logs.log
ERROR:__main__:И это тоже
```

### Пример 3: в консоль и файл

```python
import logging

# сначала консоль
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)
logger.debug('В консоль')

# потом в файл (нужно создавать обработчик вручную, об этом позже)
```

### Важное замечание

`basicConfig()` работает **ОДИН РАЗ**! Повторный вызов ничего не меняет:

```python
import logging

logging.basicConfig(level=logging.INFO)      # устанавливает INFO
logging.basicConfig(level=logging.DEBUG)     # ❌ НЕ меняет на DEBUG!
logger = logging.getLogger()

logger.debug('DEBUG')      # не выводится!
logger.info('INFO')        # выводится
```

**Решение:** использовать параметр `force=True`:

```python
import logging

logging.basicConfig(level=logging.INFO)
logging.basicConfig(level=logging.DEBUG, force=True)  # ✅ теперь меняет!
logger = logging.getLogger()

logger.debug('DEBUG')      # ✅ теперь выводится!
logger.info('INFO')        # выводится
```

---

## 📊 Уровни логирования

В Python уровни — это просто числа:

```python
import logging

print(logging.DEBUG)      # 10
print(logging.INFO)       # 20
print(logging.WARNING)    # 30
print(logging.ERROR)      # 40
print(logging.CRITICAL)   # 50
```

**Иерархия:**

```
DEBUG (10)
  ↓
INFO (20)
  ↓
WARNING (30)
  ↓
ERROR (40)
  ↓
CRITICAL (50)
```

### Используй константы, не числа

```python
# ❌ Плохо
logging.basicConfig(level=20)

# ✅ Хорошо
logging.basicConfig(level=logging.INFO)
```

Так понятнее что это INFO, а не какое-то случайное число.

---

## 🔀 Метод setLevel()

### Установка уровня логгеру

Если нужно установить уровень для конкретного логгера (не глобально):

```python
import logging

logging.basicConfig(level=logging.WARNING)
logger = logging.getLogger(__name__)

logger.setLevel(logging.DEBUG)  # переопределяем для этого логгера!

logger.debug('DEBUG')      # ✅ выводится (потому что setLevel)
logger.info('INFO')        # ✅ выводится
logger.warning('WARNING')  # ✅ выводится
```

### Важно: basicConfig() обязателен

Если НЕ вызвать `basicConfig()`, то `setLevel()` может не работать:

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

logger.debug('DEBUG')      # ❌ не выводится!
logger.info('INFO')        # ❌ не выводится!
logger.warning('WARNING')  # ✅ выводится
```

**Почему?** Потому что `basicConfig()` создаёт обработчик (handler), который реально выводит логи. Без него — нет вывода.

### Правильно: basicConfig() + setLevel()

```python
import logging

# 1. Сначала базовая конфигурация
logging.basicConfig(level=logging.WARNING)

# 2. Потом логгер и его уровень
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# 3. Теперь работает
logger.debug('DEBUG')      # ✅ выводится!
logger.warning('WARNING')  # ✅ выводится!
```

---

## 0️⃣ Константа NOTSET

### Что это?

`NOTSET` — это специальная константа, которая означает "уровень не установлен". Значение: **0**.

```python
import logging

print(logging.NOTSET)  # 0
```

### Когда встречается?

Пользовательские логгеры по умолчанию имеют уровень NOTSET:

```python
import logging

logging.basicConfig(level=logging.INFO)

root = logging.getLogger()        # корневой
logger = logging.getLogger(__name__)  # пользовательский

print(root.level)      # 20 (INFO)
print(logger.level)    # 0 (NOTSET)
```

### Что это значит?

Когда у логгера уровень NOTSET, он **ищет уровень у родителя**:

```
Пользовательский логгер (NOTSET)
        ↓
  Ищет уровень
        ↓
  Корневой логгер (INFO)
        ↓
  Использует INFO
```

```python
import logging

logging.basicConfig(level=logging.WARNING)

logger1 = logging.getLogger('module1')
logger2 = logging.getLogger('module2')

logger2.setLevel(logging.DEBUG)  # явно установили DEBUG

logger1.debug('DEBUG от module1')  # ❌ не выводится (использует WARNING от root)
logger2.debug('DEBUG от module2')  # ✅ выводится (явно установлен DEBUG)
```

### Как избежать путаницы?

**Всегда используй `basicConfig()` в начале:**

```python
import logging

logging.basicConfig(level=logging.DEBUG)  # установили для всех!
logger = logging.getLogger(__name__)

logger.debug('DEBUG')  # ✅ работает
```

---

## 📝 Примечания

### Примечание 1: метод log()

Есть универсальный метод `log()` для динамической записи:

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.log(logging.INFO, 'INFO сообщение')
logger.log(logging.ERROR, 'ERROR сообщение')
```

Полезен когда уровень динамический (из переменной).

### Примечание 2: методы warn() и fatal()

Альтернативные названия (deprecated):

```python
import logging

logging.basicConfig()
logger = logging.getLogger(__name__)

logger.warn('WARNING')    # старое название (работает, но warn() → warning())
logger.fatal('CRITICAL')  # старое название (работает, но fatal() → critical())
```

Лучше использовать `warning()` и `critical()`.

### Примечание 3: атрибут disabled

Можно временно отключить логирование:

```python
import logging

logging.basicConfig()
logger = logging.getLogger(__name__)

logger.warning('Это выводится')
logger.disabled = True
logger.warning('Это НЕ выводится')
logger.disabled = False
logger.warning('Это снова выводится')
```

### Примечание 4: логирование исключений

Метод `exception()` автоматически добавляет traceback:

```python
import logging

logging.basicConfig()
logger = logging.getLogger(__name__)

try:
    1 / 0
except ZeroDivisionError:
    logger.exception('Произошла ошибка!')  # автоматически добавит traceback
```

Вывод:
```
ERROR:__main__:Произошла ошибка!
Traceback (most recent call last):
  File "main.py", line 5, in <module>
    1 / 0
ZeroDivisionError: division by zero
```

---

## ✅ Главные правила

✅ Импортируй `import logging` в каждом файле  
✅ В каждом файле создавай `logger = logging.getLogger(__name__)`  
✅ В главном файле вызови `logging.basicConfig()` один раз  
✅ Используй константы уровней (`logging.INFO`, не `20`)  
✅ Всегда сначала `basicConfig()`, потом `setLevel()`  
✅ Корневой логгер — для быстрой настройки скриптов  
✅ Пользовательские логгеры — для настоящих проектов  
✅ `__name__` автоматически указывает имя модуля  
✅ NOTSET означает "наследовать от родителя"  
✅ Используй `logger.exception()` в обработчиках исключений  

---

## 🔗 Связанные темы

- [[00 — 📚 Введение в логирование]]
- [[02 — 📝 Форматирование событий]]
- [[03 — 🔧 Обработчики и форматеры]]
- [[04 — 🔍 Фильтрация событий]]
- [[Исключения]]
- [[Модули и пакеты]]

---

#python/logging #logger #basicConfig #levels