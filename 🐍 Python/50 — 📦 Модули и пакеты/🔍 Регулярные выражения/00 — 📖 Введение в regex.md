---
tags:
  - python
  - тема/модули
  - тема/regex
  - статус/завершён
уровень: средний
стандартная_библиотека: true
сторонний_пакет: false
---

## 📋 Содержание

1. [[#🔤 Специальные символы]]
2. [[#🔢 Квантификаторы (повторения)]]
3. [[#📝 Символьные классы]]
4. [[#⚓ Якоря и границы]]
5. [[#🎯 Группы и захват]]
6. [[#👀 Lookahead и Lookbehind]]
7. [[#🛠️ Методы модуля re]]
8. [[#📦 Методы Pattern объекта]]
9. [[#🎭 Методы Match объекта]]
10. [[#🚩 Флаги]]
11. [[#💡 Практические примеры]]

---

## 🔤 Специальные символы

|Символ|Описание|Пример|Совпадёт|Не совпадёт|
|---|---|---|---|---|
|`.`|Любой символ (кроме `\n`)|`a.c`|`abc`, `a5c`, `a c`|`ac`, `a\nc`|
|`^`|Начало строки|`^hello`|`hello world`|`say hello`|
|`$`|Конец строки|`world$`|`hello world`|`world!`|
|`\|`|ИЛИ (альтернатива)|`cat\|dog`|`cat`, `dog`|`bird`|
|`\`|Экранирование спецсимвола|`\.`|`.`|`a`|
|`[]`|Набор символов|`[abc]`|`a`, `b`, `c`|`d`|
|`[^]`|Отрицание набора|`[^abc]`|`d`, `1`, `!`|`a`, `b`|

---

## 🔢 Квантификаторы (повторения)

### Базовые квантификаторы

|Квантификатор|Описание|Пример|Совпадёт|Не совпадёт|
|---|---|---|---|---|
|`*`|0 или более|`ab*`|`a`, `ab`, `abb`, `abbb`|`b`|
|`+`|1 или более|`ab+`|`ab`, `abb`, `abbb`|`a`|
|`?`|0 или 1|`ab?`|`a`, `ab`|`abb`|
|`{n}`|Ровно n раз|`a{3}`|`aaa`|`aa`, `aaaa`|
|`{n,}`|n или более|`a{2,}`|`aa`, `aaa`, `aaaa`|`a`|
|`{n,m}`|От n до m|`a{2,4}`|`aa`, `aaa`, `aaaa`|`a`, `aaaaa`|

### Жадные vs Ленивые vs Ревнивые

[ 🔍 Объяснение (отличная статья)](https://learn.javascript.ru/regexp-greedy-and-lazy)

| Тип                                                    | Синтаксис                  | Поведение                | Backtracking |
| ------------------------------------------------------ | -------------------------- | ------------------------ | ------------ |
| <center>**Жадный**<br></center><br>                    | `*`, `+`, `?`, `{n,m}`     | Максимальное совпадение  | ✅ Да         |
| <center>**Ленивый**</center><br>                       | `*?`, `+?`, `??`, `{n,m}?` | Минимальное совпадение   | ✅ Да         |
| <center>**Ревнивый** <br>(притяжательным)</center><br> | `*+`, `++`, `?+`, `{n,m}+` | Максимальное, без отката | ❌ Нет        |

**Пример:**

```python
import re

text = '<tag>content</tag>'

# Жадный
re.findall(r'<.*>', text)     # ['<tag>content</tag>'] - весь текст

# Ленивый
re.findall(r'<.*?>', text)    # ['<tag>', '</tag>'] - минимум

# Ревнивый (Python 3.11+)
re.findall(r'<.*+>', text)    # [] - нет отката, не найдёт >
```

---

## 📝 Символьные классы

### Предопределённые классы

|Класс|Описание|Эквивалент|ASCII режим|
|---|---|---|---|
|`\d`|Цифра|Unicode цифры|`[0-9]`|
|`\D`|Не цифра|Не Unicode цифры|`[^0-9]`|
|`\w`|Буква/цифра/_|`[a-zA-Z0-9_]` + Unicode|`[a-zA-Z0-9_]`|
|`\W`|Не буква/цифра/_|Противоположность `\w`|`[^a-zA-Z0-9_]`|
|`\s`|Пробельный символ|`[ \t\n\r\f\v]` + Unicode|`[ \t\n\r\f\v]`|
|`\S`|Не пробельный|Противоположность `\s`|`[^ \t\n\r\f\v]`|

### Пользовательские наборы

|Синтаксис|Описание|Пример|Совпадёт|
|---|---|---|---|
|`[abc]`|Любой из символов|`[aeiou]`|`a`, `e`, `i`, `o`, `u`|
|`[a-z]`|Диапазон символов|`[a-z]`|`a` до `z`|
|`[^abc]`|Любой КРОМЕ|`[^0-9]`|Любой не-цифра|
|`[a-zA-Z]`|Комбинация диапазонов|`[a-zA-Z0-9]`|Буквы и цифры|
|`[-abc]`|Дефис в начале/конце|`[-+*/]`|`-`, `+`, `*`, `/`|
|`[a\-z]`|Экранированный дефис|`[a\-z]`|`a`, `-`, `z`|

**⚠️ Важно:** Внутри `[]` большинство спецсимволов теряют значение!

- `[.]` — буквальная точка (не "любой символ")
- `[*]` — буквальная звёздочка (не "0 или более")
- `[(+*)]` — буквально `(`, `+`, `*`, `)`

---

## ⚓ Якоря и границы

|Якорь|Описание|Пример|Совпадёт|Не совпадёт|
|---|---|---|---|---|
|`^`|Начало строки|`^Hello`|`Hello world`|`Say Hello`|
|`$`|Конец строки|`end$`|`The end`|`end.`|
|`\A`|Начало всего текста|`\AStart`|`Start...` (только в начале)||
|`\Z` / `\z`|Конец всего текста|`End\Z`|`...End` (только в конце)||
|`\b`|Граница слова|`\bcat\b`|`cat`, `a cat.`|`category`|
|`\B`|НЕ граница слова|`\Bcat\B`|`s**cat**ter`|`cat`|

**💡 Что такое граница слова (`\b`):**

- Между `\w` и `\W`
- Между `\w` и началом/концом строки
- Между началом/концом строки и `\w`

```python
import re

text = "cat cats category"

re.findall(r'cat', text)       # ['cat', 'cat', 'cat'] - все вхождения
re.findall(r'\bcat\b', text)   # ['cat'] - только целое слово
re.findall(r'\bcat', text)     # ['cat', 'cat', 'cat'] - начало слова
re.findall(r'cat\b', text)     # ['cat', 'cat'] - конец слова
```

---

## 🎯 Группы и захват

### Типы групп

|Синтаксис|Тип|Описание|Захватывает?|
|---|---|---|---|
|`(...)`|Захватывающая группа|Группирует и запоминает|✅ Да|
|`(?:...)`|Незахватывающая группа|Только группирует|❌ Нет|
|`(?P<name>...)`|Именованная группа|Группа с именем|✅ Да|
|`(?>...)`|Атомарная группа|Без отката назад|❌ Нет|

### Обратные ссылки

|Синтаксис|Описание|Пример|
|---|---|---|
|`\1`, `\2`, ...|Ссылка на группу по номеру|`(.)\1` → `aa`, `bb`|
|`(?P=name)`|Ссылка на именованную группу|`(?P<tag>\w+).*(?P=tag)`|
|`\g<1>`, `\g<name>`|Ссылка в замене (sub)|`re.sub(r'(\w+)', r'\g<1>!', text)`|

**Примеры:**

```python
import re

# Поиск повторяющихся слов
text = "the the cat sat sat on the mat"
re.findall(r'\b(\w+)\s+\1\b', text)  # ['the', 'sat']

# Именованные группы
pattern = r'(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})'
match = re.search(pattern, '2024-01-15')
match.group('year')   # '2024'
match.group('month')  # '01'

# HTML теги
re.findall(r'<(\w+)>.*?</\1>', '<h1>Title</h1>')  # ['h1']
```

---

## 👀 Lookahead и Lookbehind

### Таблица просмотров

|Синтаксис|Тип|Описание|Пример|
|---|---|---|---|
|`(?=...)`|Positive Lookahead|Если дальше идёт...|`\w+(?=:)`|
|`(?!...)`|Negative Lookahead|Если дальше НЕ идёт...|`\w+(?!:)`|
|`(?<=...)`|Positive Lookbehind|Если перед этим...|`(?<=@)\w+`|
|`(?<!...)`|Negative Lookbehind|Если перед этим НЕ...|`(?<!@)\w+`|
```
x(?=y)   → x, только если за x следует y
x(?!y)   → x, только если за x НЕ следует y
(?<=y)x  → x, только если перед x следует y
(?<!y)x  → x, только если перед x НЕ следует y

Кратко:
- ?=  → вперёд, да
- ?!  → вперёд, нет  
- ?<= → назад, да
- ?<! → назад, нет
```
**⚠️ Lookbehind должен быть фиксированной длины!**

### Примеры

```python
import re

# Positive Lookahead - слова перед двоеточием
text = "name: John, age: 30, city: NYC"
re.findall(r'\w+(?=:)', text)  # ['name', 'age', 'city']

# Negative Lookahead - числа НЕ перед 'px'
text = "10px 20em 30px 40"
re.findall(r'\d+(?!px)', text)  # ['20', '40']

# Positive Lookbehind - числа после '$'
text = "Price: $100, Tax: 10%, Total: $110"
re.findall(r'(?<=\$)\d+', text)  # ['100', '110']

# Negative Lookbehind - слова НЕ после '@'
text = "user@example.com admin@site.org hello"
re.findall(r'(?<!@)\b\w+', text)  # ['user', 'admin', 'hello']

# Пароль (8+ символов, есть буква и цифра)
password = r'^(?=.*[A-Za-z])(?=.*\d).{8,}$'
re.match(password, 'Pass1234')  # Match
re.match(password, '12345678')  # None (нет букв)
```

---

## 🛠️ Методы модуля re

### Основные функции

|Метод|Описание|Возвращает|
|---|---|---|
|`compile(pattern, flags)`|Компилирует regex|Pattern объект|
|`search(pattern, string)`|Ищет первое совпадение|Match или None|
|`match(pattern, string)`|Ищет в начале строки|Match или None|
|`fullmatch(pattern, string)`|Проверяет всю строку|Match или None|
|`findall(pattern, string)`|Все совпадения|Список строк/кортежей|
|`finditer(pattern, string)`|Итератор совпадений|Итератор Match|
|`split(pattern, string, maxsplit)`|Разделяет строку|Список строк|
|`sub(pattern, repl, string, count)`|Заменяет совпадения|Новая строка|
|`subn(pattern, repl, string, count)`|Заменяет + счётчик|(строка, количество)|
|`escape(pattern)`|Экранирует спецсимволы|Строка|

### Сравнение search, match, fullmatch

```python
import re

text = "abc def"

# search - ищет в любом месте
re.search(r'def', text)      # ✅ Match
re.search(r'abc', text)      # ✅ Match

# match - только с начала строки
re.match(r'abc', text)       # ✅ Match
re.match(r'def', text)       # ❌ None

# fullmatch - вся строка должна совпасть
re.fullmatch(r'abc def', text)   # ✅ Match
re.fullmatch(r'abc', text)       # ❌ None
```

### findall vs finditer

```python
import re

text = "email: user@example.com, admin@site.org"

# findall - возвращает список
emails = re.findall(r'\b\w+@\w+\.\w+\b', text)
# ['user@example.com', 'admin@site.org']

# finditer - возвращает итератор Match объектов
for match in re.finditer(r'\b\w+@\w+\.\w+\b', text):
    print(f"Найдено '{match.group()}' на позиции {match.start()}-{match.end()}")
# Найдено 'user@example.com' на позиции 7-23
# Найдено 'admin@site.org' на позиции 25-40
```

### sub - замена

```python
import re

text = "Hello World"

# Простая замена
re.sub(r'World', 'Python', text)  # 'Hello Python'

# Замена с группами
re.sub(r'(\w+) (\w+)', r'\2 \1', text)  # 'World Hello'

# Замена функцией
def upper(match):
    return match.group(0).upper()

re.sub(r'\w+', upper, text)  # 'HELLO WORLD'

# Ограничение количества замен
re.sub(r'a', 'X', 'banana', count=2)  # 'bXnXna'
```

---

## 📦 Методы Pattern объекта

```python
import re

pattern = re.compile(r'\d+')

# Те же методы, что у модуля, но без параметра pattern
pattern.search('abc 123')
pattern.match('123 abc')
pattern.findall('1 22 333')
pattern.finditer('a1 b2 c3')
pattern.split('a1b2c3')
pattern.sub('X', 'a1 b2')

# Свойства
pattern.pattern    # '\d+' - исходный паттерн
pattern.groups     # Количество групп
pattern.groupindex # Словарь именованных групп
pattern.flags      # Флаги компиляции
```

---

## 🎭 Методы Match объекта

|Метод|Описание|Пример|
|---|---|---|
|`group([n])`|Текст группы n (0=весь match)|`m.group(1)`|
|`group(name)`|Текст именованной группы|`m.group('email')`|
|`groups()`|Кортеж всех групп|`m.groups()`|
|`groupdict()`|Словарь именованных групп|`m.groupdict()`|
|`start([n])`|Начальная позиция группы|`m.start(0)`|
|`end([n])`|Конечная позиция группы|`m.end(0)`|
|`span([n])`|(start, end) группы|`m.span(0)`|
|`expand(template)`|Шаблон с подстановкой групп|`m.expand(r'\1-\2')`|
|`[index]`|Доступ к группе|`m[1]`, `m['name']`|

```python
import re

text = "Email: user@example.com"
pattern = r'(?P<user>\w+)@(?P<domain>\w+\.\w+)'
match = re.search(pattern, text)

# Доступ к группам
match.group(0)           # 'user@example.com' (весь match)
match.group(1)           # 'user'
match.group(2)           # 'example.com'
match.group('user')      # 'user'
match['domain']          # 'example.com'

# Все группы
match.groups()           # ('user', 'example.com')
match.groupdict()        # {'user': 'user', 'domain': 'example.com'}

# Позиции
match.start()            # 7
match.end()              # 23
match.span()             # (7, 23)

# Свойства
match.string             # 'Email: user@example.com'
match.re                 # объект Pattern
match.pos                # 0
match.endpos             # 23
```

---

## 🚩 Флаги
## Общий формат использования:

```python
re.findall(pattern, text, flags)     # Через параметр flags
re.findall(r'(?флаг)pattern', text)  # Встроенный в паттерн
```

| Полное имя      | Сокращённо | Встроенное имя | Назначение                                                                                                   |
| --------------- | ---------- | -------------- | ------------------------------------------------------------------------------------------------------------ |
| `re.IGNORECASE` | `re.I`     | `(?i)`         | Игнорирует регистр символов                                                                                  |
| `re.MULTILINE`  | `re.M`     | `(?m)`         | Меняет поведение `^` и `$`: они начинают работать **для каждой строки**, а не только для всей строки целиком |
| `re.DOTALL`     | `re.S`     | `(?s)`         | Заставляет метасимвол `.` совпадать **с любым символом**, включая `\n`                                       |
| `re.VERBOSE`    | `re.X`     | `(?x)`         | Разрешает комментарии и переносы строк в регулярном выражении                                                |
| `re.ASCII`      | `re.A`     | `(?a)`         | Ограничивает `\w`, `\d`, `\s` символами **ASCII**                                                            |
| `re.UNICODE`    | `re.U`     | `(?u)`         | Использует **Unicode-поведение** для `\w`, `\d`, `\s` (в Python 3 включён по умолчанию)                      |
| `re.LOCALE`     | `re.L`     | `(?L)`         | Учитывает региональные (locale) настройки при работе метасимволов                                            |
| `re.DEBUG`      | —          | —              | Выводит отладочную информацию о разборе регулярного выражения                                                |
| `re.NOFLAG`     | —          | —              | Явно указывает, что флаги не применяются                                                                     |
### Комбинирование флагов

```python
import re

# Через оператор |
pattern = re.compile(r'hello', re.I | re.M)

# Inline флаги
re.search(r'(?i)hello', 'HELLO')  # Match

# Inline флаги для части паттерна
re.search(r'(?i:hello) world', 'HELLO world')  # Match
re.search(r'(?i:hello) world', 'HELLO WORLD')  # None (WORLD не игнорируется)
```

### Примеры флагов

```python
import re

# IGNORECASE
re.search(r'hello', 'HELLO')           # None
re.search(r'hello', 'HELLO', re.I)     # Match

# MULTILINE
text = "line1\nline2"
re.findall(r'^line', text)             # ['line']
re.findall(r'^line', text, re.M)       # ['line', 'line']

# DOTALL
re.search(r'a.b', 'a\nb')              # None
re.search(r'a.b', 'a\nb', re.S)        # Match

# VERBOSE
pattern = re.compile(r"""
    \d{3}   # Код страны
    -       # Разделитель
    \d{4}   # Номер
""", re.X)
```

---

## 💡 Практические примеры

### 1. Email validation

```python
import re

email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'

re.match(email_pattern, 'user@example.com')     # ✅ Match
re.match(email_pattern, 'invalid.email')        # ❌ None
```

### 2. Телефонные номера

```python
import re

# Форматы: +7 (123) 456-78-90, 8-123-456-7890
phone = r'^[+]?[78][-\s]?[(]?\d{3}[)]?[-\s]?\d{3}[-\s]?\d{2}[-\s]?\d{2}$'

re.match(phone, '+7 (123) 456-78-90')  # ✅
re.match(phone, '8-123-456-7890')      # ✅
```

### 3. Извлечение данных

```python
import re

text = "Date: 2024-01-15, Price: $99.99, Quantity: 5"

# Именованные группы
pattern = r'Date: (?P<date>[\d-]+), Price: \$(?P<price>[\d.]+), Quantity: (?P<qty>\d+)'
match = re.search(pattern, text)

match.groupdict()
# {'date': '2024-01-15', 'price': '99.99', 'qty': '5'}
```

### 4. Замена с функцией

```python
import re

def price_converter(match):
    price = float(match.group(1))
    return f"€{price * 0.92:.2f}"

text = "Price: $100, Sale: $50"
re.sub(r'\$(\d+)', price_converter, text)
# 'Price: €92.00, Sale: €46.00'
```

### 5. Парсинг логов

```python
import re

log = "2024-01-15 10:30:45 ERROR [Database] Connection failed"

pattern = r'''
    (?P<date>\d{4}-\d{2}-\d{2})\s+
    (?P<time>\d{2}:\d{2}:\d{2})\s+
    (?P<level>\w+)\s+
    \[(?P<module>\w+)\]\s+
    (?P<message>.+)
'''

match = re.search(pattern, log, re.VERBOSE)
match.groupdict()
# {'date': '2024-01-15', 'time': '10:30:45', 'level': 'ERROR',
#  'module': 'Database', 'message': 'Connection failed'}
```

### 6. Валидация пароля

```python
import re

# Пароль: 8+ символов, буквы, цифры, спецсимволы
password_pattern = r'^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$'

re.match(password_pattern, 'Pass123!')    # ✅
re.match(password_pattern, 'password')    # ❌ (нет цифр/спецсимволов)
re.match(password_pattern, 'Pass1')       # ❌ (короткий)
```

### 7. HTML теги

```python
import re

html = "<h1>Title</h1><p>Text</p>"

# Извлечь текст из тегов
re.findall(r'<\w+>(.*?)</\w+>', html)
# ['Title', 'Text']

# Удалить все теги
re.sub(r'<[^>]+>', '', html)
# 'TitleText'
```

### 8. URL компоненты

```python
import re

url = "https://user:pass@example.com:8080/path?key=value#section"

pattern = r'''
    (?P<protocol>https?)://
    ((?P<user>\w+):(?P<pass>\w+)@)?
    (?P<host>[\w.]+)
    (:(?P<port>\d+))?
    (?P<path>/[^?#]*)?
    (\?(?P<query>[^#]*))?
    (\#(?P<fragment>.*))?
'''

match = re.search(pattern, url, re.VERBOSE)
match.groupdict()
```

---

## 🎓 Частые ошибки

### ❌ Неправильно → ✅ Правильно

|Ошибка|Проблема|Решение|
|---|---|---|
|`\b[A-Z]*\b`|Находит пустые строки|`\b[A-Z]+\b` (используй `+`)|
|`.*`|Жадный, берёт всё|`.*?` (ленивый)|
|`.`|Не ловит `\n`|Используй `re.DOTALL` или `[\s\S]`|
|`\d+`|Только цифры|`\d+\.?\d*` для чисел с точкой|
|Забыть `r`|`\d` → `\\d`|Всегда используй `r'...'`|
|`[A-z]`|Включает `[\]^_`|`[A-Za-z]`|

---

## 📌 Шпаргалка - одна таблица

|Что нужно|Паттерн|Пример|
|---|---|---|
|Email|`\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z]{2,}\b`|user@site.com|
|URL|`https?://[\w\-._~:/?#[\]@!$&'()*+,;=]+`|https://example.com|
|IPv4|`\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b`|192.168.1.1|
|Дата (YYYY-MM-DD)|`\d{4}-\d{2}-\d{2}`|2024-01-15|
|Время (HH:MM)|`\d{2}:\d{2}`|14:30|
|Телефон (RU)|`[+]?[78][-\s]?\(?\d{3}\)?[-\s]?\d{3}[-\s]?\d{2}[-\s]?\d{2}`|+7 (123) 456-78-90|
|Hex цвет|`#[0-9A-Fa-f]{6}`|#FF5733|
|Целое число|`-?\d+`|-123|
|Дробное число|`-?\d+\.?\d*`|-123.45|
|Слова из заглавных|`\b[A-Z]{2,}\b`|HELLO|
|HTML тег|`<([a-z]+)([^>]*)>.*?</\1>`|`<p>text</p>`|
|Убрать пробелы|`\s+` → `' '`|Нормализация|

---

## 🔥 Raw string (r'...') - ВАЖНО!

```python
# ❌ БЕЗ raw string
pattern = '\\d+\\s+\\w+'    # Сложно читать
re.search('\\b\\d{3}\\b', text)

# ✅ С raw string
pattern = r'\d+\s+\w+'      # Легко читать
re.search(r'\b\d{3}\b', text)

# Исключение: когда нужен буквальный \n
r'\n'   # два символа: \ и n
'\n'    # один символ: перевод строки
```

## 🔗 Связанные темы

- [[01 — 🔧 Основы регулярных выражений]]
- [[02 — ⚙️ re.compile и оптимизация]]
- [[03 — 🔀 Условные выражения и группы]]
- [[04 — ⚠️ ReDoS и Catastrophic Backtracking]]
- [[00 — 📖 Основы строк 🔹]]
- [[03 — 🔤 String константы]]
