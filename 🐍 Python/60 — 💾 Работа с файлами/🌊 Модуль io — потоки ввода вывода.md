---

tags:

- python
- тема/файлы_и_потоки
- статус/завершён

---

# 🌊 Модуль `io` — потоки ввода/вывода

## 📌 Коротко

> `io` — модуль для работы с потоками данных. Позволяет создавать файлоподобные объекты в памяти (без файла на диске) и управлять потоками ввода/вывода.

---

## 🚀 Быстрая справка

|Класс|Назначение|Данные|Пример|
|---|---|---|---|
|`StringIO`|файл в памяти|текст (str)|`StringIO('hello')`|
|`BytesIO`|файл в памяти|байты (bytes)|`BytesIO(b'hello')`|
|`TextIOWrapper`|текстовый поток|str|`sys.stdout`|
|`BufferedReader`|буферизованное чтение|bytes|внутри `open()`|
|`BufferedWriter`|буферизованное запись|bytes|внутри `open()`|
|`FileIO`|сырой файловый поток|bytes|низкий уровень|

---

## 📖 Теория

### Иерархия потоков

```
IOBase                          — базовый класс всех потоков
├── RawIOBase                   — сырые байты (FileIO)
├── BufferedIOBase              — буферизованные байты
│   ├── BufferedReader          — буферизованное чтение
│   ├── BufferedWriter          — буферизованное запись
│   └── BytesIO                 — байты в памяти
└── TextIOBase                  — текстовые потоки
    ├── TextIOWrapper           — текст поверх буфера (sys.stdout)
    └── StringIO                — текст в памяти
```

### Цепочка `open()` под капотом

```
open('file.txt', 'r', encoding='utf-8')
    ↓
FileIO          — сырые байты из файла (дескриптор №N)
    ↓
BufferedReader  — буферизует байты (по 8192 байт)
    ↓
TextIOWrapper   — декодирует байты → строки (UTF-8)
    ↓
ты работаешь с текстом
```

---

## 💻 Примеры кода

### `StringIO` — текст в памяти

```python
from io import StringIO

# создание
f = StringIO()           # пустой
f = StringIO('hello')    # с начальным текстом

# запись
f.write('world')

# чтение
f.seek(0)                # перематываем в начало!
print(f.read())          # → helloworld

# получить всё содержимое
print(f.getvalue())      # → helloworld (без seek!)

# закрыть
f.close()
```

### `StringIO` — подмена `sys.stdin`

```python
import sys
from io import StringIO

# имитируем ввод пользователя
sys.stdin = StringIO('Саша\n25\n')

name = input('Имя: ')    # → 'Саша'
age = input('Возраст: ') # → '25'

sys.stdin = sys.__stdin__ # возвращаем обратно
```

### `StringIO` — подмена `sys.stdout`

```python
import sys
from io import StringIO

# перехватываем вывод
buffer = StringIO()
sys.stdout = buffer

print('hello')
print('world')

sys.stdout = sys.__stdout__
result = buffer.getvalue()
print(result)   # → 'hello\nworld\n'
```

### `BytesIO` — байты в памяти

```python
from io import BytesIO

f = BytesIO()
f.write(b'hello')        # только байты!

f.seek(0)
print(f.read())          # → b'hello'

# getvalue() — всё содержимое
print(f.getvalue())      # → b'hello'
```

### Методы — одинаковы для `StringIO` и `BytesIO`

```python
from io import StringIO

f = StringIO('hello world')

f.read()          # читать всё
f.read(5)         # читать N символов
f.readline()      # читать строку
f.readlines()     # читать все строки в список

f.write('text')   # записать
f.writelines([])  # записать список

f.tell()          # текущая позиция курсора
f.seek(0)         # переместить курсор
f.seek(0, 2)      # переместить в конец

f.getvalue()      # всё содержимое (без seek!)
f.truncate(5)     # обрезать

f.readable()      # можно читать?
f.writable()      # можно писать?
f.seekable()      # можно seek?

f.close()         # закрыть
f.closed          # закрыт?
```

---

## 💡 Полезные советы

- **`getvalue()`** — возвращает всё содержимое без изменения позиции курсора
- **`seek(0)` перед `read()`** — после записи курсор в конце, нужно перемотать
- **`StringIO` для тестов** — удобно имитировать файлы и потоки без диска
- **`BytesIO`** — используй когда работаешь с изображениями, архивами, бинарными данными
- **`sys.__stdin__`** — оригинальный stdin (с двойным подчёркиванием), всегда можно восстановить

---

## 🔗 Связанные темы

- [[Работа с файлами]]
- [[sys.stdout и sys.stdin]]
- [[Контекстные менеджеры]]