---
tags:
  - python
  - тема/типы_данных
  - тема/строки
  - статус/завершён
  - Immutable
Не изменяемый: true
hashable: true
iterable: true
dict_key: true
---

# 📝 Строки (str)

## 📌 Коротко

> Строки — неизменяемая последовательность символов Unicode. Поддерживают индексацию, срезы и множество методов для работы с текстом.

---

## 💡 Полезные советы

- **Строки неизменяемы** — все методы возвращают новую строку, не изменяя оригинал
- **Цепочки методов** — можно объединять через точку: `text.strip().lower()`
- **Регистр имеет значение** — `"Hello" != "hello"`
- **Индексация с 0** — первый символ имеет индекс 0
- **Отрицательные индексы** — `text[-1]` это последний символ
- **f-строки** — самый современный способ форматирования (Python 3.6+)
- **Raw строки** — используйте `r"..."` для путей и регулярных выражений
- **Для сравнения используйте `==`** — не `is` (is проверяет идентичность объектов)

## 🚀 Быстрая справка

| Задача | Метод | Сложность | Пример |
|--------|-------|-----------|--------|
| Найти подстроку | `.find()` | O(n) | `text.find("abc")` |
| Заменить текст | `.replace()` | O(n) | `text.replace("old", "new")` |
| Разделить строку | `.split()` | O(n) | `text.split(",")` |
| Объединить список | `.join()` | O(n) | `",".join(list)` |
| Убрать пробелы | `.strip()` | O(n) | `text.strip()` |
| В верхний регистр | `.upper()` | O(n) | `text.upper()` |
| В нижний регистр | `.lower()` | O(n) | `text.lower()` |
| Проверить начало | `.startswith()` | O(n) | `text.startswith("abc")` |
| Проверить конец | `.endswith()` | O(n) | `text.endswith("xyz")` |
| Количество вхождений | `.count()` | O(n) | `text.count("abc")` |
| Проверить содержимое | `.isdigit()`, `.isalpha()` | O(n) | `text.isdigit()` |
| Доступ по индексу | `[i]` | O(1) | `text[0]` |
| Срез | `[start:stop:step]` | O(k) | `text[1:5]` |
| Длина | `len()` | O(1) | `len(text)` |

---

## 📖 Теория

### Основы

**Строки (str)** — один из базовых типов данных в Python. Это последовательность символов Unicode.

**Как устроено:**
- **Память**: массив символов Unicode + длина + хеш (для оптимизации)
- **Неизменяемость**: после создания строку нельзя изменить
- **Интернирование**: короткие строки кэшируются для экономии памяти
- **Unicode**: поддержка любых языков и эмодзи

**Ключевые особенности:**
- ✅ **Неизменяемы** — все методы возвращают новую строку
- ✅ **Индексация** — доступ к символам по индексу (с 0)
- ✅ **Срезы** — получение подстрок `[start:stop:step]`
- ✅ **Unicode** — поддержка любых языков
- ✅ **Множество методов** — для любых операций

### Синтаксис

```python
# Создание
text1 = 'Hello'                 # одинарные кавычки
text2 = "World"                 # двойные кавычки
text3 = """Многострочный
текст"""                        # тройные кавычки

# Raw строки (игнорируют escape)
path = r"C:\new\folder"         # без r было бы C:
ew\folder

# Доступ
text = "Hello"
text[0]                         # → "H"
text[-1]                        # → "o"
text[1:4]                       # → "ell"

# Базовые операции
"Hello" + " " + "World"         # → "Hello World" (конкатенация)
"ha" * 3                        # → "hahaha" (повторение)
len("Hello")                    # → 5 (длина)
```

---

## 💻 Примеры кода

### Создание строк

```python
# Обычные строки
text1 = 'Hello'
text2 = "World"
quote = "He said 'Hello'"
quote2 = 'She said "Hi"'

# Многострочные
multiline = """Это
многострочный
текст"""

# Raw строки (для путей, регулярок)
path = r"C:\new\folder\test.txt"
regex = r"\d+\.\d+"

# Пустая строка
empty = ""
empty = str()

# Повторение
repeated = "ha" * 3             # → "hahaha"
dashes = "-" * 20               # → "--------------------"

# Из других типов
str(123)                        # → "123"
str(3.14)                       # → "3.14"
str(True)                       # → "True"
```

### Доступ к символам и срезы

```python
text = "Hello World"

# Индексация
text[0]                         # → "H"
text[6]                         # → "W"
text[-1]                        # → "d" (последний)
text[-2]                        # → "l"

# Срезы
text[0:5]                       # → "Hello"
text[:5]                        # → "Hello"
text[6:]                        # → "World"
text[:]                         # → "Hello World" (копия)

# С шагом
text[::2]                       # → "HloWrd" (каждый второй)
text[::-1]                      # → "dlroW olleH" (реверс)

# Длина
len(text)                       # → 11
```

### Поиск и проверка

```python
text = "Hello World, Hello Python"

# Найти индекс
text.find("World")              # → 6
text.find("xyz")                # → -1 (не найдено)
text.find("Hello", 5)           # → 13 (с позиции 5)

# Поиск справа
text.rfind("Hello")             # → 13

# index (выбрасывает ошибку если не найдено)
text.index("World")             # → 6
# text.index("xyz")             # ValueError!

# Количество вхождений
text.count("l")                 # → 4
text.count("Hello")             # → 2

# Проверка наличия
"World" in text                 # → True
"xyz" not in text               # → True

# Проверка начала/конца
text.startswith("Hello")        # → True
text.endswith("Python")         # → True
text.startswith(("Hi", "Hello"))  # → True (кортеж вариантов)
```

### Разделение и объединение

```python
# Разделение
text = "apple,banana,cherry"
text.split(",")                 # → ['apple', 'banana', 'cherry']

# По пробелам (убирает лишние)
text = "apple  banana   cherry"
text.split()                    # → ['apple', 'banana', 'cherry']

# Ограничение количества
"a,b,c,d".split(",", 2)         # → ['a', 'b', 'c,d']

# По строкам
"line1\nline2\nline3".splitlines()  # → ['line1', 'line2', 'line3']

# Разделение на 3 части
"user@example.com".partition("@")  # → ('user', '@', 'example.com')

# Объединение
words = ["apple", "banana", "cherry"]
", ".join(words)                # → "apple, banana, cherry"
"".join(words)                  # → "applebananacherry"

# Конкатенация
"Hello" + " " + "World"         # → "Hello World"
```

### Замена и удаление

```python
text = "Hello World, Hello Python"

# Замена
text.replace("Hello", "Hi")     # → "Hi World, Hi Python"
text.replace("Hello", "Hi", 1)  # → "Hi World, Hello Python" (макс. 1)

# Удаление подстроки
text.replace("Hello ", "")      # → "World, Python"

# Убрать пробелы с краёв
"  Hello  ".strip()             # → "Hello"
"  Hello  ".lstrip()            # → "Hello  "
"  Hello  ".rstrip()            # → "  Hello"

# Убрать определённые символы
"---Hello---".strip("-")        # → "Hello"
"***Hello!!!***".strip("*!")    # → "Hello"

# Удалить префикс/суффикс (Python 3.9+)
"HelloWorld".removeprefix("Hello")  # → "World"
"HelloWorld".removesuffix("World")  # → "Hello"
```

### Изменение регистра

```python
text = "Hello World"

# Основные преобразования
text.upper()                    # → "HELLO WORLD"
text.lower()                    # → "hello world"
text.capitalize()               # → "Hello world"
text.title()                    # → "Hello World"
text.swapcase()                 # → "hELLO wORLD"

# Case folding (для сравнения)
"Hello".casefold()              # → "hello"
"ß".casefold()                  # → "ss" (для немецкого)
```

### Выравнивание и заполнение

```python
text = "Hello"

# Выравнивание
text.center(10)                 # → "  Hello   "
text.ljust(10)                  # → "Hello     "
text.rjust(10)                  # → "     Hello"

# С заполнителем
text.center(10, "-")            # → "--Hello---"
text.ljust(10, "*")             # → "Hello*****"

# Заполнение нулями
"42".zfill(5)                   # → "00042"
"-42".zfill(5)                  # → "-0042"
```

### Методы проверки

```python
# Типы символов
"123".isdigit()                 # → True
"abc".isalpha()                 # → True
"abc123".isalnum()              # → True
"   ".isspace()                 # → True

# Регистр
"HELLO".isupper()               # → True
"hello".islower()               # → True
"Hello World".istitle()         # → True

# Другие
"Hello\n".isprintable()         # → False
"my_var".isidentifier()         # → True
"Hello".isascii()               # → True (Python 3.7+)

# Важные различия
"123".isdecimal()               # → True (строже, только 0-9)
"²".isdigit()                   # → True (надстрочные)
"½".isnumeric()                 # → True (дроби, римские)
```

### Форматирование

```python
name = "John"
age = 25
price = 19.99

# f-строки (Python 3.6+) — рекомендуется
f"Меня зовут {name}, мне {age} лет"
f"Цена: {price:.2f}₽"           # → "Цена: 19.99₽"
f"{name:>10}"                   # → "      John"

# Метод format()
"Меня зовут {}, мне {} лет".format(name, age)
"{:.2f}".format(3.14159)        # → "3.14"

# Старый стиль %
"Меня зовут %s, мне %d лет" % (name, age)
```

### Преобразование типов

```python
# str → int/float
int("123")                      # → 123
float("3.14")                   # → 3.14
int(float("123.45"))            # → 123

# int/float → str
str(123)                        # → "123"
str(3.14)                       # → "3.14"

# str → bool
bool("")                        # → False (только пустая)
bool("False")                   # → True (любая непустая!)
```

### Кодирование (str ↔ bytes)

```python
text = "Привет"

# str → bytes
encoded = text.encode('utf-8')  # → b'\xd0\x9f\xd1\x80...'

# bytes → str
decoded = encoded.decode('utf-8')  # → "Привет"

# Обработка ошибок
bad_bytes = b'\xff\xfe'
bad_bytes.decode('utf-8', errors='ignore')   # → ''
bad_bytes.decode('utf-8', errors='replace')  # → '��'
```

### translate() — быстрая замена символов

```python
# Замена символов (быстрее чем много .replace())
table = str.maketrans("aeiou", "AEIOU")
"hello world".translate(table)  # → "hEllO wOrld"

# Удаление символов
table = str.maketrans("", "", "!?.,;:")
"Hello, World!".translate(table)  # → "Hello World"

# Удалить цифры
table = str.maketrans("", "", "0123456789")
"abc123def456".translate(table)  # → "abcdef"

# Удалить знаки препинания
import string
table = str.maketrans("", "", string.punctuation)
"Hello, World! #Python".translate(table)  # → "Hello World Python"
```

### Практические примеры

#### Проверка email

```python
email = "user@example.com"
if "@" in email and "." in email:
    username, domain = email.split("@")
    print(f"Пользователь: {username}, Домен: {domain}")
```

#### Очистка данных

```python
user_input = "  HELLO world!  "
cleaned = user_input.strip().lower()  # → "hello world!"

# Удалить все пробелы
no_spaces = user_input.replace(" ", "")  # → "HELLOworld!"
```

#### Создание slug

```python
title = "Мой Новый Пост!"
slug = title.lower().replace(" ", "-").strip("!")
# → "мой-новый-пост"
```

#### Маскирование данных

```python
# Банковская карта
card = "1234567890123456"
masked = card[:4] + "*" * 8 + card[-4:]
# → "1234********3456"

# Email
email = "john.doe@example.com"
username, domain = email.split("@")
masked = username[0] + "*" * (len(username)-2) + username[-1] + "@" + domain
# → "j*******e@example.com"
```

#### Проверка пароля

```python
password = "MyPass123!"

has_upper = any(c.isupper() for c in password)
has_lower = any(c.islower() for c in password)
has_digit = any(c.isdigit() for c in password)
has_special = any(not c.isalnum() for c in password)
is_long = len(password) >= 8

is_strong = all([has_upper, has_lower, has_digit, has_special, is_long])
```

#### Палиндром

```python
def is_palindrome(text):
    cleaned = text.lower().replace(" ", "")
    return cleaned == cleaned[::-1]

is_palindrome("А роза упала на лапу Азора")  # → True
```

#### Подсчёт слов и символов

```python
text = "Hello World! How are you?"

word_count = len(text.split())              # → 5
char_count = len(text.replace(" ", ""))     # → 20
sentence_count = text.count(".") + text.count("!") + text.count("?")
```

### Цепочки методов

```python
# Последовательные преобразования
text = "  HELLO WORLD  "
result = text.strip().lower().replace(" ", "_")
# → "hello_world"

# Очистка email
user_input = "  Email: USER@EXAMPLE.COM  "
email = user_input.split(":")[1].strip().lower()
# → "user@example.com"
```

### Escape-последовательности

```python
# Основные
"\n"        # Новая строка
"\t"        # Табуляция
"\\"        # Обратный слэш
"\'"        # Одинарная кавычка
"\""        # Двойная кавычка

# Unicode
"\u0041"    # → "A"
"\N{FACE WITH TEARS OF JOY}"  # → "😂"

# Примеры
print("Строка1\nСтрока2")
print("Колонка1\tКолонка2")
print("Путь: C:\\Users\\Name")
```

### Частые ошибки

```python
# ❌ Попытка изменить строку
text = "Hello"
# text[0] = "h"                 # TypeError!

# ✅ Создайте новую строку
text = "h" + text[1:]           # → "hello"

# ❌ Сравнение через is
user_input = "yes"
# if user_input is "yes":       # Ненадёжно!

# ✅ Используйте ==
if user_input == "yes":         # Надёжно

# ❌ bool("False") возвращает True
bool("False")                   # → True (любая непустая!)
bool("")                        # → False (только пустая)

# ⚠️ join() вызывается на разделителе
words = ["a", "b", "c"]
# words.join(" ")               # AttributeError!
" ".join(words)                 # → "a b c" ✅

# ⚠️ split() без аргументов vs с пробелом
"a  b   c".split()              # → ['a', 'b', 'c'] (умный)
"a  b   c".split(" ")           # → ['a', '', 'b', '', '', 'c'] (буквально)
```

---

## 🔗 Связанные темы

- [[f-строки]]
- [[format()]]
- [[Срезы (Slices)]]
- [[Регулярные выражения]]

## ❓ Вопросы / Непонятное

-
