
#python #модули

---

## Что это?

`inflection` — библиотека для работы с формами слов на **английском языке**. Используется в веб-фреймворках, ORM и генераторах кода для автоматического преобразования имён.

```bash
pip install inflection
```

```python
import inflection
```

> ⚠️ Работает **только с английским языком**.

---

## Множественное и единственное число

```python
# Единственное → множественное
inflection.pluralize("cat")       # → "cats"
inflection.pluralize("mouse")     # → "mice"
inflection.pluralize("person")    # → "people"
inflection.pluralize("octopus")   # → "octopi"
inflection.pluralize("matrix")    # → "matrices"
inflection.pluralize("quiz")      # → "quizzes"

# Множественное → единственное
inflection.singularize("cats")    # → "cat"
inflection.singularize("mice")    # → "mouse"
inflection.singularize("people")  # → "person"
inflection.singularize("buses")   # → "bus"
```

> 💡 Библиотека знает о нерегулярных формах (irregular forms) — `mouse → mice`, `person → people` и т.д.

---

## CamelCase и snake_case

```python
# snake_case → CamelCase
inflection.camelize("device_type")        # → "DeviceType"
inflection.camelize("user_profile")       # → "UserProfile"

# CamelCase → snake_case
inflection.underscore("DeviceType")       # → "device_type"
inflection.underscore("UserProfile")      # → "user_profile"
inflection.underscore("HTMLParser")       # → "html_parser"

# Первая буква строчная (lowerCamelCase)
inflection.camelize("device_type", False) # → "deviceType"
```

---

## Humanize и Titleize

```python
# humanize — читаемый вид из snake_case
inflection.humanize("employee_salary")   # → "Employee salary"
inflection.humanize("author_id")         # → "Author"  (убирает _id)
inflection.humanize("user_name")         # → "User name"

# titleize — каждое слово с заглавной
inflection.titleize("man from the boondocks")  # → "Man From The Boondocks"
inflection.titleize("x-men: the last stand")   # → "X Men: The Last Stand"
inflection.titleize("device_type")             # → "Device Type"
```

---

## Ordinal — порядковые числа

```python
# Только суффикс
inflection.ordinal(1)    # → "st"
inflection.ordinal(2)    # → "nd"
inflection.ordinal(3)    # → "rd"
inflection.ordinal(4)    # → "th"
inflection.ordinal(11)   # → "th"  (исключение)
inflection.ordinal(21)   # → "st"
inflection.ordinal(13)   # → "th"  (исключение)

# Число + суффикс
inflection.ordinalize(1)    # → "1st"
inflection.ordinalize(2)    # → "2nd"
inflection.ordinalize(3)    # → "3rd"
inflection.ordinalize(4)    # → "4th"
inflection.ordinalize(11)   # → "11th"
inflection.ordinalize(42)   # → "42nd"
inflection.ordinalize(103)  # → "103rd"
```

---

## Dasherize

```python
# Заменяет подчёркивания на дефисы
inflection.dasherize("puni_puni")        # → "puni-puni"
inflection.dasherize("user_profile")     # → "user-profile"
inflection.dasherize("background_color") # → "background-color"
```

---

## Tableize и Classify — для ORM

Используются для связи имён Python-классов с названиями таблиц в базе данных:

```python
# Имя класса → имя таблицы (snake_case + plural)
inflection.tableize("UserProfile")    # → "user_profiles"
inflection.tableize("BlogPost")       # → "blog_posts"
inflection.tableize("Person")         # → "people"

# Имя таблицы → имя класса (CamelCase + singular)
inflection.classify("user_profiles")  # → "UserProfile"
inflection.classify("blog_posts")     # → "BlogPost"
inflection.classify("people")         # → "Person"
```

---

## Transliterate и Parameterize

```python
# Транслитерация — убирает диакритические знаки
inflection.transliterate("Ødegård")    # → "Odegard"
inflection.transliterate("André")      # → "Andre"
inflection.transliterate("Ñoño")       # → "Nono"

# Parameterize — создаёт URL-slug
inflection.parameterize("Donald E. Knuth")         # → "donald-e-knuth"
inflection.parameterize("Hello World!")             # → "hello-world"
inflection.parameterize("My Blog Post", "_")        # → "my_blog_post" (свой разделитель)
inflection.parameterize("über coöl")               # → "uber-cool"
```

---

## Быстрая шпаргалка

|Функция|Что делает|Пример|
|---|---|---|
|`pluralize(s)`|единственное → множественное|`"cat"` → `"cats"`|
|`singularize(s)`|множественное → единственное|`"mice"` → `"mouse"`|
|`camelize(s)`|snake_case → CamelCase|`"user_name"` → `"UserName"`|
|`underscore(s)`|CamelCase → snake_case|`"UserName"` → `"user_name"`|
|`humanize(s)`|snake_case → читаемый вид|`"user_name"` → `"User name"`|
|`titleize(s)`|каждое слово с заглавной|`"hello world"` → `"Hello World"`|
|`ordinalize(n)`|число → порядковое|`1` → `"1st"`|
|`ordinal(n)`|только суффикс|`1` → `"st"`|
|`dasherize(s)`|`_` → `-`|`"user_name"` → `"user-name"`|
|`tableize(s)`|класс → таблица|`"UserPost"` → `"user_posts"`|
|`classify(s)`|таблица → класс|`"user_posts"` → `"UserPost"`|
|`transliterate(s)`|убирает диакритику|`"André"` → `"Andre"`|
|`parameterize(s)`|строка → URL-slug|`"Hello World!"` → `"hello-world"`|