---
tags:
  - python
  - тема/типы_данных
  - тема/строки
  - статус/завершён
---

# 📝 Строки (str)

## 📌 Коротко

> Строки в Python — неизменяемая последовательность символов Unicode. Поддерживают индексацию, срезы, множество методов для поиска, замены, форматирования и проверки содержимого.

---

## 📖 Теория

### Основы

**Строки (str)** — один из базовых типов данных в Python. Это последовательность символов Unicode, которую нельзя изменить после создания (immutable).

**Ключевые особенности:**
- ✅ **Неизменяемы** — все методы возвращают новую строку
- ✅ **Unicode** — поддержка любых языков и эмодзи
- ✅ **Индексация с 0** — первый символ имеет индекс 0
- ✅ **Отрицательные индексы** — -1 это последний символ
- ✅ **Срезы** — можно получать подстроки
- ✅ **Множество методов** — для любых операций со строками

### Создание строк

```python
# Одинарные и двойные кавычки
text1 = 'Hello'
text2 = "World"
quote = "He said 'Hello'"
quote2 = 'She said "Hi"'

# Тройные кавычки (многострочные)
multiline = """Это
многострочный
текст"""

multiline2 = '''Первая строка
Вторая строка
Третья строка'''

# Raw строки (игнорируют escape-последовательности)
path = r"C:\new\folder\test"    # без raw было бы C:
ewfolder	est
regex = r"\d+\.\d+"

# Пустая строка
empty = ""
empty = str()

# Повторение строк
repeated = "ha" * 3             # → "hahaha"
dashes = "-" * 20               # → "--------------------"

# Из других типов
number = str(123)               # → "123"
float_str = str(3.14)           # → "3.14"
bool_str = str(True)            # → "True"
```

---

## 💻 Примеры кода

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
text[:5]                        # → "Hello" (с начала)
text[6:]                        # → "World" (до конца)
text[:]                         # → "Hello World" (копия)

# С шагом
text[::2]                       # → "HloWrd" (каждый второй)
text[1::2]                      # → "el ol"
text[::-1]                      # → "dlroW olleH" (реверс)

# Отрицательные индексы в срезах
text[-5:]                       # → "World" (последние 5)
text[:-6]                       # → "Hello" (без последних 6)

# Длина строки
len(text)                       # → 11
```

### Поиск и проверка

```python
text = "Hello World, Hello Python"

# Найти индекс первого вхождения
text.find("World")              # → 6
text.find("xyz")                # → -1 (если не найдено)
text.find("Hello", 5)           # → 13 (поиск начиная с индекса 5)

# Поиск справа
text.rfind("Hello")             # → 13 (последнее вхождение)
text.rfind("l")                 # → 22

# index (как find, но вызывает исключение)
text.index("World")             # → 6
# text.index("xyz")             # → ValueError!

# Количество вхождений
text.count("l")                 # → 4
text.count("Hello")             # → 2
text.count("xyz")               # → 0

# Проверка наличия
"World" in text                 # → True
"xyz" in text                   # → False
"xyz" not in text               # → True
```

### Проверка начала и конца

```python
text = "Hello World"

text.startswith("Hello")        # → True
text.startswith("World")        # → False
text.startswith(("Hi", "Hello"))  # → True (кортеж вариантов)

text.endswith("World")          # → True
text.endswith("!")              # → False
text.endswith((".txt", ".py"))  # → False

# С указанием диапазона
text.startswith("World", 6)     # → True (с 6-го индекса)
text.endswith("Hello", 0, 5)    # → True (в диапазоне [0:5])
```

### Разделение и объединение

```python
# По разделителю
text = "apple,banana,cherry"
text.split(",")                 # → ['apple', 'banana', 'cherry']

# Ограничение количества разделений
text.split(",", 1)              # → ['apple', 'banana,cherry']

# По пробелам (по умолчанию)
text = "apple  banana   cherry"
text.split()                    # → ['apple', 'banana', 'cherry']

# Разделение справа
text = "a.b.c.d"
text.rsplit(".", 1)             # → ['a.b.c', 'd']

# По строкам
text = "line1\nline2\nline3"
text.splitlines()               # → ['line1', 'line2', 'line3']
text.splitlines(keepends=True)  # → ['line1\n', 'line2\n', 'line3']

# Разделение на 3 части
text = "user@example.com"
text.partition("@")             # → ('user', '@', 'example.com')
text.rpartition(".")            # → ('user@example', '.', 'com')

# Объединение строк
words = ["apple", "banana", "cherry"]

", ".join(words)                # → "apple, banana, cherry"
" ".join(words)                 # → "apple banana cherry"
"".join(words)                  # → "applebananacherry"
"-".join(words)                 # → "apple-banana-cherry"

# Объединить числа (нужно преобразовать в строки)
numbers = [1, 2, 3]
", ".join(map(str, numbers))    # → "1, 2, 3"

# Объединить строки напрямую
result = "Hello" + " " + "World"  # → "Hello World"
```

### Замена и удаление

```python
text = "Hello World, Hello Python"

# Заменить все вхождения
text.replace("Hello", "Hi")     # → "Hi World, Hi Python"

# Ограничить количество замен
text.replace("Hello", "Hi", 1)  # → "Hi World, Hello Python"

# Удалить подстроку (заменить на пустую)
text.replace("Hello ", "")      # → "World, Python"

# Множественные замены
text = "I like cats and cats like me"
text.replace("cats", "dogs").replace("like", "love")
# → "I love dogs and dogs love me"

# Удаление символов с краёв
text = "  Hello World  "

text.strip()                    # → "Hello World"
text.lstrip()                   # → "Hello World  " (слева)
text.rstrip()                   # → "  Hello World" (справа)

# Удалить определённые символы
text = "---Hello World---"
text.strip("-")                 # → "Hello World"
text.lstrip("-")                # → "Hello World---"

# Удалить любые указанные символы
text = "***Hello!!!***"
text.strip("*!")                # → "Hello"

# Удалить префикс и суффикс (Python 3.9+)
text = "HelloWorld"
text.removeprefix("Hello")      # → "World"
text.removesuffix("World")      # → "Hello"
```

### Изменение регистра

```python
text = "Hello World"

# Основные преобразования
text.upper()                    # → "HELLO WORLD"
text.lower()                    # → "hello world"
text.capitalize()               # → "Hello world" (первая буква)
text.title()                    # → "Hello World" (каждое слово)
text.swapcase()                 # → "hELLO wORLD" (инверсия)

# Специальные случаи
text = "hello WORLD"
text.capitalize()               # → "Hello world" (остальные в нижний)

# Заголовок с особенностями
"hello's world".title()         # → "Hello'S World" (не идеально)
"hello world 2024".title()      # → "Hello World 2024"

# Case folding (для сравнения)
"Hello".casefold()              # → "hello" (агрессивнее lower)
"ß".lower()                     # → "ß"
"ß".casefold()                  # → "ss" (для немецкого)
```

### Выравнивание и заполнение

```python
text = "Hello"

# Выравнивание
text.center(10)                 # → "  Hello   " (по центру)
text.ljust(10)                  # → "Hello     " (слева)
text.rjust(10)                  # → "     Hello" (справа)

# С символом-заполнителем
text.center(10, "-")            # → "--Hello---"
text.ljust(10, "*")             # → "Hello*****"
text.rjust(10, "0")             # → "00000Hello"

# Заполнение нулями
"42".zfill(5)                   # → "00042"
"-42".zfill(5)                  # → "-0042" (знак сохраняется)
"3.14".zfill(6)                 # → "003.14"

# Табуляция
text = "Name:\tJohn"
text.expandtabs()               # → "Name:   John" (табы в пробелы)
text.expandtabs(4)              # → "Name:   John" (4 пробела)
```

### Методы проверки содержимого

```python
# Проверка типа символов
"123".isdigit()                 # → True (только цифры)
"abc".isalpha()                 # → True (только буквы)
"abc123".isalnum()              # → True (буквы и цифры)
"   ".isspace()                 # → True (только пробелы)

# Проверка регистра
"HELLO".isupper()               # → True
"hello".islower()               # → True
"Hello World".istitle()         # → True

# Другие проверки
"Hello\n".isprintable()         # → False (есть непечатаемые)
"my_var".isidentifier()         # → True (корректный идентификатор)
"Привет".isascii()              # → False (Python 3.7+)

# Важные различия isdigit/isdecimal/isnumeric
"123".isdecimal()               # → True (строже, только 0-9)
"²".isdigit()                   # → True (надстрочные цифры)
"½".isnumeric()                 # → True (дроби, римские)

# Практический совет: для проверки что строка это число
# используйте isdecimal()
"123".isdecimal()               # → True ← надёжнее
```

### Форматирование строк

```python
# f-строки (Python 3.6+) — Рекомендуется!
name = "John"
age = 25
price = 19.99

f"Меня зовут {name}, мне {age} лет"
# → "Меня зовут John, мне 25 лет"

f"Цена: {price:.2f}₽"           # → "Цена: 19.99₽"
f"{name:>10}"                   # → "      John"
f"{1234567:,}"                  # → "1,234,567"

# Метод format()
"Меня зовут {}, мне {} лет".format(name, age)
"{:.2f}".format(3.14159)        # → "3.14"

# Старый стиль (%)
"Меня зовут %s, мне %d лет" % (name, age)
"Число: %.2f" % 3.14159         # → "Число: 3.14"
```

### Преобразование типов

```python
# str → int
int("123")                      # → 123
int("  42  ")                   # → 42 (пробелы игнорируются)
# int("123.45")                 # ❌ ValueError!
int(float("123.45"))            # → 123 ✅

# str → float
float("3.14")                   # → 3.14
float("  2.5  ")                # → 2.5
float("123")                    # → 123.0 ✅

# str → bool
bool("")                        # → False (пустая строка)
bool("False")                   # → True (любая непустая!)
bool("0")                       # → True (это строка!)

# int/float → str
str(123)                        # → "123"
str(3.14)                       # → "3.14"

# bool → str
str(True)                       # → "True"
str(False)                      # → "False"
```

### maketrans() и translate()

```python
# Замена символов (быстрее чем много .replace())
table = str.maketrans("aeiou", "AEIOU")
text = "hello world"
text.translate(table)           # → "hEllO wOrld"

# Удаление символов (третий аргумент)
table = str.maketrans("", "", "!?.,;:")
text = "Hello, World! How are you?"
text.translate(table)           # → "Hello World How are you"

# Удалить все цифры
table = str.maketrans("", "", "0123456789")
"abc123def456".translate(table) # → "abcdef"

# Замена и удаление одновременно
table = str.maketrans("aeiou", "AEIOU", " ")
"hello world".translate(table)  # → "hEllOwOrld"

# Удалить знаки препинания
import string
table = str.maketrans("", "", string.punctuation)
"Hello, World! #Python".translate(table)  # → "Hello World Python"
```

### Кодирование (str ↔ bytes)

```python
text = "Привет, мир!"

# str → bytes
encoded_utf8 = text.encode('utf-8')     # → b'\xd0\x9f\xd1\x80...'
encoded_cp1251 = text.encode('cp1251')  # → другая кодировка

# bytes → str
decoded = encoded_utf8.decode('utf-8')  # → "Привет, мир!"

# Обработка ошибок декодирования
bad_bytes = b'\xff\xfe'
bad_bytes.decode('utf-8', errors='ignore')   # → '' (пропустить)
bad_bytes.decode('utf-8', errors='replace')  # → '��' (заменить)
# bad_bytes.decode('utf-8', errors='strict') # → UnicodeDecodeError

# ASCII представление
ascii(text)                     # → "'\\u041f\\u0440\\u0438..."
repr(text)                      # → "'Привет, мир!'"
```

### Практические примеры

#### Проверка email

```python
email = "user@example.com"
if "@" in email and "." in email:
    username, domain = email.split("@")
    print(f"Пользователь: {username}")
    print(f"Домен: {domain}")
```

#### Очистка и нормализация

```python
user_input = "  HELLO world!  "
cleaned = user_input.strip().lower()  # → "hello world!"

# Удалить все пробелы
no_spaces = user_input.replace(" ", "")  # → "HELLOworld!"
```

#### Создание slug из заголовка

```python
title = "Мой Новый Пост!"
slug = title.lower().replace(" ", "-").strip("!")
# → "мой-новый-пост"

# Более продвинутый вариант
import re
slug = re.sub(r'[^\w\s-]', '', title.lower())
slug = re.sub(r'[-\s]+', '-', slug)
```

#### Маскирование чувствительных данных

```python
card_number = "1234567890123456"
masked = card_number[:4] + "*" * 8 + card_number[-4:]
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

#### Извлечение чисел из строки

```python
text = "Заказ №123: 5 яблок по 50₽"

# Все числа
import re
numbers = re.findall(r'\d+', text)  # → ['123', '5', '50']

# Или вручную
numbers = [int(s) for s in text.split() if s.isdigit()]
```

#### Палиндром

```python
def is_palindrome(text):
    cleaned = text.lower().replace(" ", "")
    return cleaned == cleaned[::-1]

is_palindrome("А роза упала на лапу Азора")  # → True
is_palindrome("Hello")                       # → False
```

#### Цензура слов

```python
def censor_words(text, bad_words):
    for word in bad_words:
        text = text.replace(word, "*" * len(word))
    return text

text = "This is bad and ugly"
censored = censor_words(text, ["bad", "ugly"])
# → "This is *** and ****"
```

### Цепочки методов

```python
text = "  HELLO WORLD  "

# Последовательные преобразования
result = text.strip().lower().replace(" ", "_")
# → "hello_world"

# Сложная обработка
data = "Name: John Doe; Age: 30"
name = data.split(";")[0].split(":")[1].strip()
# → "John Doe"

# Очистка данных
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
"\r"        # Возврат каретки
"\b"        # Backspace

# Unicode
"\u0041"    # → "A" (Unicode код)
"\U00000041" # → "A" (расширенный)
"\N{FACE WITH TEARS OF JOY}"  # → "😂" (по имени)

# Примеры
print("Строка1\nСтрока2")
print("Колонка1\tКолонка2")
print("Путь: C:\\Users\\Name")
print('He said: "Hello"')
```

### Частые ошибки

```python
# ❌ Попытка изменить строку (строки неизменяемы!)
text = "Hello"
# text[0] = "h"                 # TypeError!

# ✅ Создайте новую строку
text = "h" + text[1:]           # → "hello"

# ❌ Сравнение через is вместо ==
user_input = "yes"
# if user_input is "yes":       # Ненадёжно!

# ✅ Используйте ==
if user_input == "yes":         # Надёжно

# ❌ Неправильное преобразование в число
# int("123.45")                 # ValueError!

# ✅ Сначала в float, потом в int
int(float("123.45"))            # → 123

# ⚠️ bool("False") возвращает True
bool("False")                   # → True (любая непустая строка!)
bool("")                        # → False (только пустая)

# ⚠️ join() вызывается на разделителе, не на списке
words = ["a", "b", "c"]
# words.join(" ")               # AttributeError!
" ".join(words)                 # → "a b c" ✅

# ⚠️ split() без аргументов работает по-особому
"a  b   c".split()              # → ['a', 'b', 'c'] (убирает все пробелы)
"a  b   c".split(" ")           # → ['a', '', 'b', '', '', 'c'] (буквально по пробелу)
```

### Когда использовать строки

**✅ Используйте строки когда:**
- Работа с текстом
- Вывод информации пользователю
- Парсинг и обработка данных
- Работа с файлами (текст)
- Регулярные выражения
- API запросы/ответы (JSON, XML)

**❌ НЕ используйте строки когда:**
- Нужна высокая производительность конкатенации (используйте list + join)
- Храните большие объёмы текста в памяти (используйте генераторы)
- Нужна изменяемая последовательность символов (используйте list или bytearray)

---

## 🔗 Связанные темы

- [[f-строки]]
- [[format()]]
- [[Срезы (Slices)]]
- [[Регулярные выражения]]
- [[Кодировки и Unicode]]

## ❓ Вопросы / Непонятное

-
