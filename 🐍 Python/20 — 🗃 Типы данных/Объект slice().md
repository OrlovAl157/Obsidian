---
tags:
  - python
  - тема/типы_данных
  - тема/последовательности
  - статус/завершён
---

# 🔪 Объект slice()

## 📌 Коротко

> slice() создаёт объект среза, который можно переиспользовать. Эквивалент синтаксиса `[start:stop:step]`, но в виде отдельного объекта. Полезно когда нужно применять один и тот же срез многократно.

---

## 📖 Теория

### Основы

**slice()** — встроенная функция, которая создаёт объект среза. Это альтернатива синтаксису `[start:stop:step]`, которую удобно использовать когда срез нужно сохранить и переиспользовать.

**Ключевые особенности:**
- ✅ **Переиспользование** — создали один раз, применяем много раз
- ✅ **Читаемость** — можно дать срезу понятное имя
- ✅ **Динамические срезы** — можно вычислять параметры
- ✅ **Эквивалентен** синтаксису `[start:stop:step]`

### Синтаксис

```python
# Создание объекта среза
s = slice(start, stop, step)

# Применение среза
sequence[s]

# Эквивалентно
sequence[start:stop:step]
```

---

## 💻 Примеры кода

### Создание и использование

```python
# Базовое создание
s = slice(1, 5)                 # → эквивалент [1:5]
text = "abcdef"
text[s]                         # → "bcde"

# С шагом
s = slice(0, 6, 2)              # → эквивалент [0:6:2]
text = "abcdef"
text[s]                         # → "ace"

# Только stop
s = slice(5)                    # → эквивалент [:5]
text = "abcdefgh"
text[s]                         # → "abcde"

# С None (пропуск параметров)
s = slice(None, 5, None)        # → эквивалент [:5]
s = slice(2, None, None)        # → эквивалент [2:]
s = slice(None, None, 2)        # → эквивалент [::2]
```

### Сравнение с синтаксисом []

```python
items = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Через синтаксис []
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

# Изменение атрибутов невозможно (объект неизменяемый)
# s.start = 10                  # AttributeError!
```

### Переиспользование срезов

```python
# Создаём именованные срезы для читаемости
first_three = slice(0, 3)
last_three = slice(-3, None)
middle = slice(2, -2)

data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

data[first_three]               # → [0, 1, 2]
data[last_three]                # → [7, 8, 9]
data[middle]                    # → [2, 3, 4, 5, 6, 7]

# Применяем к разным последовательностям
text = "Hello World"
text[first_three]               # → "Hel"
text[last_three]                # → "rld"
```

### Практические примеры

#### Работа с фиксированными форматами данных

```python
# Парсинг строки с фиксированными позициями
record = "John    Doe     30  Engineer    "

# Определяем срезы один раз
FIRST_NAME = slice(0, 8)
LAST_NAME = slice(8, 16)
AGE = slice(16, 20)
POSITION = slice(20, 32)

# Используем многократно
first_name = record[FIRST_NAME].strip()  # → "John"
last_name = record[LAST_NAME].strip()    # → "Doe"
age = int(record[AGE].strip())           # → 30
position = record[POSITION].strip()      # → "Engineer"

# Для разных записей
another_record = "Alice   Smith   25  Designer    "
another_first = another_record[FIRST_NAME].strip()  # → "Alice"
```

#### Работа с логами

```python
# Формат лога: [TIMESTAMP] LEVEL: Message
log_lines = [
    "[2024-10-03 14:30:45] INFO: Server started",
    "[2024-10-03 14:31:12] ERROR: Connection failed",
    "[2024-10-03 14:31:45] INFO: Retry successful"
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

#### Извлечение колонок из таблицы

```python
# Таблица с фиксированными колонками
rows = [
    "ID  Name        Age City      ",
    "001 John Smith  30  New York  ",
    "002 Alice Brown 25  Boston    ",
    "003 Bob Johnson 35  Chicago   "
]

# Определяем колонки
COL_ID = slice(0, 4)
COL_NAME = slice(4, 16)
COL_AGE = slice(16, 20)
COL_CITY = slice(20, 30)

# Извлекаем данные
for row in rows[1:]:  # пропускаем заголовок
    id_val = row[COL_ID].strip()
    name = row[COL_NAME].strip()
    age = row[COL_AGE].strip()
    city = row[COL_CITY].strip()
    print(f"ID: {id_val}, Name: {name}, Age: {age}, City: {city}")
```

#### Динамические срезы

```python
# Создание среза на основе условий
def get_range_slice(data, percent_from_start):
    """Получить срез от начала до указанного процента"""
    length = len(data)
    stop = int(length * percent_from_start)
    return slice(0, stop)

data = list(range(100))

# Первые 25%
s = get_range_slice(data, 0.25)
data[s]                         # → [0, 1, 2, ..., 24]

# Первые 50%
s = get_range_slice(data, 0.5)
data[s]                         # → [0, 1, 2, ..., 49]
```

#### Чередующиеся группы

```python
# Разделение данных на группы
data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]

# Каждая третья группа
group_0 = slice(0, None, 3)     # → [::3]
group_1 = slice(1, None, 3)     # → [1::3]
group_2 = slice(2, None, 3)     # → [2::3]

data[group_0]                   # → [0, 3, 6, 9]
data[group_1]                   # → [1, 4, 7, 10]
data[group_2]                   # → [2, 5, 8, 11]
```

### Метод indices()

```python
# indices() нормализует срез для данной длины
s = slice(-3, -1)
s.indices(10)                   # → (7, 9, 1)
# Это означает: start=7, stop=9, step=1

# Полезно для реализации собственных классов
class MySequence:
    def __getitem__(self, key):
        if isinstance(key, slice):
            # Нормализуем срез
            start, stop, step = key.indices(len(self.data))
            # Теперь работаем с конкретными индексами
            return [self.data[i] for i in range(start, stop, step)]

# Примеры indices()
slice(5).indices(10)            # → (0, 5, 1)  для [:5]
slice(None, None, 2).indices(10)  # → (0, 10, 2) для [::2]
slice(-5, -2).indices(10)       # → (5, 8, 1)  для [-5:-2]
```

### Когда использовать slice()

**✅ Используйте slice() когда:**
- Один срез применяется много раз
- Хотите дать срезу понятное имя
- Работаете с фиксированными форматами данных
- Срез вычисляется динамически
- Пишете собственные классы с `__getitem__`

**❌ НЕ используйте slice() когда:**
- Срез используется один раз (проще `[start:stop:step]`)
- Код становится менее читаемым
- Нет выгоды от переиспользования

```python
# ❌ Излишнее использование
s = slice(0, 5)
data[s]  # проще было data[0:5]

# ✅ Оправданное использование
HEADER_SECTION = slice(0, 10)
BODY_SECTION = slice(10, -5)
FOOTER_SECTION = slice(-5, None)

for packet in network_packets:
    header = packet[HEADER_SECTION]
    body = packet[BODY_SECTION]
    footer = packet[FOOTER_SECTION]
    process(header, body, footer)
```

---

## 🔗 Связанные темы

- [[Срезы (Slices)]]
- [[Строки (str)]]
- [[Списки (list)]]
- [[Кортежи (tuple)]]
- [[Индексация]]

## ❓ Вопросы / Непонятное

-
