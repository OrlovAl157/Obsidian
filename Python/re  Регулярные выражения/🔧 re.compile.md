

## 💡 Синтаксис

```python
regex = re.compile(pattern, flags=0)
```

**Что делает:** Компилирует регулярное выражение в объект `Pattern` для переиспользования.

---

## ✅ Два способа использования

### Способ 1: Передать в функции модуля re

```python
import re

regex = re.compile(r'\d+')
result = re.findall(regex, text)
```

### Способ 2: Вызвать как метод (рекомендуется!)

```python
import re

regex = re.compile(r'\d+')
result = regex.findall(text)  # Короче и понятнее
```

---

## 🎯 Доступные методы

```python
regex = re.compile(r'\d+')

regex.search(string, pos, endpos)      # Первое совпадение
regex.match(string, pos, endpos)       # С начала строки
regex.fullmatch(string, pos, endpos)   # Полное совпадение
regex.findall(string, pos, endpos)     # Все (список)
regex.finditer(string, pos, endpos)    # Все (итератор)
regex.sub(repl, string)                # Замена
regex.split(string)                    # Разделение
```

**Бонус:** Методы объекта поддерживают `pos` и `endpos` для поиска в части строки.

---

## 🔍 Аргументы pos и endpos

```python
import re

regex = re.compile(r'\d+')
text = 'foo12345barbaz'

regex.search(text)                  # '12345' (весь текст)
regex.search(text, pos=4)           # '2345'  (с индекса 4)
regex.search(text, endpos=7)        # '1234'  (до индекса 7)
regex.search(text, pos=4, endpos=7) # '234'   (от 4 до 7)
```

**Работают как срезы:** `string[pos:endpos]`

---

## 🚩 Флаги

```python
re.I  # IGNORECASE  - игнорировать регистр
re.M  # MULTILINE   - ^ и $ для каждой строки
re.S  # DOTALL      - . включает \n
re.X  # VERBOSE     - комментарии в regex
```

**Использование:**

```python
# Один флаг
regex = re.compile(r'test', re.I)

# Несколько флагов
regex = re.compile(r'^\w+', re.I | re.M)

# Флаг указывается один раз, работает везде
regex.search('TEST')  # Совпадёт (re.I)
regex.search('test')  # Совпадёт (re.I)
```

---

## 💎 Зачем нужна компиляция?

### 1. Читабельность и удобство

**❌ Плохо:**

```python
print(re.search(r'\d+', s1))
print(re.search(r'\d+', s2))
print(re.search(r'\d+', s3))
# Нужно изменить паттерн? Меняй в 3 местах!
```

**✅ Хорошо:**

```python
digit_pattern = re.compile(r'\d+')
print(digit_pattern.search(s1))
print(digit_pattern.search(s2))
print(digit_pattern.search(s3))
# Изменение паттерна — только в одном месте!
```

### 2. Переиспользование

```python
email = re.compile(r'\b[\w.-]+@[\w.-]+\.\w+\b', re.I)

def validate_email(email_str):
    return email.fullmatch(email_str) is not None

def extract_emails(text):
    return email.findall(text)

def mask_emails(text):
    return email.sub('[EMAIL]', text)
```

---

## 📊 Свойства объекта Pattern

```python
regex = re.compile(r'(\w+)@(\w+)', re.I)

regex.pattern      # '(\\w+)@(\\w+)'
regex.flags        # re.IGNORECASE
regex.groups       # 2 (количество групп)
regex.groupindex   # {'name': 1} (для именованных групп)
```

---

## 🔥 Практические примеры

### Пример 1: Валидация

```python
import re

email_regex = re.compile(r'^[\w.-]+@[\w.-]+\.\w+$', re.I)
phone_regex = re.compile(r'^\+?\d{1,3}[-.\s]?\d{1,4}[-.\s]?\d{1,9}$')

def validate(value, type):
    validators = {'email': email_regex, 'phone': phone_regex}
    return validators[type].match(value) is not None
```

### Пример 2: Парсинг логов

```python
import re

log_pattern = re.compile(
    r'(?P<ip>\d+\.\d+\.\d+\.\d+) .* "(?P<method>\w+) (?P<path>\S+)"'
)

def parse_log(line):
    match = log_pattern.match(line)
    return match.groupdict() if match else None
```

### Пример 3: Условная замена

```python
import re

price_pattern = re.compile(r'\$(\d+(?:\.\d{2})?)')

def increase_prices(text, percent=10):
    def replacer(m):
        price = float(m.group(1)) * (1 + percent/100)
        return f'${price:.2f}'
    return price_pattern.sub(replacer, text)
```

### Пример 4: re.VERBOSE для читаемости

```python
import re

email = re.compile(r'''
    ^              # Начало
    [\w.+-]+       # Локальная часть
    @              # @
    [\w.-]+        # Домен
    \.             # Точка
    [a-zA-Z]{2,}   # Зона
    $              # Конец
''', re.X | re.I)
```

### Пример 5: Именованные группы

```python
import re

date = re.compile(r'(?P<day>\d{2})/(?P<month>\d{2})/(?P<year>\d{4})')
match = date.search('Дата: 25/12/2024')

print(match.group('day'))    # '25'
print(match.groupdict())     # {'day': '25', 'month': '12', 'year': '2024'}
```

---

## ⚠️ Частые ошибки

```python
# ❌ Забыли raw string
regex = re.compile('\d+')  # ПЛОХО

# ✅ Используем raw string
regex = re.compile(r'\d+')  # ХОРОШО

# ❌ Неверный порядок аргументов
regex = re.compile(re.I, r'\d+')  # ОШИБКА

# ✅ Правильный порядок
regex = re.compile(r'\d+', re.I)  # ПРАВИЛЬНО
```

---

## 📝 Шпаргалка

### Создание

```python
regex = re.compile(r'\d+')              # Базовая
regex = re.compile(r'\d+', re.I)        # С флагом
regex = re.compile(r'\d+', re.I | re.M) # Несколько флагов
```

### Методы

```python
regex.search(text)        # Первое совпадение
regex.findall(text)       # Все совпадения
regex.sub(repl, text)     # Замена
regex.split(text)         # Разделение
```

### Флаги

|Флаг|Описание|
|---|---|
|`re.I`|Игнорировать регистр|
|`re.M`|^ и $ для каждой строки|
|`re.S`|. включает \n|
|`re.X`|Комментарии в regex|

---

## 🎯 Когда использовать

✅ **Используйте compile():**

- Паттерн используется многократно
- Нужна читабельность (осмысленное имя)
- Сложный паттерн (нужны комментарии с re.X)
- Нужны pos/endpos

❌ **Можно не использовать:**

- Паттерн используется один раз
- Очень простой паттерн

---

## ✨ Что запомнить

✅ **compile() создаёт объект Pattern**  
✅ **Вызывайте методы напрямую: `regex.search(text)`**  
✅ **Флаги указываются один раз при создании**  
✅ **Методы поддерживают pos/endpos**  
✅ **re кэширует паттерны, compile() для читабельности**  
✅ **Используйте re.X для сложных паттернов**  
✅ **Всегда используйте raw strings (r'')**