---
tags:
  - python
  - тема/основы
  - статус/в-разработке
---

# 🔄 Циклы for и while

## 📌 Коротко

> Циклы позволяют повторять код несколько раз. **for** — для известного числа итераций, **while** — пока условие истинно.

---

## 🚀 Быстрая справка

```python
# for цикл
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# while цикл
while condition:
    print("Executing")
    condition = False

# break и continue
for i in range(10):
    if i == 5:
        break      # Выход из цикла
    if i == 2:
        continue   # Пропустить итерацию
    print(i)
```

---

## 📖 Примеры

### for цикл

```python
# Простой счёт
for i in range(5):
    print(i)        # 0, 1, 2, 3, 4

# По списку
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)    # apple, banana, cherry

# С индексом
for i, fruit in enumerate(fruits):
    print(i, fruit) # 0 apple, 1 banana, 2 cherry
```

### while цикл

```python
count = 0
while count < 5:
    print(count)    # 0, 1, 2, 3, 4
    count += 1

# Бесконечный цикл (не рекомендуется)
# while True:
#     print("Infinite")
```

### break и continue

```python
for i in range(10):
    if i == 5:
        break       # Выход из цикла
    print(i)        # 0, 1, 2, 3, 4

for i in range(5):
    if i == 2:
        continue    # Пропустить
    print(i)        # 0, 1, 3, 4
```

---

## 🔗 Связанные темы

- [[03 — 🔀 Условные операторы if-else]]
- [[04 — 📝 Ввод и вывод]]

## ❓ Вопросы / Непонятное

- ...
