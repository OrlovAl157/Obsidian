---
tags:
  - python
  - тема/основы
  - статус/завершён
---

# 🔀 Условные операторы if-else

## 📌 Коротко

> Условные операторы позволяют выполнять разный код в зависимости от условия. Python использует **if**, **elif** и **else** для ветвления логики.

---

## 💡 Полезные советы

- **if без else работает** — можно проверять одно условие
- **elif может быть много** — но else только один в конце
- **Отступы важны** — Python использует отступы вместо скобок
- **Ternary оператор экономит код** — `x if condition else y`
- **Используйте в для сложных условий** — не усложняйте читаемость

---

## 🚀 Быстрая справка

```python
# Простой if
if condition:
    print("True")

# if-else
if condition:
    print("True")
else:
    print("False")

# if-elif-else
if condition1:
    print("First")
elif condition2:
    print("Second")
elif condition3:
    print("Third")
else:
    print("None matched")

# Ternary (одна строка)
result = value_if_true if condition else value_if_false
```

---

## 📖 Полные примеры

### Простой if

```python
age = 18

if age >= 18:
    print("You are an adult")
# Вывод: You are an adult

# if без else - просто не выполнится если False
age = 10
if age >= 18:
    print("Adult")  # Не выведется
# (ничего не выводится)
```

### if-else (два варианта)

```python
score = 75

if score >= 60:
    print("Pass")
else:
    print("Fail")
# Вывод: Pass

# Другой пример
temperature = 15

if temperature >= 20:
    print("Warm")
else:
    print("Cold")
# Вывод: Cold
```

### if-elif-else (множество вариантов)

```python
# Система оценок
grade = 85

if grade >= 90:
    rating = "A"
elif grade >= 80:
    rating = "B"
elif grade >= 70:
    rating = "C"
elif grade >= 60:
    rating = "D"
else:
    rating = "F"

print(f"Grade: {rating}")  # Grade: B

# Классификация возраста
age = 45

if age < 13:
    category = "Child"
elif age < 18:
    category = "Teen"
elif age < 65:
    category = "Adult"
else:
    category = "Senior"

print(f"Category: {category}")  # Category: Adult
```

### Множественные условия (AND)

```python
age = 25
has_license = True

# Оба условия должны быть True
if age >= 18 and has_license:
    print("Can drive")
# Вывод: Can drive

# Это не выведется
age = 16
has_license = True
if age >= 18 and has_license:
    print("Can drive")
# (ничего не выводится)
```

### Множественные условия (OR)

```python
# Хотя бы одно условие должно быть True
age = 10
is_authorized = True

if age >= 18 or is_authorized:
    print("Access granted")
# Вывод: Access granted

# Оба False
age = 10
is_authorized = False
if age >= 18 or is_authorized:
    print("Access granted")
# (ничего не выводится)
```

### Инверсия с NOT

```python
is_active = False

if not is_active:
    print("Account is inactive")
# Вывод: Account is inactive

# Без not (усложняет читаемость)
if is_active == False:
    print("Account is inactive")
```

### Вложенные условия

```python
age = 25
income = 50000
credit_score = 750

# Вложенные if
if age >= 21:
    if income >= 30000:
        if credit_score >= 700:
            print("Approved for loan")
        else:
            print("Credit score too low")
    else:
        print("Income too low")
else:
    print("Too young")
# Вывод: Approved for loan

# Лучше - с AND (более читаемо)
if age >= 21 and income >= 30000 and credit_score >= 700:
    print("Approved for loan")
```

### Ternary оператор (одна строка)

```python
# Синтаксис: значение_если_true if условие else значение_если_false

age = 25
status = "Adult" if age >= 18 else "Minor"
print(status)  # Adult

# С вычислениями
score = 75
result = "Pass" if score >= 60 else "Fail"
print(result)  # Pass

# В функции
def get_discount(age):
    return 20 if age >= 65 else 0

print(get_discount(70))  # 20
print(get_discount(30))  # 0

# Вложенные ternary (для сложных случаев)
score = 85
grade = "A" if score >= 90 else \
        "B" if score >= 80 else \
        "C" if score >= 70 else \
        "D" if score >= 60 else \
        "F"
print(grade)  # B
```

---

## 🎯 Практические примеры

### Валидация входных данных

```python
username = "alice123"
password = "secure_pass"

if len(username) < 3:
    print("Username too short")
elif len(password) < 8:
    print("Password too weak")
elif not username.isalnum():
    print("Username contains invalid characters")
else:
    print("Registration successful")
# Вывод: Registration successful
```

### Система доступа

```python
role = "admin"
action = "delete"

if role == "admin":
    print(f"Admin: {action} allowed")
elif role == "editor" and action != "delete":
    print(f"Editor: {action} allowed")
elif role == "viewer" and action == "read":
    print(f"Viewer: read allowed")
else:
    print("Access denied")
# Вывод: Admin: delete allowed
```

### Определение дня недели

```python
day_num = 3

if day_num == 1:
    day = "Monday"
elif day_num == 2:
    day = "Tuesday"
elif day_num == 3:
    day = "Wednesday"
elif day_num == 4:
    day = "Thursday"
elif day_num == 5:
    day = "Friday"
elif day_num == 6:
    day = "Saturday"
elif day_num == 7:
    day = "Sunday"
else:
    day = "Invalid"

print(day)  # Wednesday
```

### Расчёт цены с условиями

```python
quantity = 15
price_per_item = 10

# Скидки в зависимости от количества
if quantity >= 100:
    discount = 0.2  # 20%
elif quantity >= 50:
    discount = 0.15  # 15%
elif quantity >= 20:
    discount = 0.1   # 10%
elif quantity >= 10:
    discount = 0.05  # 5%
else:
    discount = 0     # 0%

total = quantity * price_per_item
discount_amount = total * discount
final_price = total - discount_amount

print(f"Total: ${total}")
print(f"Discount: {discount*100}% (${discount_amount})")
print(f"Final: ${final_price}")
# Вывод:
# Total: $150
# Discount: 5% ($7.5)
# Final: $142.5
```

### Проверка диапазонов

```python
temperature = 22

# Проверка диапазонов
if temperature < 0:
    condition = "Freezing"
elif 0 <= temperature < 10:
    condition = "Cold"
elif 10 <= temperature < 20:
    condition = "Cool"
elif 20 <= temperature < 30:
    condition = "Warm"
else:
    condition = "Hot"

print(f"Temperature: {condition}")  # Temperature: Warm

# Альтернатива - цепочки сравнений
if 20 <= temperature < 30:
    print("Pleasant temperature")
# Вывод: Pleasant temperature
```

### Валидация пароля

```python
password = "Pass123!"

has_upper = any(c.isupper() for c in password)
has_lower = any(c.islower() for c in password)
has_digit = any(c.isdigit() for c in password)
has_special = any(c in "!@#$%^&*" for c in password)
is_long = len(password) >= 8

if is_long and has_upper and has_lower and has_digit and has_special:
    print("Strong password")
elif is_long and has_upper and has_lower and has_digit:
    print("Medium password")
elif is_long and (has_upper or has_digit):
    print("Weak password")
else:
    print("Very weak password")
# Вывод: Strong password
```

---

## 📊 Сравнение стилей

### Стиль 1: if-elif-else

```python
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "F"
```

**Плюсы:** Читаемо, понятно, хорошо для сложной логики
**Минусы:** Многовато кода

### Стиль 2: Ternary

```python
grade = "A" if score >= 90 else \
        "B" if score >= 80 else \
        "C" if score >= 70 else \
        "F"
```

**Плюсы:** Компактно, одна строка
**Минусы:** Сложнее читать при вложении

### Стиль 3: Словарь (для отображения)

```python
grade_map = {
    (90, 100): "A",
    (80, 90): "B",
    (70, 80): "C",
    (0, 70): "F"
}

grade = next(v for k, v in grade_map.items() if k[0] <= score < k[1])
```

**Плюсы:** Очень компактно для отображений
**Минусы:** Менее понятно

---

## ⚠️ Частые ошибки

```python
# ❌ Забыли двоеточие после условия
if x > 5
    print("x is big")
# SyntaxError: missing ':'

# ✅ Двоеточие обязательно
if x > 5:
    print("x is big")

# ❌ Неправильные отступы
if x > 5:
print("x is big")  # IndentationError

# ✅ 4 пробела (или Tab)
if x > 5:
    print("x is big")

# ❌ == вместо = в сравнении уже описано в операторах
if x = 5:  # SyntaxError (присваивание вместо сравнения)
    pass

# ✅ Используйте == для сравнения
if x == 5:
    pass

# ❌ else с elif создаст ошибку
if x > 5:
    print("big")
else:
    print("not big")
elif x == 5:  # SyntaxError: elif after else
    print("equal")

# ✅ elif перед else
if x > 5:
    print("big")
elif x == 5:
    print("equal")
else:
    print("small")

# ❌ Забыли обработать все варианты
day = 8
if day == 1:
    name = "Monday"
# ...остальные дни...
# Если day = 8, name не определена!

# ✅ Используйте else для остальных случаев
else:
    name = "Invalid day"
```

---

## 🔗 Связанные темы

- [[02 — ➡️ Операторы]]
- [[04 — 🔄 Циклы for и while]]
- [[09 — 🔀 Условные выражения match-case]]

## ❓ Вопросы / Непонятное

- ...
