---
tags:
  - python
  - тема/основы
  - статус/в-разработке
---

# 🔀 Условные операторы if-else

## 📌 Коротко

> Условные операторы позволяют выполнять разный код в зависимости от условия (True или False).

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
    print("1")
elif condition2:
    print("2")
else:
    print("3")
```

---

## 📖 Примеры

### Простой if

```python
age = 18
if age >= 18:
    print("Adult")  # Adult
```

### if-else

```python
score = 45
if score >= 60:
    print("Pass")
else:
    print("Fail")  # Fail
```

### if-elif-else

```python
grade = 85
if grade >= 90:
    print("A")
elif grade >= 80:
    print("B")  # B
elif grade >= 70:
    print("C")
else:
    print("F")
```

### Множественные условия

```python
age = 25
has_license = True

if age >= 18 and has_license:
    print("Can drive")  # Can drive

if age < 13 or age > 65:
    print("Special discount")
```

---

## 🔗 Связанные темы

- [[02 — ➡️ Операторы]]
- [[04 — 🔄 Циклы for и while]]

## ❓ Вопросы / Непонятное

- ...
