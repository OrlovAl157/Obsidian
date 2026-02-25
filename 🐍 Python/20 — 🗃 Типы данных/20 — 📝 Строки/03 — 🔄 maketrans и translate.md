# Памятка: str.maketrans() и translate()

## Быстрый справочник

|Задача                  |Синтаксис                 |Пример                                 |
|------------------------|--------------------------|---------------------------------------|
|**Замена символов**     |`str.maketrans(x, y)`     |`str.maketrans('aeiou', '12345')`      |
|**Удаление символов**   |`str.maketrans('', '', z)`|`str.maketrans('', '', '.,!?')`        |
|**Замена + Удаление**   |`str.maketrans(x, y, z)`  |`str.maketrans('aeiou', '12345', '.,')`|
|**Замена через словарь**|`str.maketrans(dict)`     |`str.maketrans({'a': '1', 'b': '2'})`  |

-----

## Описание

`maketrans()` создает таблицу перевода для замены/удаления множества символов за один проход. Таблица используется с методом `translate()`.

**Синтаксис:**

```python
str.maketrans(x, y, z)
# x - символы для замены (строка или словарь)
# y - символы замены (строка, если x - строка)
# z - символы для удаления (опционально)
```

-----

## 1. Замена символов (два аргумента)

```python
intab = "aeiou"      # Что заменять
outtab = "12345"     # На что заменять
trantab = str.maketrans(intab, outtab)

text = "this is string example"
result = text.translate(trantab)
print(result)  # th3s 3s str3ng 2x1mpl2
```

⚠️ **Важно:** `intab` и `outtab` должны быть **одинаковой длины**!

-----

## 2. Замена через словарь

```python
# Более гибкий способ
replace_dict = {'a': '1', 'b': '2', 'c': '3'}
trantab = str.maketrans(replace_dict)

text = "abc hello"
result = text.translate(trantab)
print(result)  # 123 hello
```

**Преимущества словаря:**

- Можно заменять на строки разной длины
- Можно заменять на None (удаление)
- Более читаемый код

```python
# Замена на многосимвольные строки
replace_dict = {'a': '[A]', 'b': '[B]'}
trantab = str.maketrans(replace_dict)
text = "abc"
print(text.translate(trantab))  # [A][B]c
```

-----

## 3. Удаление символов (три аргумента)

### ✅ ПРАВИЛЬНЫЙ способ удаления:

```python
# Первые два аргумента - ПУСТЫЕ строки
# Третий аргумент - символы для УДАЛЕНИЯ
trantab = str.maketrans('', '', ',!?')

text = "Hello, world! How are you?"
result = text.translate(trantab)
print(result)  # Hello world How are you
```

### ❌ ТИПИЧНЫЕ ОШИБКИ:

```python
# ❌ НЕПРАВИЛЬНО - пытается заменить, а не удалить
trantab = str.maketrans(marks, ' ' * len(marks))

# ❌ НЕПРАВИЛЬНО - лишние аргументы
trantab = str.maketrans(marks, '  ', '  ')

# ✅ ПРАВИЛЬНО
trantab = str.maketrans('', '', marks)
```

-----

## 4. Замена И удаление одновременно

```python
intab = "aeiou"
outtab = "12345"
del_chars = ".,!?"    # Что удалить

trantab = str.maketrans(intab, outtab, del_chars)
text = "Hello, world! How are you?"
result = text.translate(trantab)
print(result)  # H2ll4 w4rld H4w 1r2 y45
```

-----

## Сравнение методов удаления символов

### Метод 1: maketrans() (самый быстрый)

```python
def remove_marks(text, marks):
    trantab = str.maketrans('', '', marks)
    return text.translate(trantab)
```

### Метод 2: Генератор списка

```python
def remove_marks(text, marks):
    return ''.join([ch for ch in text if ch not in marks])
```

### Метод 3: Цикл replace()

```python
def remove_marks(text, marks):
    result = text
    for mark in marks:
        result = result.replace(mark, '')
    return result
```

**Производительность (на больших текстах):**

- `maketrans()` — **самый быстрый** ⚡
- `replace()` в цикле — средний
- Генератор — самый медленный

-----

## Практические примеры

### Пример 1: Шифр Цезаря

```python
import string

# Сдвиг на 1 букву
intab = string.ascii_lowercase
outtab = intab[1:] + intab[0]  # bcdef...a
trantab = str.maketrans(intab, outtab)

text = "hello world"
encrypted = text.translate(trantab)
print(encrypted)  # ifmmp xpsme
```

### Пример 2: Удаление пунктуации

```python
import string

text = "Hello, world! How are you?"
trantab = str.maketrans('', '', string.punctuation)
result = text.translate(trantab)
print(result)  # Hello world How are you
```

### Пример 3: Нормализация текста

```python
# Удаляем цифры и знаки препинания
text = "User123 said: Hello, World! @2024"
trantab = str.maketrans('', '', string.digits + string.punctuation)
result = text.translate(trantab)
print(result)  # User said Hello World 
```

### Пример 4: ROT13 шифрование

```python
import string

# ROT13 - сдвиг на 13 символов
lower = string.ascii_lowercase
upper = string.ascii_uppercase

rot13_lower = lower[13:] + lower[:13]
rot13_upper = upper[13:] + upper[:13]

trantab = str.maketrans(lower + upper, rot13_lower + rot13_upper)

text = "Hello World"
encrypted = text.translate(trantab)
print(encrypted)  # Uryyb Jbeyq
```

### Пример 5: Удаление с учётом регистра

```python
def remove_case_insensitive(text, chars):
    # Добавляем верхний и нижний регистр
    all_chars = chars.lower() + chars.upper()
    trantab = str.maketrans('', '', all_chars)
    return text.translate(trantab)

text = "Hello, WORLD! How ARE you?"
result = remove_case_insensitive(text, "aeiou")
print(result)  # Hll, WRLD! Hw R y?
```

-----

## Функция со счётчиком вызовов

```python
def remove_marks(text, marks):
    """Удаляет все указанные символы из текста"""
    
    # Счётчик вызовов функции
    if not hasattr(remove_marks, 'count'):
        remove_marks.count = 0
    remove_marks.count += 1
    
    # Удаление символов через maketrans
    trantab = str.maketrans('', '', marks)
    result = text.translate(trantab)
    
    return result

# Использование
text = "Hello, world! How are you?"
print(remove_marks(text, ",!?"))     # Hello world How are you
print(remove_marks(text, "aeiou"))   # Hll, wrld! Hw r y?
print(f"Вызовов: {remove_marks.count}")  # Вызовов: 2
```

-----

## Ключевые правила

✅ **Что нужно знать:**

- `maketrans()` создает таблицу, `translate()` применяет её
- Для замены: первые два аргумента одинаковой длины
- Для удаления: `str.maketrans('', '', 'символы')`
- Символы, не указанные в таблице, остаются без изменений
- `maketrans()` — самый быстрый способ для массовых операций

❌ **Частые ошибки:**

- Попытка удалить через замену на пустую строку: `str.maketrans(marks, '')`
- Разная длина строк при замене
- Забыть применить `.translate()` к результату

-----

## Итог

**`maketrans()` + `translate()`** = мощный инструмент для:

- Пакетной замены символов
- Быстрого удаления символов
- Шифрования текста
- Нормализации данных

**Золотое правило:** Для удаления всегда используйте `str.maketrans('', '', 'удаляемые_символы')`