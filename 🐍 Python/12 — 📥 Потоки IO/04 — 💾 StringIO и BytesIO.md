---
tags:
  - python
  - тема/потоки
  - тема/io
  - статус/завершён
уровень: средний
---

```python
# 💾 STRINGIO И BYTESIO — ПОТОКИ В ПАМЯТИ
from io import StringIO, BytesIO

# StringIO — текстовый буфер в памяти
with StringIO() as buf:
    buf.write('Hello\n')
    buf.seek(0)
    print(buf.read())       # Hello
    print(buf.getvalue())   # Hello (без seek)

# BytesIO — байтовый буфер в памяти
with BytesIO() as buf:
    buf.write(b'Hello')
    print(buf.getvalue())   # b'Hello'
    view = buf.getbuffer()  # memoryview на буфер
```

# 💾 Памятка: StringIO и BytesIO

`StringIO` и `BytesIO` — файловые объекты в памяти (RAM) из модуля `io`. Работают как обычные файлы, но без создания файла на диске.

## Содержание

- [[#🧵 Тип StringIO|Тип StringIO]]
- [[#🔢 Тип BytesIO|Тип BytesIO]]
- [[#🔭 BytesIO и memoryview|BytesIO и memoryview]]
- [[#⚖️ StringIO vs BytesIO|StringIO vs BytesIO]]
- [[#⚠️ Важные особенности|Важные особенности]]

---

### 💡 Ключевые особенности

- **`StringIO`** — текстовый буфер в памяти. Работает как `open(file, 'r')` — читает/пишет строки (`str`).
- **`BytesIO`** — байтовый буфер в памяти. Работает как `open(file, 'rb')` — читает/пишет байты (`bytes`/`bytearray`).
- **Оба** поддерживают все файловые методы: `write()`, `read()`, `seek()`, `tell()`, `close()`.
- **`getvalue()`** — возвращает всё содержимое буфера без перемещения указателя (удобнее, чем `seek(0)` + `read()`).
- **`seek(0)`** нужен перед `read()` потому что после `write()` указатель стоит в конце.
- **Оба** поддерживают контекстный менеджер (`with`) — закрытие и очистка автоматически.
- **`BytesIO.getbuffer()`** — возвращает `memoryview` на буфер. Пока жив хотя бы один `memoryview` — `BytesIO` нельзя закрыть или изменить.

### 💡 Полезные советы

- Используй `getvalue()` вместо `seek(0)` + `read()` — короче и понятнее.
- Предпочитай `with` вместо явного `close()` — не забудешь освободить память.
- `StringIO` не добавляет `\n` автоматически — указывай явно в строке.
- Перед передачей `BytesIO` в другую функцию сделай `seek(0)`, иначе та прочитает пустоту.

### 🚀 Быстрая справка

| **Операция** | **StringIO** | **BytesIO** |
|---|---|---|
| создать | `StringIO()` | `BytesIO()` |
| записать | `buf.write('text')` | `buf.write(b'bytes')` |
| прочитать всё | `buf.seek(0); buf.read()` | `buf.seek(0); buf.read()` |
| прочитать без seek | `buf.getvalue()` | `buf.getvalue()` |
| получить memoryview | — | `buf.getbuffer()` |
| закрыть | `buf.close()` | `buf.close()` |

---

## 🧵 Тип StringIO

**Назначение:** текстовый буфер в памяти — файловый интерфейс без файла для строковых данных.

**Примеры:**

```python
from io import StringIO

# Базовый пример
buffer = StringIO()
buffer.write('Beegeek\n')
buffer.write('🐝')
buffer.seek(0)            # перемещаем указатель в начало
print(buffer.read())      # Beegeek\n🐝
buffer.close()
```

```python
# Через контекстный менеджер (предпочтительно)
with StringIO() as buffer:
    buffer.write('Beegeek\n')
    buffer.write('🐝')
    buffer.seek(0)
    print(buffer.read())
# после with: буфер закрыт, память освобождена
```

```python
# getvalue() — удобнее чем seek(0) + read()
with StringIO() as buffer:
    buffer.write('Beegeek\n')
    buffer.write('🐝')
    print(buffer.getvalue())   # Beegeek\n🐝 -- без seek!
```

```python
# Чтение из закрытого буфера -- ошибка
buffer = StringIO()
buffer.write('Beegeek')
buffer.close()
buffer.read()   # ❌ ValueError: I/O operation on closed file
```

```python
# ❗ StringIO не добавляет \n автоматически
with StringIO() as buf:
    buf.write('строка 1')   # нет \n — следующая строка прилипнет
    buf.write('строка 2')
    print(buf.getvalue())   # строка 1строка 2

with StringIO() as buf:
    buf.write('строка 1\n')  # ✅ явный \n
    buf.write('строка 2')
    print(buf.getvalue())    # строка 1\nстрока 2
```

**Применение:**

```python
# Тестирование — имитация файла без создания файла
from io import StringIO
import sys

def capture_print():
    old_stdout = sys.stdout
    sys.stdout = buffer = StringIO()

    print('тестируемый вывод')

    sys.stdout = old_stdout
    return buffer.getvalue()

result = capture_print()
print(repr(result))   # 'тестируемый вывод\n'
```

**Важное:** `StringIO` часто используется при тестировании (имитация файла), логировании (перехват вывода) и при работе с библиотеками, которые ожидают файловый объект, а не строку.

---

## 🔢 Тип BytesIO

**Назначение:** байтовый буфер в памяти — то же что `StringIO`, но работает с `bytes` и `bytearray`.

**Примеры:**

```python
from io import BytesIO

with BytesIO() as buffer:
    buffer.write(b'Beegeek\n')
    buffer.write(bytearray('🐝', encoding='utf-8'))
    print(buffer.getvalue())
# b'Beegeek\n\xf0\x9f\x90\x9d'
```

```python
# seek(0) перед read() — указатель после write() стоит в конце
with BytesIO() as buffer:
    buffer.write(b'Hello')
    print(buffer.read())    # b''  ← пусто! указатель в конце

with BytesIO() as buffer:
    buffer.write(b'Hello')
    buffer.seek(0)          # ← перемещаем в начало
    print(buffer.read())    # b'Hello' ✅
```

```python
# Применение: передача байтов как файлового объекта
from io import BytesIO
from PIL import Image   # пример с библиотекой изображений

img_bytes = b'...'     # байты изображения
buffer = BytesIO(img_bytes)
# img = Image.open(buffer)  -- открываем как файл
```

**Важное:** `BytesIO` можно сразу инициализировать с данными: `BytesIO(b'initial data')`.

---

## 🔭 BytesIO и memoryview

**Назначение:** `BytesIO` поддерживает буферный протокол — `getbuffer()` даёт `memoryview` на внутренний буфер.

**Примеры:**

```python
from io import BytesIO

with BytesIO() as buffer:
    buffer.write(b'Beegeek\n')
    buffer.write(bytearray('🐝', encoding='utf-8'))

    print(buffer.getbuffer())          # <memory at 0x...>
    print(type(buffer.getbuffer()))    # <class 'memoryview'>
    print(buffer.getbuffer().tobytes()) # b'Beegeek\n\xf0\x9f\x90\x9d'
```

```python
# ❗ Нельзя закрыть BytesIO пока жив memoryview
with BytesIO() as buffer:
    buffer.write(b'Beegeek')
    view = buffer.getbuffer()   # сохранили memoryview
# ❌ BufferError: Existing exports of data: object cannot be re-sized
# with пытается закрыть буфер, но view ещё жив
```

```python
# ✅ Способ 1: удалить memoryview вручную
with BytesIO() as buffer:
    buffer.write(b'Beegeek')
    view = buffer.getbuffer()
    print(view.tobytes())
    del view                   # ← удаляем ссылку перед закрытием
```

```python
# ✅ Способ 2: вложенный контекстный менеджер
with BytesIO() as buffer:
    buffer.write(b'Beegeek')
    with buffer.getbuffer() as view:   # view закроется автоматически
        print(view.tobytes())
# ← здесь view уже удалён, buffer закрывается без ошибки
```

**Важное:** `BytesIO` нельзя закрыть или изменить, пока существует хотя бы один активный `memoryview` на него. Всегда удаляй `memoryview` (`del view` или `with`) перед закрытием буфера.

---

## ⚖️ StringIO vs BytesIO

| | **StringIO** | **BytesIO** |
|---|---|---|
| Тип данных | `str` | `bytes`, `bytearray` |
| Аналог файла | `open(f, 'r')` | `open(f, 'rb')` |
| `write()` принимает | строку | байты |
| `getvalue()` возвращает | строку | байты |
| `getbuffer()` | нет | есть → `memoryview` |
| Поддержка буферного протокола | нет | да |

---

## ⚠️ Важные особенности

```python
# ❗ После write() указатель в конце — read() вернёт пустоту
buf = BytesIO()
buf.write(b'Hello')
buf.read()         # b'' ← пусто!
buf.seek(0)
buf.read()         # b'Hello' ✅

# ❗ getvalue() работает без seek
buf = BytesIO()
buf.write(b'Hello')
buf.getvalue()     # b'Hello' ✅ -- seek не нужен

# ❗ StringIO не добавляет \n
buf = StringIO()
buf.write('line1')
buf.write('line2')
buf.getvalue()     # 'line1line2' -- без разделителя!

# ❗ BytesIO с memoryview нельзя закрыть
with BytesIO() as buf:
    buf.write(b'data')
    view = buf.getbuffer()
# ❌ BufferError -- del view перед выходом из with!

# ❗ Чтение из закрытого буфера
buf = StringIO()
buf.close()
buf.read()   # ❌ ValueError: I/O operation on closed file
```

---

## 🔗 Связанные темы

- [[00 — 📥 stdin, stdout, stderr]]
- [[01 — 🔄 Перенаправление потоков]]
- [[03 — 🖨 print() изнутри]]
- [[06 — 🔬 memoryview и буферный протокол]]
- [[05 — 🔢 bytes и bytearray]]
