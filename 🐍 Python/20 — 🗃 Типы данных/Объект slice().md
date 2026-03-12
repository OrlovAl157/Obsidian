---
tags:
  - python
  - тема/типы_данных
  - статус/завершён
уровень: средний
---

# 🔪 Объект slice()

## 📌 Коротко

> slice() создаёт объект среза для переиспользования. Эквивалент `[start:stop:step]`, но в виде отдельного объекта с именем.

---

## 💡 Полезные советы

- **Переиспользование** — создали один раз, применяем много раз
- **Читаемость** — дайте срезу понятное имя вместо загадочных индексов
- **Фиксированные форматы** — идеально для парсинга данных с фиксированной структурой
- **Эквивалентен синтаксису `[]`** — `slice(1,5)` = `[1:5]`
- **Атрибуты доступны** — `.start`, `.stop`, `.step`
- **Неизменяемый** — нельзя изменить после создания

## 🚀 Быстрая справка

| Задача | Способ | Пример |
|--------|--------|--------|
| Создать срез | `slice(stop)` | `s = slice(5)` → эквивалент `[:5]` |
| С началом | `slice(start, stop)` | `s = slice(1, 5)` → `[1:5]` |
| С шагом | `slice(start, stop, step)` | `s = slice(0, 10, 2)` → `[0:10:2]` |
| Применить | `seq[s]` | `text[slice(1, 5)]` |
| Атрибуты | `.start`, `.stop`, `.step` | `s.start` → `1` |
| Нормализовать | `.indices(len)` | `s.indices(10)` → `(1, 5, 1)` |

---

## 📖 Теория

### Основы

**slice()** — встроенная функция, создающая объект среза. Это альтернатива синтаксису `[start:stop:step]`, которую удобно использовать для именованных или переиспользуемых срезов.

**Ключевые особенности:**
- ✅ **Переиспользование** — не нужно повторять магические числа
- ✅ **Именование** — `HEADER`, `BODY` вместо `[0:10]`, `[10:-5]`
- ✅ **Эквивалентность** — полностью заменяет синтаксис `[]`
- ⚠️ **Для частого использования** — если срез один раз, проще `[:]`

### Синтаксис

```python
# Создание
s = slice(start, stop, step)

# Применение
sequence[s]

# Эквивалентно
sequence[start:stop:step]

# Примеры
s = slice(1, 5)
text = "abcdef"
text[s]                         # → "bcde" (то же что text[1:5])

s = slice(0, 6, 2)
text[s]                         # → "ace" (то же что text[0:6:2])
```

---

## 💻 Примеры кода

### Создание и использование

```python
# Базовое создание
s = slice(1, 5)
text = "abcdef"
text[s]                         # → "bcde"

# С шагом
s = slice(0, 6, 2)
text[s]                         # → "ace"

# Только stop
s = slice(5)                    # эквивалент [:5]
"abcdefgh"[s]                   # → "abcde"

# С None (пропуск параметров)
s = slice(None, 5, None)        # [:5]
s = slice(2, None, None)        # [2:]
s = slice(None, None, 2)        # [::2]
```

### Сравнение с синтаксисом []

```python
items = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Через []
items[1:5]                      # → [1, 2, 3, 4]

# Через slice()
s = slice(1, 5)
items[s]                        # → [1, 2, 3, 4]

# Эквивалентны!
items[1:5] == items[slice(1, 5)]  # → True
```

### Атрибуты объекта slice

```python
s = slice(1, 5, 2)

# Доступ к параметрам
s.start                         # → 1
s.stop                          # → 5
s.step                          # → 2

# Неизменяемый объект
# s.start = 10                  # AttributeError!
```

### Переиспользование срезов

```python
# Именованные срезы для читаемости
first_three = slice(0, 3)
last_three = slice(-3, None)
middle = slice(2, -2)

data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

data[first_three]               # → [0, 1, 2]
data[last_three]                # → [7, 8, 9]
data[middle]                    # → [2, 3, 4, 5, 6, 7]

# Применение к разным последовательностям
text = "Hello World"
text[first_three]               # → "Hel"
text[last_three]                # → "rld"
```

### Практические примеры

#### Парсинг фиксированных форматов

```python
# Формат: Имя(8) Фамилия(8) Возраст(4) Должность(12)
record = "John    Doe     30  Engineer    "

# Определяем срезы один раз
FIRST_NAME = slice(0, 8)
LAST_NAME = slice(8, 16)
AGE = slice(16, 20)
POSITION = slice(20, 32)

# Используем многократно
first_name = record[FIRST_NAME].strip()   # → "John"
last_name = record[LAST_NAME].strip()     # → "Doe"
age = int(record[AGE].strip())            # → 30
position = record[POSITION].strip()       # → "Engineer"

# Для других записей
another = "Alice   Smith   25  Designer    "
another[FIRST_NAME].strip()     # → "Alice"
```

#### Работа с логами

```python
# Формат: [TIMESTAMP] LEVEL: Message
log_lines = [
    "[2024-10-03 14:30:45] INFO: Server started",
    "[2024-10-03 14:31:12] ERROR: Connection failed",
]

# Определяем срезы
TIMESTAMP = slice(1, 20)
LEVEL = slice(22, 27)
MESSAGE = slice(29, None)

for log in log_lines:
    timestamp = log[TIMESTAMP]
    level = log[LEVEL]
    message = log[MESSAGE]
    print(f"{timestamp} [{level}] {message}")
```

#### Извлечение колонок

```python
# Таблица с фиксированными колонками
rows = [
    "001 John Smith  30  New York  ",
    "002 Alice Brown 25  Boston    ",
]

COL_ID = slice(0, 4)
COL_NAME = slice(4, 16)
COL_AGE = slice(16, 20)
COL_CITY = slice(20, 30)

for row in rows:
    id_val = row[COL_ID].strip()
    name = row[COL_NAME].strip()
    age = row[COL_AGE].strip()
    city = row[COL_CITY].strip()
    print(f"ID: {id_val}, Name: {name}")
```

#### Динамические срезы

```python
def get_percent_slice(data, percent):
    """Получить срез от начала до указанного процента"""
    length = len(data)
    stop = int(length * percent)
    return slice(0, stop)

data = list(range(100))

# Первые 25%
s = get_percent_slice(data, 0.25)
data[s]                         # → [0, 1, ..., 24]

# Первые 50%
s = get_percent_slice(data, 0.5)
data[s]                         # → [0, 1, ..., 49]
```

#### Чередующиеся группы

```python
data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]

# Каждая третья группа
group_0 = slice(0, None, 3)     # [::3]
group_1 = slice(1, None, 3)     # [1::3]
group_2 = slice(2, None, 3)     # [2::3]

data[group_0]                   # → [0, 3, 6, 9]
data[group_1]                   # → [1, 4, 7, 10]
data[group_2]                   # → [2, 5, 8, 11]
```

### Метод indices()

```python
# indices() нормализует срез для данной длины
s = slice(-3, -1)
s.indices(10)                   # → (7, 9, 1)
# Это: start=7, stop=9, step=1

# Примеры
slice(5).indices(10)            # → (0, 5, 1)
slice(None, None, 2).indices(10)  # → (0, 10, 2)
slice(-5, -2).indices(10)       # → (5, 8, 1)

# Полезно для реализации __getitem__
class MySequence:
    def __getitem__(self, key):
        if isinstance(key, slice):
            start, stop, step = key.indices(len(self.data))
            return [self.data[i] for i in range(start, stop, step)]
```

### Когда использовать

**✅ Используйте slice() когда:**
- Один срез применяется много раз
- Хотите дать срезу понятное имя
- Работаете с фиксированными форматами
- Срез вычисляется динамически
- Пишете `__getitem__` в классе

**❌ НЕ используйте slice() когда:**
- Срез используется один раз
- Код становится менее читаемым
- Нет выгоды от переиспользования

```python
# ❌ Излишне
s = slice(0, 5)
data[s]  # проще data[0:5]

# ✅ Оправданно
HEADER = slice(0, 10)
BODY = slice(10, -5)
FOOTER = slice(-5, None)

for packet in packets:
    header = packet[HEADER]
    body = packet[BODY]
    footer = packet[FOOTER]
    process(header, body, footer)
```

---

## 🔗 Связанные темы

- [[Срезы (Slices)]]
- [[01 — 📋 Cписки ( list ) 🔹]]

## ❓ Вопросы / Непонятное

-
