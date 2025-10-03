
## 💡 Полезные советы

- **f-строки** - самый современный и быстрый способ форматирования (Python 3.6+)
- **Синтаксис**: `f"текст {переменная} текст"` или `f'текст {переменная} текст'`
- **Выражения**: можно использовать любые выражения Python внутри `{}`
- **Форматирование**: `{value:format_spec}` для управления форматом
- **Многострочные**: используйте тройные кавычки `f""" ... """`
- **Отладка**: `f"{x=}"` выведет `x=значение` (Python 3.8+)
- **Вложенность**: можно вкладывать f-строки друг в друга
- **Скорость**: f-строки быстрее чем `.format()` и `%`

## 🚀 Быстрая справка

|Задача|Синтаксис|Пример|
|---|---|---|
|Базовая подстановка|`f"{var}"`|`f"Hello {name}"`|
|Выражения|`f"{expr}"`|`f"{x + y}"`|
|Вызов функций|`f"{func()}"`|`f"{len(text)}"`|
|Округление float|`f"{x:.2f}"`|`f"{3.14159:.2f}"` → `"3.14"`|
|Разделители тысяч|`f"{n:,}"`|`f"{1000000:,}"` → `"1,000,000"`|
|Процент|`f"{x:.1%}"`|`f"{0.756:.1%}"` → `"75.6%"`|
|Дополнение нулями|`f"{n:05d}"`|`f"{42:05d}"` → `"00042"`|
|Выравнивание влево|`f"{s:<10}"`|`f"{'Hi':<10}"` → `"Hi "`|
|Выравнивание вправо|`f"{s:>10}"`|`f"{'Hi':>10}"` → `" Hi"`|
|Выравнивание по центру|`f"{s:^10}"`|`f"{'Hi':^10}"` → `" Hi "`|
|Знак числа|`f"{n:+}"`|`f"{42:+}"` → `"+42"`|
|Двоичная система|`f"{n:b}"`|`f"{10:b}"` → `"1010"`|
|Hex система|`f"{n:x}"`|`f"{255:x}"` → `"ff"`|
|Отладка (3.8+)|`f"{x=}"`|`f"{x=}"` → `"x=10"`|

## 📝 Базовое использование

```python
# Простая подстановка
name = "John"
age = 25
f"Hello, {name}!"               # → "Hello, John!"
f"I am {age} years old"         # → "I am 25 years old"

# Несколько переменных
f"{name} is {age} years old"    # → "John is 25 years old"

# Выражения
x = 10
y = 5
f"{x} + {y} = {x + y}"          # → "10 + 5 = 15"
f"{x} * {y} = {x * y}"          # → "10 * 5 = 50"

# Вызов методов
text = "hello"
f"{text.upper()}"               # → "HELLO"
f"{text.capitalize()}"          # → "Hello"

# Вызов функций
numbers = [1, 2, 3, 4, 5]
f"Length: {len(numbers)}"       # → "Length: 5"
f"Sum: {sum(numbers)}"          # → "Sum: 15"

# Доступ к атрибутам
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person = Person("Alice", 30)
f"{person.name} is {person.age}"  # → "Alice is 30"

# Индексация и срезы
items = ["apple", "banana", "cherry"]
f"First: {items[0]}"            # → "First: apple"
f"Last: {items[-1]}"            # → "Last: cherry"
f"First two: {items[:2]}"       # → "First two: ['apple', 'banana']"

# Словари
user = {"name": "Bob", "age": 28}
f"{user['name']} is {user['age']}"  # → "Bob is 28"
```

## 🔢 Форматирование чисел

### Округление float

```python
pi = 3.14159265359

# Количество знаков после запятой
f"{pi:.2f}"                     # → "3.14"
f"{pi:.4f}"                     # → "3.1416"
f"{pi:.0f}"                     # → "3"

# Научная нотация
large = 1234567.89
f"{large:.2e}"                  # → "1.23e+06"
f"{large:.3E}"                  # → "1.235E+06" (заглавная E)

# Общий формат (выбирает между fixed и scientific)
f"{pi:.3g}"                     # → "3.14"
f"{large:.3g}"                  # → "1.23e+06"
```

### Разделители тысяч

```python
big_number = 1234567890

# Запятая как разделитель
f"{big_number:,}"               # → "1,234,567,890"

# Подчеркивание как разделитель
f"{big_number:_}"               # → "1_234_567_890"

# С округлением
price = 1234567.89
f"{price:,.2f}"                 # → "1,234,567.89"
f"{price:_.2f}"                 # → "1_234_567.89"
```

### Проценты

```python
ratio = 0.7562

# Процент
f"{ratio:%}"                    # → "75.620000%"
f"{ratio:.1%}"                  # → "75.6%"
f"{ratio:.2%}"                  # → "75.62%"

# Примеры
success_rate = 0.856
f"Success rate: {success_rate:.1%}"  # → "Success rate: 85.6%"
```

### Знак числа

```python
positive = 42
negative = -42
zero = 0

# Всегда показывать знак
f"{positive:+}"                 # → "+42"
f"{negative:+}"                 # → "-42"
f"{zero:+}"                     # → "+0"

# Пробел для положительных
f"{positive: }"                 # → " 42"
f"{negative: }"                 # → "-42"

# По умолчанию (знак только для отрицательных)
f"{positive}"                   # → "42"
f"{negative}"                   # → "-42"
```

## 📏 Выравнивание и заполнение

### Выравнивание текста

```python
text = "Hello"

# Влево (по умолчанию для строк)
f"{text:<10}"                   # → "Hello     "
f"{text:<15}"                   # → "Hello          "

# Вправо (по умолчанию для чисел)
f"{text:>10}"                   # → "     Hello"
f"{text:>15}"                   # → "          Hello"

# По центру
f"{text:^10}"                   # → "  Hello   "
f"{text:^15}"                   # → "     Hello     "
```

### Заполнитель

```python
text = "Hi"
number = 42

# С символом-заполнителем
f"{text:*<10}"                  # → "Hi********"
f"{text:*>10}"                  # → "********Hi"
f"{text:*^10}"                  # → "****Hi****"

# Дополнение нулями для чисел
f"{number:05}"                  # → "00042"
f"{number:010}"                 # → "0000000042"

# Альтернатива через 0
f"{number:05d}"                 # → "00042"

# С разделителями
big = 1234
f"{big:0>10,}"                  # → "00001,234"
```

### Ширина из переменной

```python
text = "Hello"
width = 10

# Динамическая ширина
f"{text:<{width}}"              # → "Hello     "
f"{text:^{width}}"              # → "  Hello   "

# Динамическая точность
pi = 3.14159
precision = 3
f"{pi:.{precision}f}"           # → "3.142"
```

## 🔢 Системы счисления

```python
num = 42

# Двоичная система
f"{num:b}"                      # → "101010"
f"{num:#b}"                     # → "0b101010" (с префиксом)
f"{num:08b}"                    # → "00101010" (дополнить нулями)

# Восьмеричная система
f"{num:o}"                      # → "52"
f"{num:#o}"                     # → "0o52"

# Шестнадцатеричная система
f"{num:x}"                      # → "2a" (строчные)
f"{num:X}"                      # → "2A" (заглавные)
f"{num:#x}"                     # → "0x2a" (с префиксом)
f"{num:#X}"                     # → "0X2A"
f"{num:04x}"                    # → "002a" (дополнить нулями)

# Примеры
color = 0xFF5733
f"Color: #{color:06X}"          # → "Color: #FF5733"
```

## 🎯 Практические примеры

### Таблицы

```python
# Заголовок и данные таблицы
products = [
    ("Apple", 150, 2.50),
    ("Banana", 200, 1.80),
    ("Cherry", 75, 5.20)
]

# Красивый вывод таблицы
print(f"{'Product':<10} {'Qty':>5} {'Price':>8}")
print("-" * 25)
for name, qty, price in products:
    print(f"{name:<10} {qty:>5} {price:>8.2f}")

# Вывод:
# Product       Qty    Price
# -------------------------
# Apple         150     2.50
# Banana        200     1.80
# Cherry         75     5.20
```

### Прогресс бар

```python
def progress_bar(current, total, width=50):
    percent = current / total
    filled = int(width * percent)
    bar = "█" * filled + "░" * (width - filled)
    return f"{bar} {percent:.1%} ({current}/{total})"

print(progress_bar(75, 100))
# → "█████████████████████████████████████░░░░░░░░░░░░░░░ 75.0% (75/100)"
```

### Форматирование времени

```python
from datetime import datetime

now = datetime.now()

# Разные форматы
f"{now:%Y-%m-%d}"               # → "2024-10-03"
f"{now:%d.%m.%Y}"               # → "03.10.2024"
f"{now:%H:%M:%S}"               # → "14:30:45"
f"{now:%d %B %Y}"               # → "03 October 2024"

# Полный формат
f"Current time: {now:%Y-%m-%d %H:%M:%S}"
```

### Форматирование денег

```python
def format_money(amount, currency="₽"):
    return f"{amount:,.2f} {currency}"

format_money(1234567.89)        # → "1,234,567.89 ₽"
format_money(999.5, "$")        # → "999.50 $"

# В таблице цен
prices = {"laptop": 45999, "mouse": 899, "keyboard": 2499}
for item, price in prices.items():
    print(f"{item.capitalize():<10} {price:>10,.2f} ₽")
```

### Выравнивание чисел в столбцах

```python
numbers = [1, 12, 123, 1234, 12345]

# Правое выравнивание
for num in numbers:
    print(f"{num:>5}")

# Вывод:
#     1
#    12
#   123
#  1234
# 12345
```

## 🐛 Отладка (Python 3.8+)

```python
# Оператор = для отладки
x = 10
y = 20
f"{x=}"                         # → "x=10"
f"{y=}"                         # → "y=20"
f"{x + y=}"                     # → "x + y=30"

# С вычислениями
numbers = [1, 2, 3, 4, 5]
f"{sum(numbers)=}"              # → "sum(numbers)=15"
f"{len(numbers)=}"              # → "len(numbers)=5"

# С форматированием
pi = 3.14159
f"{pi=:.2f}"                    # → "pi=3.14"

# Множественные переменные
name = "Alice"
age = 30
print(f"{name=}, {age=}")       # → "name='Alice', age=30"

# В функциях (отладка)
def calculate(a, b):
    result = a * b
    print(f"{a=}, {b=}, {result=}")
    return result

calculate(5, 7)                 # → a=5, b=7, result=35
```

## 📋 Многострочные f-строки

```python
name = "John"
age = 25
city = "Moscow"

# Многострочная f-строка
message = f"""
Hello, {name}!
You are {age} years old.
You live in {city}.
"""

# С выражениями
numbers = [1, 2, 3, 4, 5]
stats = f"""
Numbers: {numbers}
Count: {len(numbers)}
Sum: {sum(numbers)}
Average: {sum(numbers) / len(numbers):.2f}
"""

# HTML генерация
html = f"""
<html>
<head><title>{name}'s Page</title></head>
<body>
    <h1>Welcome, {name}!</h1>
    <p>Age: {age}</p>
</body>
</html>
"""
```

## 🔄 Условные выражения

```python
# Тернарный оператор внутри f-строк
age = 18
f"You are {'adult' if age >= 18 else 'minor'}"  # → "You are adult"

score = 85
f"Grade: {'Pass' if score >= 60 else 'Fail'}"  # → "Grade: Pass"

# С множественными условиями
temperature = 25
f"Weather: {'Hot' if temperature > 30 else 'Warm' if temperature > 20 else 'Cold'}"

# Форматирование в зависимости от условия
balance = -100
f"Balance: {abs(balance):.2f} ({('debt' if balance < 0 else 'credit')})"
```

## 🎨 Специальные случаи

### Escape-последовательности

```python
# Фигурные скобки
f"{{Hello}}"                    # → "{Hello}"
f"Set: {{{1, 2, 3}}}"          # → "Set: {1, 2, 3}"

# Двойные фигурные скобки для экранирования
value = 42
f"Value is {{{value}}}"         # → "Value is {42}"

# Кавычки
name = "John"
f'He said "Hello, {name}!"'     # → 'He said "Hello, John!"'
f"It's {name}'s book"           # → "It's John's book"
```

### Словари и сложные структуры

```python
# Словари
user = {"name": "Alice", "age": 30, "city": "Moscow"}
f"{user['name']} from {user['city']}"  # → "Alice from Moscow"

# Вложенные структуры
data = {"users": [{"name": "Alice"}, {"name": "Bob"}]}
f"First user: {data['users'][0]['name']}"  # → "First user: Alice"

# С методами словаря
f"Keys: {list(user.keys())}"    # → "Keys: ['name', 'age', 'city']"
```

### Вложенные f-строки

```python
# f-строка внутри f-строки
width = 10
value = "Hi"
f"{value:{'^' if True else '<'}{width}}"  # → "    Hi    "

# Динамический формат
decimals = 2
price = 123.456
f"{price:.{decimals}f}"         # → "123.46"

# Сложный пример
align = "<"
fill = "*"
width = 15
text = "Hello"
f"{text:{fill}{align}{width}}"  # → "Hello**********"
```

## 🔍 Сравнение со старыми методами

```python
name = "John"
age = 25

# f-строка (современный способ)
f"Hello, {name}! Age: {age}"

# str.format() (старый способ)
"Hello, {}! Age: {}".format(name, age)
"Hello, {0}! Age: {1}".format(name, age)
"Hello, {name}! Age: {age}".format(name=name, age=age)

# % форматирование (очень старый)
"Hello, %s! Age: %d" % (name, age)

# Конкатенация (плохой способ)
"Hello, " + name + "! Age: " + str(age)

# f-строки быстрее и читабельнее!
```

## ⚠️ Важные замечания

```python
# ⚠️ Нельзя использовать \ внутри {}
# f"{'\n'}"                     # ❌ SyntaxError!

# ✅ Используйте переменную
newline = '\n'
f"{newline}"                    # ✅

# ⚠️ Нельзя использовать комментарии внутри {}
# f"{x + y  # comment}"         # ❌ SyntaxError!

# ⚠️ Пустые {} не работают
# f"{}"                         # ❌ SyntaxError!

# ⚠️ f-строки вычисляются сразу
x = 10
s = f"{x}"                      # s = "10"
x = 20
print(s)                        # → "10" (не "20"!)

# ⚠️ Будьте осторожны с пользовательским вводом
# Не используйте eval() с f-строками от пользователей!
```

## 💡 Советы по производительности

```python
# ✅ f-строки быстрее остальных методов
# Benchmark: f-строки > str.format() > % > конкатенация

# ✅ Для множественного форматирования используйте f-строки
names = ["Alice", "Bob", "Charlie"]
# Быстро
result = [f"Hello, {name}" for name in names]

# ✅ Для статичных строк без переменных НЕ нужна f-строка
# Медленнее
f"Hello, World!"
# Быстрее
"Hello, World!"

# ✅ Кэшируйте сложные выражения
# Медленнее
for i in range(1000):
    print(f"{expensive_function()}")

# Быстрее
value = expensive_function()
for i in range(1000):
    print(f"{value}")
```

## 🎯 Когда использовать f-строки

**Используйте f-строки когда:**

- Нужно форматировать строки с переменными
- Важна читаемость кода
- Требуется высокая производительность
- Python 3.6+ доступен
- Нужна отладка (f"{x=}")
- Вставляете выражения в строки

**НЕ используйте f-строки когда:**

- Python версии < 3.6
- Строка полностью статична (без переменных)
- Нужно много раз использовать один шаблон (используйте Template)
- Работаете с пользовательским вводом без валидации
- Строка формируется из внешнего источника (SQL, конфиг)