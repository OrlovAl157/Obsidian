---
tags:
  - python
  - тема/задачи
  - тема/замыкания
  - тема/хеширование
  - статус/готово
---

# 🌶️ limited_hash — сужение диапазона хеша

## 📌 Задача

> Реализовать функцию `limited_hash(left, right, hash_function=hash)`, которая возвращает новую функцию. Эта функция принимает объект, вычисляет его хеш и **циклично** преобразует результат в диапазон `[left; right]`.

---

## 📋 Условие

```
limited_hash(10, 15)

hash_function(10)  → 10   # уже в диапазоне
hash_function(16)  → 10   # right + 1 → left
hash_function(17)  → 11   # right + 2 → left + 1
hash_function(9)   → 15   # left - 1 → right
hash_function(8)   → 14   # left - 2 → right - 1
```

---

## 💡 Ключевая идея — модульная арифметика

### Наивное решение (while-циклы)

```python
def limited_hash(left, right, hash_function=hash):
    def wrapper(obj):
        hash_num = hash_function(obj)

        if hash_num in range(left, right + 1):
            return hash_num
        elif hash_num > right:
            while hash_num > right:
                hash_num = left + hash_num - right - 1
            return hash_num
        elif hash_num < left:
            while hash_num < left:
                hash_num = right + hash_num - left + 1
            return hash_num

    return wrapper
```

**Проблема:** если хеш сильно выходит за диапазон — цикл работает очень долго. O(n) где n — расстояние от хеша до диапазона.

---

### Оптимальное решение — одна формула

```python
def limited_hash(left, right, hash_function=hash):
    def wrapper(obj):
        hash_num = hash_function(obj)

        if hash_num in range(left, right + 1):
            return hash_num
        else:
            return ((hash_num - left) % (right - left + 1)) + left

    return wrapper
```

**Сложность:** O(1) — одна операция независимо от величины хеша.

---

## 🧮 Как работает формула

Диапазон `[10; 15]`, размер = `15 - 10 + 1 = 6`:

```
Шаг 1: hash_num - left        # сдвигаем к диапазону [0; size-1]
Шаг 2: % (right - left + 1)  # циклично заворачиваем в [0; size-1]
Шаг 3: + left                 # сдвигаем обратно в [left; right]
```

**Проверка:**

```
hash_num = 17, диапазон [10; 15], размер 6:

17 - 10 = 7         # сдвиг к нулю
7 % 6 = 1           # завернули в [0; 5]
1 + 10 = 11 ✅      # сдвиг обратно

hash_num = 8, диапазон [10; 15], размер 6:

8 - 10 = -2         # отрицательное — тоже работает с %!
-2 % 6 = 4          # Python % всегда положителен!
4 + 10 = 14 ✅
```

> 💡 В Python `%` всегда возвращает **неотрицательный** результат если делитель положительный. Поэтому формула работает и для отрицательных хешей.

---

## 🏗 Паттерн — замыкание

`limited_hash` — это **замыкание**: внешняя функция возвращает внутреннюю, которая "помнит" `left`, `right` и `hash_function` из внешнего скоупа.

```python
# limited_hash — фабрика функций
h = limited_hash(0, 9)      # создаём специализированную функцию
h("hello")                  # используем её
h(42)                       # снова используем

# Можно передать свою хеш-функцию:
my_hash = limited_hash(0, 9, hash_function=lambda x: len(str(x)))
```

> 💡 Это также похоже на декоратор — функция принимает и возвращает функцию. Разница: декоратор оборачивает **ту же** функцию, замыкание создаёт **новую**.

---

## ⚠️ Можно упростить ещё больше

Проверка `if hash_num in range(...)` — лишняя, формула уже обрабатывает этот случай:

```python
def limited_hash(left, right, hash_function=hash):
    def wrapper(obj):
        return ((hash_function(obj) - left) % (right - left + 1)) + left
    return wrapper
```

Одна строка! Работает для всех случаев включая когда хеш уже в диапазоне.

---

## 🔗 Связанные темы

- [[Хеш-функции и хеширование]]
- [[Декораторы]]
- [[Замыкания (closures)]]

## ❓ Вопросы / Непонятное

- ...
