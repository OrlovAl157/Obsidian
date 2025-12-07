# Памятка: Строки в Python

## 💡 Полезные советы

- **Строки неизменяемы**: все методы возвращают новую строку, не изменяя оригинал
- **Цепочки методов**: можно объединять методы через точку
- **Регистр имеет значение**: "Hello" ≠ "hello"
- **Индексация с 0**: первый символ имеет индекс 0
- **Отрицательные индексы**: `text[-1]` - последний символ
- **f-строки**: самый современный и удобный способ форматирования (Python 3.6+)
- **Raw строки**: используйте `r"..."` для путей и регулярных выражений
- **Многострочные**: тройные кавычки `"""..."""` или `'''...'''`

## 🚀 Быстрая справка

| Задача               | Метод                      | Пример                       |
| -------------------- | -------------------------- | ---------------------------- |
| Найти подстроку      | `.find()`                  | `text.find("abc")`           |
| Заменить текст       | `.replace()`               | `text.replace("old", "new")` |
| Разделить строку     | `.split()`                 | `text.split(",")`            |
| Объединить список    | `.join()`                  | `",".join(list)`             |
| Убрать пробелы       | `.strip()`                 | `text.strip()`               |
| В верхний регистр    | `.upper()`                 | `text.upper()`               |
| В нижний регистр     | `.lower()`                 | `text.lower()`               |
| Проверить начало     | `.startswith()`            | `text.startswith("abc")`     |
| Проверить конец      | `.endswith()`              | `text.endswith("xyz")`       |
| Количество вхождений | `.count()`                 | `text.count("abc")`          |
| Проверить содержимое | `.isdigit()`, `.isalpha()` | `text.isdigit()`             |

## 📝 Создание строк

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
path = r"C:\new\folder\test"    # → без raw было бы C:
ew\folder	est
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

## 🔍 Поиск и проверка

### Поиск подстроки

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

### Проверка содержимого

```python
# Цифры
"123".isdigit()                 # → True
"12.3".isdigit()                # → False (точка не цифра)
"abc".isdigit()                 # → False

# Буквы
"Hello".isalpha()               # → True
"Hello123".isalpha()            # → False (есть цифры)
"Hello World".isalpha()         # → False (есть пробел)

# Буквы и цифры
"Hello123".isalnum()            # → True
"Hello 123".isalnum()           # → False (есть пробел)

# Регистр
"hello".islower()               # → True
"Hello".islower()               # → False
"HELLO".isupper()               # → True
"Hello".isupper()               # → False

# Пробельные символы
"   ".isspace()                 # → True
"  \t\n  ".isspace()            # → True
"  a  ".isspace()               # → False

# Заголовок (каждое слово с большой буквы)
"Hello World".istitle()         # → True
"Hello world".istitle()         # → False

# Только ASCII символы
"Hello".isascii()               # → True
"Привет".isascii()              # → False

# Десятичные числа (включая дроби)
"123".isdecimal()               # → True
"½".isdecimal()                 # → False

# Числовые символы
"123".isnumeric()               # → True
"½".isnumeric()                 # → True
"Ⅳ".isnumeric()                 # → True (римские цифры)
```

## ✂️ Разделение и объединение

### Разделение строк

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
```

### Объединение строк

```python
words = ["apple", "banana", "cherry"]

# Объединить с разделителем
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

## 🔄 Замена и удаление

### Замена текста

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
```

### Удаление символов с краёв

```python
text = "  Hello World  "

# Удалить пробелы
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

## 📐 Изменение регистра

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

## 📏 Выравнивание и заполнение

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

## 📊 Доступ к символам и срезы

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

# Отрицательные индексы
text[-5:]                       # → "World" (последние 5)
text[:-6]                       # → "Hello" (без последних 6)

# Длина строки
len(text)                       # → 11
```

## 🎨 Форматирование строк

### f-строки (Python 3.6+) - Рекомендуется!

```python
name = "John"
age = 25
price = 19.99

# Базовое форматирование
f"Меня зовут {name}, мне {age} лет"
# → "Меня зовут John, мне 25 лет"

# Выражения в f-строках
f"Через 5 лет мне будет {age + 5} лет"
f"Цена с НДС: {price * 1.2:.2f}₽"

# Форматирование чисел
f"{price:.2f}"                  # → "19.99" (2 знака)
f"{price:.0f}"                  # → "20" (округление)
f"{1234567:,}"                  # → "1,234,567" (разделитель)
f"{42:05d}"                     # → "00042" (дополнить нулями)

# Выравнивание
f"{name:>10}"                   # → "      John" (вправо)
f"{name:<10}"                   # → "John      " (влево)
f"{name:^10}"                   # → "   John   " (центр)
f"{name:*^10}"                  # → "***John***" (с символом)

# Системы счисления
num = 42
f"{num:b}"                      # → "101010" (двоичная)
f"{num:o}"                      # → "52" (восьмеричная)
f"{num:x}"                      # → "2a" (шестнадцатеричная)
f"{num:X}"                      # → "2A" (заглавные)

# Процент
ratio = 0.756
f"{ratio:.1%}"                  # → "75.6%"

# Отладка (Python 3.8+)
x = 10
f"{x=}"                         # → "x=10"
f"{x + 5=}"                     # → "x + 5=15"
```

### Метод format()

```python
name = "John"
age = 25

# Позиционные аргументы
"Меня зовут {}, мне {} лет".format(name, age)

# Именованные аргументы
"Меня зовут {name}, мне {age} лет".format(name=name, age=age)

# Индексы
"{0} + {1} = {2}".format(5, 3, 8)
"{1} - {0} = {2}".format(5, 3, -2)

# Форматирование
"{:.2f}".format(3.14159)        # → "3.14"
"{:>10}".format("text")         # → "      text"
```

### Старый стиль (%)

```python
name = "John"
age = 25

"Меня зовут %s, мне %d лет" % (name, age)
"Число: %.2f" % 3.14159         # → "Число: 3.14"
"Число: %05d" % 42              # → "Число: 00042"
```

### Шаблоны (Template)

```python
from string import Template

tmpl = Template("Привет, $name! Тебе $age лет.")
tmpl.substitute(name="John", age=25)
# → "Привет, John! Тебе 25 лет."
```

## 🎯 Полезные примеры

### Проверка email

```python
email = "user@example.com"
if "@" in email and "." in email:
    username, domain = email.split("@")
    print(f"Пользователь: {username}")
    print(f"Домен: {domain}")
```

### Очистка и нормализация

```python
user_input = "  HELLO world!  "
cleaned = user_input.strip().lower()  # → "hello world!"

# Удалить все пробелы
no_spaces = user_input.replace(" ", "")  # → "HELLOworld!"
```

### Создание slug из заголовка

```python
title = "Мой Новый Пост!"
slug = title.lower().replace(" ", "-").strip("!")
# → "мой-новый-пост"

# Более продвинутый вариант
import re
slug = re.sub(r'[^\w\s-]', '', title.lower())
slug = re.sub(r'[-\s]+', '-', slug)
```

### Маскирование чувствительных данных

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

### Проверка пароля

```python
password = "MyPass123!"

has_upper = any(c.isupper() for c in password)
has_lower = any(c.islower() for c in password)
has_digit = any(c.isdigit() for c in password)
has_special = any(not c.isalnum() for c in password)
is_long = len(password) >= 8

is_strong = all([has_upper, has_lower, has_digit, has_special, is_long])
```

### Извлечение чисел из строки

```python
text = "Заказ №123: 5 яблок по 50₽"

# Все числа
import re
numbers = re.findall(r'\d+', text)  # → ['123', '5', '50']

# Или вручную
numbers = [int(s) for s in text.split() if s.isdigit()]
```

### Подсчёт слов и символов

```python
text = "Hello World! How are you?"

# Количество слов
word_count = len(text.split())  # → 5

# Количество символов (без пробелов)
char_count = len(text.replace(" ", ""))  # → 20

# Количество предложений
sentence_count = text.count(".") + text.count("!") + text.count("?")
```

### Палиндром

```python
def is_palindrome(text):
    cleaned = text.lower().replace(" ", "")
    return cleaned == cleaned[::-1]

is_palindrome("А роза упала на лапу Азора")  # → True
is_palindrome("Hello")  # → False
```

### Цензура слов

```python
def censor_words(text, bad_words):
    for word in bad_words:
        text = text.replace(word, "*" * len(word))
    return text

text = "This is bad and ugly"
censored = censor_words(text, ["bad", "ugly"])
# → "This is *** and ****"
```

## 🔧 Кодирование

```python
text = "Привет, мир!"

# Кодирование в байты
encoded = text.encode('utf-8')      # → b'\xd0\x9f\xd1\x80...'
encoded = text.encode('cp1251')     # → разные байты

# Декодирование из байтов
decoded = encoded.decode('utf-8')   # → "Привет, мир!"

# Обработка ошибок
bad_bytes = b'\xff\xfe'
bad_bytes.decode('utf-8', errors='ignore')      # → пропустить
bad_bytes.decode('utf-8', errors='replace')     # → заменить на �

# ASCII представление
ascii(text)                         # → "'\\u041f\\u0440\\u0438..."
repr(text)                          # → "'Привет, мир!'"
```

## ⚡ Цепочки методов

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

## 📋 Escape-последовательности

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