---
type: memory
tags: [logging, python, handler, formatter, logger]
created: 2026-07-21
updated: 2026-07-21
---

# 🔧 Logger, Handler и Formatter

## Справка: основные объекты

| Объект | Что делает | Где находится | На что влияет |
|--------|-----------|---------------|---------------|
| **Logger** | Принимает сообщения (info, error, debug...) | `logging.getLogger(__name__)` | Отправляет LogRecord в Handler |
| **LogRecord** | Контейнер всех данных о сообщении | Создаётся внутри Logger | Передаётся Handler и Formatter |
| **Handler** | Решает, куда отправить сообщение | Присоединяется к Logger | Выбирает, какой Formatter использовать |
| **Formatter** | Решает, как выглядит сообщение | Присоединяется к Handler | Форматирует строку, которая попадает в файл/консоль |
| **Filter** | Решает, пропустить или отбросить LogRecord | Присоединяется к Logger или Handler | Влияет на то, обработается ли сообщение вообще |

---

## Архитектура: основная схема

```
logger.info("текст")
      │
      ▼
  LogRecord создаётся
  ├── name = __name__
  ├── levelname = "INFO"
  ├── message = "текст"
  ├── filename, lineno, funcName...
  └── extra = {...}
      │
      ▼
    Logger
      │
      ├──────────────────────────┐
      │                          │
      ▼                          ▼
   Handler 1               Handler 2
   (FileHandler)           (StreamHandler)
      │                          │
      ▼                          ▼
 Formatter 1              Formatter 2
      │                          │
      ▼                          ▼
  logs.log                    stdout
```

**Ключевой момент:** один Logger может иметь несколько Handler, каждый может иметь свой Formatter.

---

## Жизненный цикл `logger.info()`

Пошагово, что происходит:

```
1. Вызов logger.info("сообщение")
                 │
                 ▼
2. Logger создаёт LogRecord
   - берёт текст "сообщение"
   - добавляет метаданные (имя файла, строку, имя функции)
   - добавляет extra (если был передан)
                 │
                 ▼
3. Logger проверяет свой уровень (logger.level)
   - если message меньше уровня → выход, сообщение не пойдёт дальше
   - если больше → продолжить
                 │
                 ▼
4. Logger проходит по своим Handler
   (цикл: для каждого Handler)
                 │
                 ▼
5. Handler проверяет свой уровень (handler.level)
   - если LogRecord не подходит → перейти к следующему Handler
   - если подходит → продолжить
                 │
                 ▼
6. Handler пропускает через свои Filter
   - если Filter вернул False → перейти к следующему Handler
   - если True → продолжить
                 │
                 ▼
7. Handler вызывает Formatter.format(LogRecord)
   - Formatter преобразует LogRecord в строку
   - использует шаблон: "[%(levelname)s] %(message)s"
                 │
                 ▼
8. Handler отправляет строку в целевой объект
   - FileHandler → пишет в файл
   - StreamHandler → пишет в консоль
   - SMTPHandler → отправляет по почте
                 │
                 ▼
9. Logger проверяет propagate
   - если propagate=False → конец
   - если propagate=True → поднять сообщение родителю (следующая схема)
```

---

## Поиск обработчиков: механизм propagate

Когда Logger не находит обработчик самостоятельно:

```
logger = logging.getLogger("app.db")
logger.info("ошибка в БД")
          │
          ▼
    Logger "app.db"
          │
    Есть свои Handler?
          │
      ┌───┴───┐
      │       │
     Да      Нет
      │       │
      ▼       ▼
  Обработка  propagate?
                │
            ┌───┴───┐
            │       │
          Нет      Да
            │       │
            ▼       ▼
          Конец  Logger "app"
                     │
                Есть свои Handler?
                     │
                 ┌───┴───┐
                 │       │
                Да      Нет
                 │       │
                 ▼       ▼
            Обработка  propagate?
                           │
                       ┌───┴───┐
                       │       │
                     Нет      Да
                       │       │
                       ▼       ▼
                     Конец  Root Logger
                              │
                         Есть Handler?
                              │
                          ┌───┴───┐
                          │       │
                         Да      Нет
                          │       │
                          ▼       ▼
                      Обработка  Потеря
```

**Суть:** сообщение "всплывает" вверх по цепочке родительских логгеров, пока не найдёт обработчик.

---

## Структура LogRecord

Когда создаётся LogRecord, он содержит:

```
LogRecord
├── name          # "app.db" или "__main__"
├── levelname     # "INFO", "ERROR", "DEBUG"
├── levelno       # 20, 40, 10 (числовое значение уровня)
├── message       # "сообщение"
├── msg           # исходная строка до форматирования
├── args          # аргументы для форматирования (если были)
│
├── filename      # "main.py"
├── module        # "main"
├── funcName      # "process_data"
├── lineno        # 42
│
├── created       # временная метка создания
├── msecs         # миллисекунды
│
└── extra         # {"user_id": 123, "request_id": "abc"}
                  # поля, которые вы передали через extra={}
```

Все эти поля доступны в Formatter через `%(name)s`, `%(levelname)s` и т.д.

---

## Как Formatter использует LogRecord

```
LogRecord ──► Formatter ──► строка
                  │
                  ▼
         Шаблон: "[%(levelname)s] %(message)s"
                  │
         Подставляет:
         - %(levelname)s → INFO
         - %(message)s → "сообщение"
         - %(created)s → 1721567890.123
         - любое поле из extra
                  │
                  ▼
         Итоговая строка:
         "[INFO] сообщение"
```

**Важно:** Formatter только преобразует LogRecord в строку. Он **не решает**, куда отправить сообщение — это делает Handler.

---

## Несколько Handler на одном Logger

```
logger = logging.getLogger(__name__)

file_handler = logging.FileHandler("app.log")
console_handler = logging.StreamHandler(sys.stdout)

logger.addHandler(file_handler)
logger.addHandler(console_handler)

logger.info("привет")
    │
    ├──────► file_handler
    │            │
    │            ▼
    │      Formatter для файла
    │            │
    │            ▼
    │      "[INFO] привет" → app.log
    │
    └──────► console_handler
                 │
                 ▼
           Formatter для консоли
                 │
                 ▼
           "[INFO] привет" → stdout
```

**Результат:** одно сообщение попадает сразу в оба места, но каждый может иметь свой формат.

---

## Поле extra: добавление пользовательских данных

```
from datetime import datetime

date_str = datetime.now().strftime("%d.%m.%Y")

logger.info(
    "действие завершено",
    extra={"date_str": date_str}
)
      │
      ▼
  LogRecord создаётся
      │
      ├── message = "действие завершено"
      └── date_str = "21.07.2026"  ← добавлено из extra
      │
      ▼
  Formatter
      │
      └── "[%(levelname)s] %(date_str)s | %(message)s"
      │
      ▼
  "[INFO] 21.07.2026 | действие завершено"
```

**Правило:** любой ключ из `extra={}` становится атрибутом LogRecord и доступен в Formatter.

---

## Уровни логирования и фильтрация

```
logger.setLevel(logging.INFO)    # ← на Logger
handler.setLevel(logging.ERROR)  # ← на Handler

logger.debug("отладка")     → Logger проверяет: DEBUG < INFO → ❌ не пройдёт
logger.info("информация")   → Logger проверяет: INFO = INFO → ✓ пройдёт
logger.error("ошибка")      → Logger проверяет: ERROR > INFO → ✓ пройдёт
                                  ↓
                              Handler проверяет: ERROR = ERROR → ✓ пройдёт
                                  ↓
                              Запись в файл

logger.warning("предупреждение")  → Logger: WARNING > INFO → ✓
                                      Handler: WARNING > ERROR → ❌ не пойдёт в Handler
```

**Правило:** сообщение должно пройти проверку и Logger, и Handler, иначе оно не обработается.

---

## Когда Handler найти не удалось

```
logger = logging.getLogger("app.db")
logger.info("сообщение")
      │
      ▼
"app.db" Handler? Нет
propagate? Да
      ▼
"app" Handler? Нет
propagate? Да
      ▼
Root Logger Handler? Нет
      ▼
❌ Сообщение потеряно, ничего не выведется
```

**Но если ранее был вызван `logging.basicConfig()`:**

```
logging.basicConfig()  # создал Root Logger с StreamHandler

logger = logging.getLogger("app.db")
logger.info("сообщение")
      ▼
Root Logger Handler? Да ✓
      ▼
Сообщение выведется в консоль
```

---

## Практический пример: полная схема

```python
import logging
import sys
from datetime import datetime

# Создание Logger
logger = logging.getLogger("myapp")
logger.setLevel(logging.DEBUG)

# Создание Handler
file_handler = logging.FileHandler("app.log", encoding="UTF-8")
file_handler.setLevel(logging.INFO)

console_handler = logging.StreamHandler(sys.stdout)
console_handler.setLevel(logging.WARNING)

# Создание Formatter
file_formatter = logging.Formatter(
    "[%(levelname)s] %(created)s | %(message)s"
)
console_formatter = logging.Formatter(
    "⚠️ %(levelname)s: %(message)s"
)

# Присоединение
file_handler.setFormatter(file_formatter)
console_handler.setFormatter(console_formatter)

logger.addHandler(file_handler)
logger.addHandler(console_handler)

# Использование
logger.debug("отладка")        # ❌ Logger OK, но оба Handler отклонят
logger.info("информация")      # ✓ в файл (Handler ОК), ❌ в консоль
logger.warning("внимание")     # ✓ в файл и консоль
logger.error("ошибка")         # ✓ в файл и консоль
```

**Путь сообщения `logger.info("информация")`:**
1. Logger проверяет: DEBUG ≤ INFO ✓
2. FileHandler проверяет: DEBUG ≤ INFO ✓ → пишет в app.log
3. ConsoleHandler проверяет: WARNING ≤ INFO ❌ → игнорирует

---

## Главные правила (Запомнить)

1. **Logger — входная дверь**, Handler — маршруты, Formatter — украшение
   - Logger решает, обработать вообще
   - Handler решает, куда отправить
   - Formatter решает, как выглядит

2. **Один Logger → много Handler**, один Handler → один Formatter
   - Если нужны разные форматы → разные Handler

3. **propagate — механизм всплытия**
   - Если Logger не найдёт обработчик → проверяет propagate
   - Если true → идёт к родителю → пока не найдёт обработчик или не дойдёт до Root

4. **Проверка уровня на двух уровнях**
   - Logger.level — первый фильтр
   - Handler.level — второй фильтр
   - Сообщение должно пройти обе проверки

5. **extra — твои собственные поля в LogRecord**
   - Передаёшь `extra={"key": value}`
   - Используешь в Formatter как `%(key)s`

6. **basicConfig() вызывается автоматически**
   - Только если используешь `logging.info()` (функции модуля)
   - Если используешь именованный Logger — сам настраивай обработчики

7. **Formatter получает готовый LogRecord**
   - Он может использовать любые поля LogRecord
   - Но не может повлиять на то, обработается ли сообщение (это решил уже Handler)

---

## Частые ошибки

❌ **Создал Logger, но сообщения не видны**
- Забыл добавить Handler к Logger
- Решение: `logger.addHandler(handler)`

❌ **Вижу сообщения из разных модулей одновременно**
- propagate = True (по умолчанию)
- Root Logger обрабатывает всё
- Решение: `logger.propagate = False` если нужно изолировать

❌ **Некоторые сообщения не попадают в файл**
- Handler.level выше чем Logger.level
- Решение: проверь оба уровня

❌ **Один и тот же Logger в разных модулях — конфликт**
- Используй `logging.getLogger(__name__)` (одиночное имя для каждого модуля)
- Или используй иерархию: `logging.getLogger("myapp.module")`

❌ **Форматер не подхватывает моё поле из extra**
- Забыл шаблон в Formatter: `"%(my_field)s"`
- Решение: добавить в Formatter.format строку

---

## Связь с фильтрами (Filter)

```
LogRecord → Logger.filters → Handler.filters → обработка
              │                    │
              └─ вернул False → ❌ отклонить
              └─ вернул True  → ✓ пропустить
```

Filter — промежуточный этап между Logger/Handler и обработкой. Подробнее → [[05_Filters]]

---

## Ключевые связи (Quick Reference)

| Вопрос | Ответ |
|--------|-------|
| Куда попадает сообщение? | В Handler, которые присоединены к Logger (или Root) |
| Как решается формат? | Formatter, присоединённый к Handler |
| Почему сообщение потеряно? | Нет Handler у Logger и propagate пошёл в пусто (Root без Handler) |
| Как добавить свои поля? | `extra={"key": value}` и `%(key)s` в Formatter |
| Как разные форматы для разных мест? | Разные Handler с разными Formatter |
| Как отключить распространение? | `logger.propagate = False` |
| Как изменить минимальный уровень? | `logger.setLevel()` или `handler.setLevel()` |
