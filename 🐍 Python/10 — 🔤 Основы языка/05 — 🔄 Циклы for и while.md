---
tags:
  - python
  - тема/основы
  - статус/завершён
---

# 🔄 Циклы for и while

## 📌 Коротко

> Циклы позволяют повторять код многократно. **for** — для известного числа итераций (по коллекции), **while** — пока условие истинно.

---

## 💡 Полезные советы

- **for для итерации** — по спискам, кортежам, строкам, диапазонам
- **while для условных циклов** — пока что-то не произойдёт
- **break выходит из цикла** — полностью прерывает цикл
- **continue пропускает итерацию** — переходит к следующей
- **else в цикле выполняется если цикл завершился нормально** — не выполняется если break

---

## 🚀 Быстрая справка

```python
# for цикл
for element in sequence:
    print(element)

# for с range
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# while цикл
while condition:
    print("Executing")

# break и continue
for i in range(10):
    if i == 3:
        break       # Выход из цикла
    if i == 1:
        continue    # Пропуск итерации
    print(i)
```

---

## 📖 Полные примеры

### for цикл - базовое

```python
# По списку
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)
# Вывод:
# apple
# banana
# cherry

# По кортежу
colors = ("red", "green", "blue")
for color in colors:
    print(color)

# По строке
for char in "Hello":
    print(char)
# Вывод:
# H
# e
# l
# l
# o
```

### for с range()

```python
# range(stop) - от 0 до stop-1
for i in range(5):
    print(i)
# Вывод: 0, 1, 2, 3, 4

# range(start, stop) - от start до stop-1
for i in range(2, 5):
    print(i)
# Вывод: 2, 3, 4

# range(start, stop, step) - с шагом
for i in range(0, 10, 2):
    print(i)
# Вывод: 0, 2, 4, 6, 8

# Отрицательный шаг (в обратном порядке)
for i in range(5, 0, -1):
    print(i)
# Вывод: 5, 4, 3, 2, 1
```

### for с enumerate() - получить индекс и значение

```python
fruits = ["apple", "banana", "cherry"]

# Без индекса
for fruit in fruits:
    print(fruit)  # apple, banana, cherry

# С индексом
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
# Вывод:
# 0: apple
# 1: banana
# 2: cherry

# С пользовательским начальным индексом
for num, fruit in enumerate(fruits, start=1):
    print(f"{num}: {fruit}")
# Вывод:
# 1: apple
# 2: banana
# 3: cherry
```

### for с zip() - итерация по нескольким последовательностям

```python
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]

# Одновременная итерация
for name, age in zip(names, ages):
    print(f"{name}: {age}")
# Вывод:
# Alice: 25
# Bob: 30
# Charlie: 35

# По словарю
person = {"name": "Alice", "age": 25, "city": "NYC"}
for key, value in person.items():
    print(f"{key}: {value}")
# Вывод:
# name: Alice
# age: 25
# city: NYC
```

### while цикл - базовое

```python
# Простой счётчик
count = 0
while count < 5:
    print(count)
    count += 1
# Вывод: 0, 1, 2, 3, 4

# С условием
password = ""
while password != "secret":
    password = input("Enter password: ")
print("Access granted!")

# Бесконечный цикл (⚠️ используйте редко)
# while True:
#     print("This repeats forever")
#     # break нужен чтобы выйти
```

### while с break

```python
# Выход из цикла по условию
count = 0
while True:
    print(count)
    count += 1
    if count >= 5:
        break  # Выход при count = 5
# Вывод: 0, 1, 2, 3, 4

# Поиск элемента
numbers = [1, 2, 3, 4, 5, 10]
target = 10
for num in numbers:
    if num == target:
        print(f"Found {target}!")
        break
else:
    print(f"{target} not found")
# Вывод: Found 10!
```

### while с continue

```python
# Пропуск нечётных чисел
count = 0
while count < 10:
    count += 1
    if count % 2 == 1:  # Нечётные
        continue  # Пропустить
    print(count)
# Вывод: 2, 4, 6, 8, 10
```

### for с break

```python
# Поиск в списке
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
for num in numbers:
    if num == 5:
        print(f"Found {num}!")
        break
else:
    print("Number not found")
# Вывод: Found 5!

# break в основном цикле
found = False
for i in range(10):
    for j in range(10):
        if i * j == 20:
            print(f"{i} * {j} = 20")
            found = True
            break
    if found:
        break
# Вывод: 4 * 5 = 20
```

### for с continue

```python
# Пропуск определённых значений
for i in range(10):
    if i == 3 or i == 7:
        continue  # Пропустить 3 и 7
    print(i)
# Вывод: 0, 1, 2, 4, 5, 6, 8, 9

# Пропуск пустых строк
lines = ["Hello", "", "World", "", "!"]
for line in lines:
    if not line:  # Пропустить пустые
        continue
    print(line)
# Вывод:
# Hello
# World
# !
```

### else в циклах (выполняется если цикл завершился нормально)

```python
# else выполняется если цикл завершился без break
for i in range(5):
    print(i)
else:
    print("Cycle completed normally")
# Вывод:
# 0
# 1
# 2
# 3
# 4
# Cycle completed normally

# else НЕ выполняется если был break
for i in range(5):
    if i == 3:
        print(f"Breaking at {i}")
        break
else:
    print("Cycle completed normally")
# Вывод:
# 0
# 1
# 2
# Breaking at 3
# (else не выведется)

# Практический пример - поиск
numbers = [1, 2, 3, 4, 5]
target = 10
for num in numbers:
    if num == target:
        print(f"Found {target}!")
        break
else:
    print(f"{target} not found in list")
# Вывод: 10 not found in list
```

### Вложенные циклы

```python
# Таблица умножения
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i}*{j}={i*j}", end="  ")
    print()  # Новая строка
# Вывод:
# 1*1=1  1*2=2  1*3=3
# 2*1=2  2*2=4  2*3=6
# 3*1=3  3*2=6  3*3=9

# Вложенные списки
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
for row in matrix:
    for element in row:
        print(element, end=" ")
    print()  # Новая строка
# Вывод:
# 1 2 3
# 4 5 6
# 7 8 9
```

---

## 🎯 Практические примеры

### Сумма чисел

```python
# Способ 1: for
total = 0
numbers = [1, 2, 3, 4, 5]
for num in numbers:
    total += num
print(f"Sum: {total}")  # Sum: 15

# Способ 2: while
total = 0
i = 0
while i < len(numbers):
    total += numbers[i]
    i += 1
print(f"Sum: {total}")  # Sum: 15

# Способ 3: встроенная функция
print(f"Sum: {sum(numbers)}")  # Sum: 15
```

### Поиск максимума

```python
numbers = [3, 7, 2, 9, 1, 5]
max_num = numbers[0]

for num in numbers:
    if num > max_num:
        max_num = num

print(f"Maximum: {max_num}")  # Maximum: 9
```

### Фильтрация данных

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = []

for num in numbers:
    if num % 2 == 0:
        evens.append(num)

print(evens)  # [2, 4, 6, 8, 10]

# Или более компактно (list comprehension)
evens = [num for num in numbers if num % 2 == 0]
```

### Обратный отсчёт

```python
# Способ 1: while с уменьшением
count = 5
while count > 0:
    print(count)
    count -= 1
print("Blast off!")
# Вывод:
# 5
# 4
# 3
# 2
# 1
# Blast off!

# Способ 2: for с range в обратном порядке
for count in range(5, 0, -1):
    print(count)
print("Blast off!")
```

### Ввод с проверкой

```python
while True:
    age = input("Enter your age (or 'quit' to exit): ")
    
    if age.lower() == 'quit':
        print("Goodbye!")
        break
    
    if not age.isdigit():
        print("Please enter a valid number")
        continue
    
    age = int(age)
    if age >= 18:
        print("You are an adult")
    else:
        print("You are a minor")
```

---

## 📊 Сравнение for и while

| Параметр | for | while |
|---|---|---|
| **Когда использовать** | Известно число итераций | Пока условие истинно |
| **Итерация по коллекции** | ✅ Естественно | ⚠️ Нужен индекс |
| **Бесконечный цикл** | ❌ Сложно | ✅ Просто (True) |
| **Прерывание по условию** | ✅ break | ✅ break |
| **Читаемость** | ✅ Лучше обычно | ⚠️ Зависит от кода |

---

## ⚠️ Частые ошибки

```python
# ❌ Забыли двоеточие
for i in range(5)
    print(i)
# SyntaxError: missing ':'

# ✅ Двоеточие обязательно
for i in range(5):
    print(i)

# ❌ Неправильные отступы
for i in range(5):
print(i)
# IndentationError

# ✅ Правильные отступы
for i in range(5):
    print(i)

# ❌ Бесконечный цикл без break
while True:
    x = input("Enter: ")
    # Если не будет break, цикл никогда не завершится

# ✅ Добавьте break
while True:
    x = input("Enter (or 'quit'): ")
    if x == 'quit':
        break

# ❌ Изменение списка во время итерации
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    fruits.remove(fruit)  # ❌ Опасно! Пропустит элементы

# ✅ Итерируйте по копии или создайте новый список
for fruit in fruits[:]:
    fruits.remove(fruit)
# или
fruits = [f for f in fruits if f != "banana"]
```

---

## 🔗 Связанные темы

- [[03 — 🔀 Условные операторы if-else]]
- [[05 — 📝 Ввод и вывод]]

## ❓ Вопросы / Непонятное

- ...
