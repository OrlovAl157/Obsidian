---
tags:
  - python
  - тема/модули
  - тема/строки
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
сторонний_пакет: false
---

## 📥 Импорт модуля

```python
# Стандартный импорт
import string
string.ascii_letters            # → использование через string.

# Импорт конкретных констант/классов
from string import ascii_letters, digits, Template
ascii_letters                   # → без префикса string.

# Импорт всех констант (не рекомендуется)
from string import *
ascii_letters                   # → может привести к конфликтам имён
```

## 💡 Полезные советы

- **Константы символов**: готовые наборы букв, цифр, знаков препинания
- **Template**: безопасная подстановка переменных в строки
- **Formatter**: продвинутое форматирование строк (редко используется напрямую)
- **ascii_letters**: только ASCII буквы (a-z, A-Z), без кириллицы
- **punctuation**: стандартные знаки препинания
- **Капитализация**: методы capwords() для заголовков
- **Используйте для валидации**: проверка, что строка содержит только определённые символы
- **Генерация паролей**: комбинируйте константы для создания случайных строк

## 🚀 Быстрая справка

| Константа         | Описание                | Значение                                            |
| ----------------- | ----------------------- | --------------------------------------------------- |
| `ascii_lowercase` | Строчные ASCII буквы    | `'abcdefghijklmnopqrstuvwxyz'`                      |
| `ascii_uppercase` | Заглавные ASCII буквы   | `'ABCDEFGHIJKLMNOPQRSTUVWXYZ'`                      |
| `ascii_letters`   | Все ASCII буквы         | `ascii_lowercase + ascii_uppercase`                 |
| `digits`          | Цифры                   | `'0123456789'`                                      |
| `hexdigits`       | Шестнадцатеричные цифры | `'0123456789abcdefABCDEF'`                          |
| `octdigits`       | Восьмеричные цифры      | `'01234567'`                                        |
| `punctuation`     | Знаки препинания        | `'!"#$%&\'()*+,-./:;<=>?@[\\]^_\`{\|}~'`            |
| `whitespace`      | Пробельные символы      | `' \\t\\n\\r\\x0b\\x0c'`                            |
| `printable`       | Печатные символы        | `digits + ascii_letters + punctuation + whitespace` |

## 📚 Константы - наборы символов

### Буквы

```python
import string

# Строчные буквы (a-z)
string.ascii_lowercase
# → 'abcdefghijklmnopqrstuvwxyz'

# Заглавные буквы (A-Z)
string.ascii_uppercase
# → 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

# Все буквы (a-z + A-Z)
string.ascii_letters
# → 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'

# Примеры использования
# Проверить, что строка содержит только буквы
text = "Hello"
all(c in string.ascii_letters for c in text)  # → True

# Получить случайную букву
import random
random.choice(string.ascii_letters)  # → 'M'
```

### Цифры

```python
import string

# Десятичные цифры (0-9)
string.digits
# → '0123456789'

# Шестнадцатеричные цифры (0-9, a-f, A-F)
string.hexdigits
# → '0123456789abcdefABCDEF'

# Восьмеричные цифры (0-7)
string.octdigits
# → '01234567'

# Примеры использования
# Проверить, что строка - число
text = "12345"
all(c in string.digits for c in text)  # → True

# Проверить, что строка - hex число
hex_text = "A3F"
all(c in string.hexdigits for c in hex_text)  # → True

# Удалить все нецифровые символы
text = "Phone: +7 (123) 456-78-90"
''.join(c for c in text if c in string.digits)  # → "712345678900"
```

### Знаки препинания

```python
import string

# Все знаки препинания
string.punctuation
# → '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'

# Примеры использования
# Удалить знаки препинания
text = "Hello, World!"
''.join(c for c in text if c not in string.punctuation)
# → "Hello World"

# Подсчитать знаки препинания
text = "Hello! How are you? I'm fine."
sum(1 for c in text if c in string.punctuation)  # → 4

# Заменить знаки препинания на пробел
import re
re.sub(f"[{re.escape(string.punctuation)}]", " ", text)
```

### Пробельные символы

```python
import string

# Все пробельные символы
string.whitespace
# → ' \t\n\r\x0b\x0c'
# Включает: пробел, табуляция, перевод строки, возврат каретки, и др.

# Примеры использования
# Удалить все пробельные символы
text = "Hello\n\tWorld  "
''.join(c for c in text if c not in string.whitespace)
# → "HelloWorld"

# Заменить любые пробелы на обычный пробел
' '.join(text.split())  # → "Hello World"
```

### Печатные символы

```python
import string

# Все печатные символы
string.printable
# → digits + ascii_letters + punctuation + whitespace

# Примеры использования
# Удалить непечатные символы
text = "Hello\x00World\x01"
''.join(c for c in text if c in string.printable)
# → "HelloWorld"

# Проверить на печатные символы
text = "Hello World"
all(c in string.printable for c in text)  # → True
```

## 🎯 Практические примеры с константами

### Генерация случайных строк

```python
import string
import random

# Случайная строка из букв
def random_string(length=10):
    return ''.join(random.choice(string.ascii_letters) for _ in range(length))

random_string()  # → "xKmPqWjHzN"

# Случайная строка из букв и цифр
def random_alphanumeric(length=10):
    chars = string.ascii_letters + string.digits
    return ''.join(random.choice(chars) for _ in range(length))

random_alphanumeric()  # → "aB3xK9mP2q"

# Генерация пароля
def generate_password(length=12):
    chars = string.ascii_letters + string.digits + string.punctuation
    return ''.join(random.choice(chars) for _ in range(length))

generate_password()  # → "Kj#9mP$2qW!z"

# Генерация PIN-кода
def generate_pin(length=4):
    return ''.join(random.choice(string.digits) for _ in range(length))

generate_pin()  # → "7392"
```

### Валидация данных

```python
import string

# Проверить, что строка - только буквы
def is_alpha(text):
    return all(c in string.ascii_letters for c in text)

is_alpha("Hello")  # → True
is_alpha("Hello123")  # → False

# Проверить, что строка - только цифры
def is_numeric(text):
    return all(c in string.digits for c in text)

is_numeric("12345")  # → True
is_numeric("123.45")  # → False

# Проверить, что строка - буквы и цифры
def is_alphanumeric(text):
    allowed = string.ascii_letters + string.digits
    return all(c in allowed for c in text)

is_alphanumeric("Hello123")  # → True

# Проверить корректность username
def is_valid_username(username):
    allowed = string.ascii_letters + string.digits + "_-"
    return (len(username) >= 3 and
            all(c in allowed for c in username) and
            username[0] in string.ascii_letters)

is_valid_username("user_123")  # → True
is_valid_username("123user")  # → False
```

### Очистка текста

```python
import string

# Удалить все знаки препинания
def remove_punctuation(text):
    return ''.join(c for c in text if c not in string.punctuation)

remove_punctuation("Hello, World!")  # → "Hello World"

# Удалить всё кроме букв и цифр
def keep_alphanumeric(text):
    allowed = string.ascii_letters + string.digits + ' '
    return ''.join(c for c in text if c in allowed)

keep_alphanumeric("Hello, World! 123")  # → "Hello World 123"

# Нормализовать пробелы
def normalize_spaces(text):
    return ' '.join(text.split())

normalize_spaces("Hello    World\n\t123")  # → "Hello World 123"
```

### Анализ текста

```python
import string

def analyze_text(text):
    stats = {
        'letters': sum(1 for c in text if c in string.ascii_letters),
        'uppercase': sum(1 for c in text if c in string.ascii_uppercase),
        'lowercase': sum(1 for c in text if c in string.ascii_lowercase),
        'digits': sum(1 for c in text if c in string.digits),
        'punctuation': sum(1 for c in text if c in string.punctuation),
        'whitespace': sum(1 for c in text if c in string.whitespace),
        'total': len(text)
    }
    return stats

analyze_text("Hello World! 123")
# → {'letters': 10, 'uppercase': 2, 'lowercase': 8, 
#    'digits': 3, 'punctuation': 1, 'whitespace': 2, 'total': 16}
```

## 📝 Template - безопасное форматирование

### Основы Template

```python
from string import Template

# Создание шаблона
template = Template("Hello, $name! You are $age years old.")

# Подстановка значений
template.substitute(name="John", age=25)
# → "Hello, John! You are 25 years old."

# Через словарь
data = {"name": "Alice", "age": 30}
template.substitute(data)
# → "Hello, Alice! You are 30 years old."

# Через **kwargs
template.substitute(**data)
# → то же самое
```

### Безопасная подстановка

```python
from string import Template

template = Template("Hello, $name! Your score is $score.")

# substitute() вызовет ошибку при отсутствии ключа
try:
    template.substitute(name="John")  # нет 'score'
except KeyError as e:
    print(f"Ошибка: {e}")  # → Ошибка: 'score'

# safe_substitute() заменит только известные ключи
template.safe_substitute(name="John")
# → "Hello, John! Your score is $score."

# Оставшиеся $переменные не заменяются
template.safe_substitute(name="John", extra="value")
# → "Hello, John! Your score is $score."
```

### Расширенный синтаксис Template

```python
from string import Template

# С фигурными скобками (для разделения от текста)
template = Template("${noun}ification")
template.substitute(noun="test")  # → "testification"

# Двойной $ для экранирования
template = Template("Price: $$100")
template.substitute()  # → "Price: $100"

# Комбинирование
template = Template("${item} costs $$${price}")
template.substitute(item="Book", price=20)
# → "Book costs $20"
```

### Кастомизация Template

```python
from string import Template

# Изменить разделитель (вместо $)
class PercentTemplate(Template):
    delimiter = '%'

template = PercentTemplate("Hello, %name!")
template.substitute(name="World")  # → "Hello, World!"

# Использовать другой паттерн
class BraceTemplate(Template):
    delimiter = '{'
    pattern = r'\{(?P<braced>[^}]*)\}'

template = BraceTemplate("Hello, {name}!")
template.substitute(name="World")  # → "Hello, World!"
```

### Практические примеры с Template

```python
from string import Template

# Email шаблон
email_template = Template("""
Dear $name,

Your order #$order_id has been confirmed.
Total amount: $$${amount}

Thank you for shopping with us!

Best regards,
$company
""")

email = email_template.substitute(
    name="John Doe",
    order_id="12345",
    amount="99.99",
    company="MyShop"
)

# SQL запрос (безопаснее чем format)
sql_template = Template("SELECT * FROM $table WHERE id = $id")
query = sql_template.safe_substitute(table="users")
# → "SELECT * FROM users WHERE id = $id"

# HTML шаблон
html_template = Template("""
<html>
<head><title>$title</title></head>
<body>
    <h1>$heading</h1>
    <p>$content</p>
</body>
</html>
""")

html = html_template.substitute(
    title="My Page",
    heading="Welcome",
    content="Hello World"
)

# Конфигурационный файл
config_template = Template("""
[database]
host = $db_host
port = $db_port
name = $db_name

[server]
host = $server_host
port = $server_port
""")

config = config_template.substitute(
    db_host="localhost",
    db_port="5432",
    db_name="mydb",
    server_host="0.0.0.0",
    server_port="8000"
)
```

## 🔧 Функция capwords()

```python
import string

# Capitalize первую букву каждого слова
text = "hello world from python"
string.capwords(text)
# → "Hello World From Python"

# С разделителем
text = "hello-world-from-python"
string.capwords(text, sep="-")
# → "Hello-World-From-Python"

# Отличие от str.title()
text = "they're bill's friends"
text.title()  # → "They'Re Bill'S Friends" (не идеально)
string.capwords(text)  # → "They're Bill's Friends" (лучше)

# Примеры
string.capwords("new york city")  # → "New York City"
string.capwords("python 3.10")  # → "Python 3.10"
string.capwords("hello_world", sep="_")  # → "Hello_World"
```

## 🎨 Formatter (продвинутое форматирование)

```python
import string

# Создание форматтера
formatter = string.Formatter()

# Форматирование
formatter.format("Hello, {name}! Age: {age}", name="John", age=25)
# → "Hello, John! Age: 25"

# Получить поля из строки
formatter.parse("Hello, {name}! Age: {age}")
# → [('Hello, ', 'name', '', None), ('! Age: ', 'age', '', None)]

# Получить имена полей
list(formatter.parse("Hello, {name}! Age: {age}"))

# Vformat (с словарём)
formatter.vformat("Hello, {name}!", {"name": "World"}, {})
# → "Hello, World!"
```

## 🎯 Полезные комбинации

### Очистка и нормализация

```python
import string

def clean_text(text):
    """Удалить знаки препинания и лишние пробелы"""
    # Удалить знаки препинания
    text = ''.join(c if c not in string.punctuation else ' ' for c in text)
    # Нормализовать пробелы
    return ' '.join(text.split())

clean_text("Hello,  World! How are you?")
# → "Hello World How are you"

def slugify(text):
    """Создать URL-friendly строку"""
    text = text.lower()
    text = ''.join(c if c in string.ascii_lowercase + string.digits else '-' 
                   for c in text)
    # Убрать повторяющиеся дефисы
    import re
    text = re.sub(r'-+', '-', text)
    return text.strip('-')

slugify("Hello World! 2024")  # → "hello-world-2024"
```

### Маскирование данных

```python
import string

def mask_email(email):
    """Замаскировать часть email"""
    if '@' not in email:
        return email
    
    local, domain = email.split('@')
    if len(local) <= 2:
        masked_local = local[0] + '*'
    else:
        masked_local = local[0] + '*' * (len(local) - 2) + local[-1]
    
    return f"{masked_local}@{domain}"

mask_email("john.doe@example.com")  # → "j******e@example.com"

def mask_phone(phone):
    """Замаскировать часть телефона"""
    digits = ''.join(c for c in phone if c in string.digits)
    if len(digits) < 4:
        return phone
    
    return digits[:2] + '*' * (len(digits) - 4) + digits[-2:]

mask_phone("+7 (123) 456-78-90")  # → "71******90"
```

### Генерация кодов

```python
import string
import random

def generate_code(length=8, use_upper=True, use_lower=True, 
                  use_digits=True, use_special=False):
    """Генератор кодов с настройками"""
    chars = ''
    if use_upper:
        chars += string.ascii_uppercase
    if use_lower:
        chars += string.ascii_lowercase
    if use_digits:
        chars += string.digits
    if use_special:
        chars += string.punctuation
    
    if not chars:
        raise ValueError("Нужно выбрать хотя бы один тип символов")
    
    return ''.join(random.choice(chars) for _ in range(length))

generate_code(6, use_lower=False, use_special=False)
# → "A5K9M2" (только заглавные и цифры)

def generate_license_key():
    """Генератор лицензионного ключа"""
    segments = []
    for _ in range(4):
        segment = ''.join(random.choice(string.ascii_uppercase + string.digits)
                         for _ in range(4))
        segments.append(segment)
    return '-'.join(segments)

generate_license_key()  # → "A5K9-M2P7-X8Q3-N4R6"
```

### Проверка сложности пароля

```python
import string

def check_password_strength(password):
    """Проверить сложность пароля"""
    score = 0
    feedback = []
    
    # Длина
    if len(password) >= 8:
        score += 1
    else:
        feedback.append("Пароль должен быть минимум 8 символов")
    
    # Заглавные буквы
    if any(c in string.ascii_uppercase for c in password):
        score += 1
    else:
        feedback.append("Добавьте заглавные буквы")
    
    # Строчные буквы
    if any(c in string.ascii_lowercase for c in password):
        score += 1
    else:
        feedback.append("Добавьте строчные буквы")
    
    # Цифры
    if any(c in string.digits for c in password):
        score += 1
    else:
        feedback.append("Добавьте цифры")
    
    # Специальные символы
    if any(c in string.punctuation for c in password):
        score += 1
    else:
        feedback.append("Добавьте специальные символы")
    
    strength = ["Очень слабый", "Слабый", "Средний", "Хороший", "Отличный"]
    
    return {
        'score': score,
        'max_score': 5,
        'strength': strength[min(score, 4)],
        'feedback': feedback
    }

check_password_strength("Pass123!")
# → {'score': 5, 'max_score': 5, 'strength': 'Отличный', 'feedback': []}
```

## ⚠️ Важные замечания

```python
import string

# ⚠️ ascii_letters НЕ включает не-ASCII символы
"Привет" not in string.ascii_letters  # → True
'ё' not in string.ascii_lowercase  # → True

# ✅ Для Unicode используйте методы строк
'Привет'.isalpha()  # → True

# ⚠️ punctuation может не включать все символы
'«»' not in string.punctuation  # → True (кавычки-ёлочки)
'—' not in string.punctuation  # → True (длинное тире)

# ⚠️ Template не экранирует HTML/SQL
from string import Template
template = Template("<div>$content</div>")
# НЕ безопасно для пользовательского ввода!

# ✅ Используйте специализированные библиотеки
# Для HTML: html.escape()
# Для SQL: параметризованные запросы

# ⚠️ capwords() работает только с пробелами по умолчанию
string.capwords("hello-world")  # → "Hello-world" (не "Hello-World")
string.capwords("hello-world", sep="-")  # → "Hello-World" ✅
```

## 🎯 Когда использовать модуль string

**Используйте string когда:**

- Нужны готовые наборы символов (буквы, цифры, знаки)
- Генерируете случайные строки, пароли, коды
- Валидируете ввод пользователя
- Очищаете текст от определённых символов
- Используете Template для безопасной подстановки
- Работаете с ASCII символами

**НЕ используйте string когда:**

- Нужна работа с Unicode (используйте методы строк)
- Нужно экранирование HTML/SQL (используйте специальные библиотеки)
- Достаточно методов str (upper, lower, split и т.д.)
- Нужно сложное форматирование (используйте f-строки)

## 🔗 Связанные темы

- [[00 — 📖 Основы строк 🔹]]
- [[00 — 📖 Введение в regex]]
- [[01 — 🔧 Основы регулярных выражений]]
