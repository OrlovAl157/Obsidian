---
tags:
  - python
  - тема/строки
  - тема/bytes
  - статус/завершён
уровень: средний
---

```python
# 🔢 BYTES И BYTEARRAY
'Beegeek'.encode('utf-8')        # str → bytes
b'Beegeek'.decode('utf-8')       # bytes → str
bytes('Beegeek', encoding='utf-8')      # явный конструктор (кодировка обязательна)
bytearray('Beegeek', encoding='utf-8')  # изменяемый аналог bytes

ba = bytearray(b'Beegeek')
ba[0] = 98                       # изменение элемента — только число!
```

# 🔢 Памятка: типы bytes и bytearray

`bytes` и `bytearray` — последовательности байтов в Python. `bytes` неизменяемый (как `str`), `bytearray` изменяемый (как `list`).

## Содержание

- [[#🔡 Символ — три представления|Символ — три представления]]
- [[#📦 Тип bytes|Тип bytes]]
- [[#✏️ Тип bytearray|Тип bytearray]]
- [[#🔢 Индексация и срезы|Индексация и срезы]]
- [[#🔧 Особенности индексации bytearray|Особенности индексации bytearray]]
- [[#💾 Занимаемый объём памяти|Занимаемый объём памяти]]
- [[#⚙️ Параметр errors|Параметр errors]]
- [[#🔁 Итерация и создание из чисел|Итерация и создание из чисел]]
- [[#⚠️ Важные особенности|Важные особенности]]

---

### 💡 Ключевые особенности

- **Любой символ** имеет три представления: графическое (`B`, `🐝`), идентификатор/code point (`U+0042`), байтовое (`b'\x42'`).
- **`bytes`** — неизменяемая байтовая строка, ведёт себя как `str`, но хранит числа 0–255.
- **`bytearray`** — изменяемый аналог `bytes`, поддерживает методы списков (`append`, `extend`, `pop`).
- **При создании** `bytes(s, encoding=...)` и `bytearray(s, encoding=...)` кодировка **обязательна** (в отличие от `str.encode()`, где есть значение по умолчанию `utf-8`).
- **Байтовый литерал** (`b'...'`) может содержать только ASCII-символы — `b'🐝'` вызовет `SyntaxError`.
- **Индексация** `bytes`/`bytearray` по одному элементу возвращает `int` (0–255), не байт. Чтобы получить объект — нужен срез длины 1.

### 💡 Полезные советы

- Для перевода строки в байты предпочитай `str.encode()` — кодировка не обязательна (по умолчанию `utf-8`).
- Для явного конструктора `bytes(s, encoding=...)` кодировку указывать обязательно.
- Чтобы изменить элемент `bytearray` — присваивай число, а не байтовую строку.
- `bytes`/`bytearray` экономят память по сравнению с `str`, особенно для текста с многобайтовыми символами.

### 🚀 Быстрая справка

| **Операция** | **Синтаксис** | **Результат** |
|---|---|---|
| str → bytes (метод) | `'A'.encode('utf-8')` | `b'A'` |
| str → bytes (конструктор) | `bytes('A', encoding='utf-8')` | `b'A'` |
| bytes → str | `b'A'.decode('utf-8')` | `'A'` |
| создать bytearray | `bytearray('A', encoding='utf-8')` | `bytearray(b'A')` |
| индекс → число | `b'A'[0]` | `65` |
| срез → объект | `b'A'[0:1]` | `b'A'` |
| изменить элемент | `ba[0] = 98` | меняет байт на `98` |

---

## 🔡 Символ — три представления

**Назначение:** понимать, что один символ — это три разных взгляда на одно и то же.

| Представление | Что это | Пример для `🐝` |
|---|---|---|
| Графическое | внешний вид | `🐝` |
| Идентификатор (code point) | номер в Unicode | `U+1F41D` |
| Байтовое | последовательность байт по кодировке | `b'\xf0\x9f\x90\x9d'` (UTF-8, 4 байта) |

**Примеры:**

```python
print(chr(128029))     # 🐝
print(chr(0x1f41d))    # 🐝  -- то же число в hex

print(chr(66))          # B
print(chr(0x0042))      # B
```

**Важное:** один и тот же символ в разных кодировках имеет **разное** байтовое представление: `🐝` в UTF-8 — 4 байта, в UTF-16 — 5 байт.

---

## 📦 Тип bytes

**Назначение:** неизменяемая последовательность байтов — байтовый аналог `str`.

| Способ создания | Кодировка обязательна? |
|---|---|
| `str.encode(encoding='utf-8')` | нет, по умолчанию `utf-8` |
| `bytes(string, encoding=...)` | **да** |

**Примеры:**

```python
bee = b'\x42\x65\x65\x67\x65\x65\x6b\x20\xf0\x9f\x90\x9d'
print(bee)        # b'Beegeek \xf0\x9f\x90\x9d'
print(type(bee))  # <class 'bytes'>
```

```python
# Создание через encode()
bee = 'Beegeek 🐝'
bee_bytes = bee.encode()              # кодировка по умолчанию utf-8
bee_bytes = bee.encode(encoding='utf-8')  # то же самое явно

print(len(bee))         # 9   -- символов
print(len(bee_bytes))   # 12  -- байт (🐝 занимает 4 байта)
```

```python
# Создание через конструктор bytes() -- кодировка ОБЯЗАТЕЛЬНА
bee_bytes = bytes('Beegeek 🐝', encoding='utf-8')   # ✅

bee_bytes = bytes('Beegeek 🐝')
# ❌ TypeError: string argument without an encoding
```

```python
# Обратное преобразование -- decode()
bee_bytes = 'Beegeek 🐝'.encode()
print(bee_bytes.decode(encoding='utf-8'))   # 'Beegeek 🐝'
print(bee_bytes.decode())                   # то же -- utf-8 по умолчанию
```

```python
# bytes поддерживает большинство методов str
bee_bytes = 'Beegeek 🐝'.encode()
print(bee_bytes.count(b'e'))    # 4
print(bee_bytes.upper())        # b'BEEGEEK \xf0\x9f\x90\x9d'
print(bee_bytes.isdigit())      # False
```

### Правила отображения байтовой строки

| Диапазон байта | Что выводится |
|---|---|
| `\x20`–`\x7e` (32–126) | сам ASCII-символ |
| таб, новая строка, `\`, возврат каретки | `\t`, `\n`, `\\`, `\r` |
| всё остальное | hex-представление `\xNN` |

```python
print(b'\x20')    # b' '       -- печатный символ
print(b'\x1f')    # b'\x1f'    -- непечатный, hex
print(b'\x7e')    # b'~'
print(b'\x7f')    # b'\x7f'
print(b'\n\r\t\\')  # b'\n\r\t\\'
```

```python
# ❗ Байтовый литерал — только ASCII!
bee = b'Beegeek 🐝'
# ❌ SyntaxError: bytes can only contain ASCII literal characters
```

---

## ✏️ Тип bytearray

**Назначение:** изменяемый аналог `bytes` — поддерживает методы списков.

**Примеры:**

```python
bee = 'Beegeek 🐝'
bee_bytearray = bytearray(bee, encoding='utf-8')   # кодировка обязательна

print(bee_bytearray)       # bytearray(b'Beegeek \xf0\x9f\x90\x9d')
print(len(bee_bytearray))  # 12
print(type(bee_bytearray)) # <class 'bytearray'>
```

```python
# bytearray реализует MutableSequence -- методы как у списка
bee_bytearray = bytearray('Beegeek 🐝', encoding='utf-8')

print(bee_bytearray.decode())     # Beegeek 🐝
bee_bytearray.extend(b'\xf0\x9f\x90\x9d')
print(bee_bytearray.decode())     # Beegeek 🐝🐝
```

**Важное:** `decode()` у `bytearray`, как и у `bytes`, не требует обязательной кодировки — по умолчанию `utf-8`. Обязательна она только при **создании** через конструктор.

---

## 🔢 Индексация и срезы

**Назначение:** `bytes`/`bytearray` — последовательности, поддерживают индекс и срез, но индекс возвращает **число**, не байт.

**Примеры:**

```python
bee_bytes = bytes('Beegeek 🐝', encoding='utf-8')
bee_bytearray = bytearray('Beegeek 🐝', encoding='utf-8')

print(bee_bytes[0])      # 66    -- число, не b'B'!
print(bee_bytes[-3])     # 159
print(bee_bytes[::2])    # b'Beek\xf0\x90'

print(bee_bytearray[2])  # 101
print(bee_bytearray[2:5])  # bytearray(b'ege')
```

```python
# Чтобы получить объект, а не число -- срез длины 1
bee_bytes = bytes('Beegeek 🐝', encoding='utf-8')
print(bee_bytes[0:1])    # b'B'  -- объект bytes
print(bee_bytes[0])      # 66    -- просто int
```

**Важное:** `bee_bytes[0]` — это `int`, а `bee_bytes[0:1]` — это `bytes`. Это частый источник путаницы.

---

## 🔧 Особенности индексации bytearray

**Назначение:** изменение элемента `bytearray` по индексу требует **числа**, не байтовой строки.

**Примеры:**

```python
bee_bytearray = bytearray('Beegeek 🐝', encoding='utf-8')

bee_bytearray[0] = b'b'
# ❌ TypeError: 'bytes' object cannot be interpreted as an integer
```

```python
# ✅ Правильно -- присваиваем число
bee_bytearray = bytearray('Beegeek 🐝', encoding='utf-8')

bee_bytearray[0] = 98              # явное число
bee_bytearray[0] = b'B'[0]         # число через индекс другого bytes
bee_bytearray[0] = ord('b')        # число через ord() -- префикс b не нужен

print(bee_bytearray)   # bytearray(b'beegeek \xf0\x9f\x90\x9d')
```

**Важное:** `bytearray` — последовательность чисел (0–255), а не байтов-объектов. Присвоить можно только число.

---

## 💾 Занимаемый объём памяти

**Назначение:** `bytes`/`bytearray` могут экономить память по сравнению с `str`, особенно для многобайтовых символов.

**Примеры:**

```python
import sys

bee = 'Beegeek 🐝' * 1000000
bee_bytes = bytes(bee, encoding='utf-8')
bee_bytearray = bytearray(bee, encoding='utf-8')

print(sys.getsizeof(bee))          # 36000076
print(sys.getsizeof(bee_bytes))    # 12000033
print(sys.getsizeof(bee_bytearray))# 12000057
```

```python
# Для чистого ASCII разница почти отсутствует
bee = 'Beegeek' * 1000000
bee_bytes = bytes(bee, encoding='utf-8')

print(sys.getsizeof(bee))        # 7000049
print(sys.getsizeof(bee_bytes))  # 7000033
```

**Важное:** экономия памяти зависит от кодировки и от того, сколько символов в тексте многобайтовые (эмодзи, кириллица и т.д.). Для чистого ASCII разница незначительна.

---

## ⚙️ Параметр errors

**Назначение:** управляет поведением `encode()`/`decode()`/конструкторов при встрече символа, которого нет в выбранной кодировке.

| Значение `errors` | Поведение |
|---|---|
| `'strict'` (по умолчанию) | возбуждает исключение |
| `'replace'` | заменяет на `?` |
| `'ignore'` | пропускает символ |

**Примеры:**

```python
bee = 'Beegeek 🐝'

bytes(bee, encoding='ascii', errors='strict')
# ❌ UnicodeEncodeError: 'ascii' codec can't encode character '\U0001f41d'...
```

```python
bee_bytes = bytes(bee, encoding='ascii', errors='replace')
print(bee_bytes)   # b'Beegeek ?'

bee_bytearray = bytearray(bee, encoding='ascii', errors='ignore')
print(bee_bytearray)   # bytearray(b'Beegeek ')
```

---

## 🔁 Итерация и создание из чисел

**Назначение:** `bytes`/`bytearray` — итерируемые объекты, состоящие из чисел 0–255; можно создавать их и из таких чисел.

**Примеры:**

```python
bee_bytes = bytes('Beegeek 🐝', encoding='utf-8')

print(list(bee_bytes))   # [66, 101, 101, 103, ...]
print(tuple(bee_bytes))  # (66, 101, 101, 103, ...)

iter_bee = iter(bee_bytes)
print(type(iter_bee))    # <class 'bytes_iterator'>
```

```python
# Создание из произвольного итерируемого с числами 0-255
list_of_ints = [66, 101, 101, 103]
print(bytes(list_of_ints))      # b'Beeg'
print(bytearray(list_of_ints))  # bytearray(b'Beeg')
```

```python
# bytes и bytearray можно создавать друг из друга -- без кодировки
bee_bytes = bytes('Beegeek', encoding='utf-8')
bee_bytearray = bytearray(bee_bytes)   # кодировка НЕ нужна

print(bytes(bee_bytearray))    # b'Beegeek'
print(bytearray(bee_bytes))    # bytearray(b'Beegeek')
```

---

## ⚠️ Важные особенности

```python
# ❗ Байтовый литерал -- только ASCII
b'🐝'   # ❌ SyntaxError

# ❗ Конструктор bytes()/bytearray() требует кодировку явно
bytes('A')   # ❌ TypeError: string argument without an encoding
'A'.encode() # ✅ кодировка по умолчанию utf-8

# ❗ Индекс возвращает int, не байт
b'A'[0]      # → 65 (int)
b'A'[0:1]    # → b'A' (bytes)

# ❗ Присваивание элементу bytearray -- только число
ba[0] = b'b'    # ❌ TypeError
ba[0] = 98      # ✅
ba[0] = ord('b')  # ✅

# ❗ bytes неизменяем -- как str
bb = b'abc'
bb[0] = 65   # ❌ TypeError: 'bytes' object does not support item assignment
```

---

## 🔗 Связанные темы

- [[04 — 🌍 Unicode, UTF-8 и кодировки]]
- [[00 — 📖 Основы строк 🔹]]
- [[01 — 🔤 Все виды строк в Python]]

---

#python/bytes #encoding #strings #bytearray
