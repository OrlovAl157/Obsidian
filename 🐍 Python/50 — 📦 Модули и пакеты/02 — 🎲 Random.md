---
tags:
  - python
  - тема/модули
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
сторонний_пакет: false
---

# Памятка: Модуль random в Python

## 📥 Импорт модуля

```python
# Стандартный импорт
import random
random.randint(1, 10)           # → использование через random.

# Импорт с псевдонимом
import random as rnd
rnd.randint(1, 10)              # → использование через rnd.

# Импорт конкретных функций
from random import randint, choice, shuffle
randint(1, 10)                  # → без префикса random.
choice([1, 2, 3])

# Импорт всех функций (не рекомендуется)
from random import *
randint(1, 10)                  # → может привести к конфликтам имён
```

## 💡 Полезные советы

- **Импортируйте модуль**: `import random` перед использованием
- **Псевдослучайность**: числа предсказуемы, но выглядят случайными
- **Установите seed**: `random.seed(42)` для воспроизводимых результатов
- **Для криптографии**: используйте модуль `secrets`, а не `random`
- **random.choice()**: выбрать случайный элемент из последовательности
- **random.shuffle()**: перемешивает список на месте (изменяет оригинал)
- **random.sample()**: выбрать несколько уникальных элементов
- **Диапазоны**: `randint(a, b)` включает b, `randrange(a, b)` исключает b

## 🚀 Быстрая справка

| Задача                     | Функция             | Пример                         |
| -------------------------- | ------------------- | ------------------------------ |
| Случайное float [0.0, 1.0) | `random()`          | `random.random()`              |
| Случайное int [a, b]       | `randint(a, b)`     | `random.randint(1, 10)`        |
| Случайное int [a, b)       | `randrange(a, b)`   | `random.randrange(0, 10)`      |
| Случайное float [a, b]     | `uniform(a, b)`     | `random.uniform(1.5, 10.5)`    |
| Случайный элемент          | `choice(seq)`       | `random.choice([1,2,3])`       |
| Несколько элементов        | `choices(seq, k=n)` | `random.choices([1,2,3], k=5)` |
| Уникальные элементы        | `sample(seq, k=n)`  | `random.sample([1,2,3], k=2)`  |
| Перемешать список          | `shuffle(list)`     | `random.shuffle(my_list)`      |
| Установить seed            | `seed(n)`           | `random.seed(42)`              |
| Нормальное распределение   | `gauss(mu, sigma)`  | `random.gauss(0, 1)`           |

## 🎲 Случайные числа

### Случайные float числа

```python
import random

# Случайное число от 0.0 до 1.0 (не включая 1.0)
random.random()                 # → 0.37444887175646646

# Случайное число в диапазоне [a, b]
random.uniform(1.5, 10.5)       # → 7.8459283264
random.uniform(10, 20)          # → 15.234567

# С указанным количеством знаков
round(random.uniform(1, 10), 2)  # → 5.73
```

### Случайные целые числа

```python
# Случайное целое [a, b] (включая оба конца)
random.randint(1, 10)           # → 7 (от 1 до 10 включительно)
random.randint(1, 100)          # → 42

# Случайное целое [a, b) (не включая b)
random.randrange(10)            # → 0-9 (от 0 до 9)
random.randrange(1, 11)         # → 1-10 (от 1 до 10)
random.randrange(0, 101, 5)     # → 0, 5, 10, ..., 100 (с шагом 5)

# Примеры с шагом
random.randrange(0, 10, 2)      # → чётные: 0, 2, 4, 6, 8
random.randrange(1, 10, 2)      # → нечётные: 1, 3, 5, 7, 9
```

## 🎯 Выбор элементов

### Один случайный элемент

```python
# Из списка
fruits = ["apple", "banana", "cherry", "date"]
random.choice(fruits)           # → "banana"

# Из строки
random.choice("abcdefg")        # → "d"

# Из кортежа
random.choice((1, 2, 3, 4, 5))  # → 3

# Из range
random.choice(range(100))       # → 73

# Случайная буква
import string
random.choice(string.ascii_letters)  # → "K"
random.choice(string.ascii_lowercase)  # → "m"
random.choice(string.digits)    # → "7"
```

### Несколько случайных элементов (с повторениями)

```python
numbers = [1, 2, 3, 4, 5]

# Выбрать k элементов (могут повторяться)
random.choices(numbers, k=3)    # → [2, 2, 5]
random.choices(numbers, k=10)   # → [1, 3, 3, 2, 5, 1, 4, 2, 3, 1]

# С весами (вероятностями)
items = ["яблоко", "груша", "банан"]
weights = [5, 2, 3]             # яблоко чаще
random.choices(items, weights=weights, k=10)
# → больше яблок

# Со встроенным подсчётом вероятностей
results = random.choices(["орёл", "решка"], k=100)
results.count("орёл")           # → примерно 50
```

### Несколько уникальных элементов (без повторений)

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Выбрать k уникальных элементов
random.sample(numbers, k=3)     # → [7, 2, 9]
random.sample(numbers, k=5)     # → [1, 8, 4, 6, 3]

# Нельзя выбрать больше, чем есть
# random.sample(numbers, k=20)  # ❌ ValueError!

# Из строки
random.sample("abcdefg", k=3)   # → ['c', 'a', 'f']

# Из range
random.sample(range(100), k=10)  # → 10 уникальных чисел

# Использование для перемешивания (копия)
shuffled = random.sample(numbers, k=len(numbers))
```

## 🔀 Перемешивание

```python
# Перемешать список (изменяет оригинал!)
cards = ["А", "К", "Д", "В", "10", "9", "8", "7"]
random.shuffle(cards)           # → cards изменён
print(cards)                    # → ['7', 'Д', '10', 'А', ...]

# Важно: shuffle возвращает None!
# shuffled = random.shuffle(cards)  # ❌ shuffled будет None

# Перемешивание с сохранением оригинала
original = [1, 2, 3, 4, 5]
shuffled = original.copy()
random.shuffle(shuffled)        # → только shuffled изменён

# Альтернатива через sample
shuffled = random.sample(original, k=len(original))
```

## 🔢 Распределения вероятностей

### Равномерное распределение

```python
# Уже рассмотрено выше
random.random()                 # → [0.0, 1.0)
random.uniform(a, b)            # → [a, b]
random.randint(a, b)            # → [a, b]
```

### Нормальное (Гауссово) распределение

```python
# Нормальное распределение (среднее, стандартное отклонение)
random.gauss(0, 1)              # → стандартное нормальное
random.gauss(100, 15)           # → среднее=100, σ=15

# Альтернатива (немного быстрее)
random.normalvariate(0, 1)      # → то же самое

# Пример: IQ (среднее=100, σ=15)
iq = random.gauss(100, 15)
print(f"IQ: {iq:.0f}")          # → IQ: 103

# Генерация нескольких значений
iqs = [random.gauss(100, 15) for _ in range(100)]
```

### Другие распределения

```python
# Экспоненциальное распределение
random.expovariate(1.5)         # → λ = 1.5

# Логнормальное распределение
random.lognormvariate(0, 1)     # → μ = 0, σ = 1

# Гамма-распределение
random.gammavariate(2, 1)       # → α = 2, β = 1

# Бета-распределение
random.betavariate(2, 5)        # → α = 2, β = 5

# Распределение Парето
random.paretovariate(3)         # → α = 3

# Распределение Вейбулла
random.weibullvariate(1, 1.5)   # → α = 1, β = 1.5

# Треугольное распределение
random.triangular(0, 100, 75)   # → low=0, high=100, mode=75
```

## 🎲 Управление генератором

### Установка seed (зерна)

```python
# Установить seed для воспроизводимости
random.seed(42)
print(random.random())          # → всегда 0.6394267984578837
print(random.random())          # → всегда 0.025010755222666936

# Сброс к случайному состоянию
random.seed()                   # → использует системное время

# Seed из строки
random.seed("мой секретный ключ")

# Получить состояние генератора
state = random.getstate()

# Восстановить состояние
random.setstate(state)
```

### Создание отдельного генератора

```python
# Создать свой генератор (не влияет на глобальный)
rng = random.Random(42)
rng.random()                    # → использует свой seed
rng.randint(1, 10)

# Полезно для многопоточности
rng1 = random.Random(1)
rng2 = random.Random(2)
```

## 🎯 Практические примеры

### Бросок кубика

```python
def roll_dice(sides=6):
    return random.randint(1, sides)

roll_dice()                     # → 4 (обычный кубик)
roll_dice(20)                   # → 17 (20-гранный)

# Бросить несколько кубиков
def roll_multiple(n=2, sides=6):
    return [random.randint(1, sides) for _ in range(n)]

roll_multiple(3)                # → [4, 2, 6]
```

### Бросок монеты

```python
def flip_coin():
    return random.choice(["орёл", "решка"])

flip_coin()                     # → "орёл"

# Множественные броски
def flip_coins(n):
    return [flip_coin() for _ in range(n)]

flip_coins(10)                  # → ['орёл', 'решка', 'орёл', ...]

# С вероятностью
def weighted_flip(heads_prob=0.5):
    return "орёл" if random.random() < heads_prob else "решка"

weighted_flip(0.7)              # → 70% вероятность орла
```

### Случайная строка

```python
import string

# Случайная строка заданной длины
def random_string(length=10):
    letters = string.ascii_letters
    return ''.join(random.choice(letters) for _ in range(length))

random_string()                 # → "xKmPqWjHzN"
random_string(5)                # → "AbCdE"

# Буквы и цифры
def random_alphanumeric(length=10):
    chars = string.ascii_letters + string.digits
    return ''.join(random.choice(chars) for _ in range(length))

random_alphanumeric()           # → "aB3xK9mP2q"

# Безопасный пароль
def generate_password(length=12):
    chars = string.ascii_letters + string.digits + string.punctuation
    return ''.join(random.choice(chars) for _ in range(length))

generate_password()             # → "Kj#9mP$2qW!z"
```

### Случайный цвет

```python
# RGB цвет
def random_rgb():
    return (random.randint(0, 255), 
            random.randint(0, 255), 
            random.randint(0, 255))

random_rgb()                    # → (127, 234, 89)

# HEX цвет
def random_hex_color():
    return f"#{random.randint(0, 0xFFFFFF):06x}"

random_hex_color()              # → "#a3f5d2"
```

### Случайная дата

```python
from datetime import datetime, timedelta

def random_date(start_date, end_date):
    delta = end_date - start_date
    random_days = random.randint(0, delta.days)
    return start_date + timedelta(days=random_days)

start = datetime(2020, 1, 1)
end = datetime(2024, 12, 31)
random_date(start, end)         # → 2022-07-15 или другая дата
```

### Перемешивание карт

```python
# Создание колоды
def create_deck():
    suits = ["♠", "♥", "♦", "♣"]
    ranks = ["2", "3", "4", "5", "6", "7", "8", "9", "10", "В", "Д", "К", "Т"]
    return [f"{rank}{suit}" for suit in suits for rank in ranks]

# Перемешать и раздать
deck = create_deck()
random.shuffle(deck)
hand = deck[:5]                 # → первые 5 карт
print(hand)                     # → ['7♥', 'К♠', '3♦', 'Т♣', '9♥']
```

### Выбор победителя

```python
participants = ["Алиса", "Боб", "Карл", "Даша", "Елена"]

# Один победитель
winner = random.choice(participants)
print(f"Победитель: {winner}")

# Топ-3
top3 = random.sample(participants, k=3)
print(f"Призёры: {top3}")
```

### Случайные координаты

```python
# На плоскости
def random_point_2d(x_range, y_range):
    x = random.uniform(*x_range)
    y = random.uniform(*y_range)
    return (x, y)

random_point_2d((0, 100), (0, 100))  # → (45.3, 78.9)

# В круге
import math

def random_point_in_circle(radius):
    angle = random.uniform(0, 2 * math.pi)
    r = radius * math.sqrt(random.random())
    x = r * math.cos(angle)
    y = r * math.sin(angle)
    return (x, y)
```

### Моделирование вероятностей

```python
# Вероятность события при множественных попытках
def simulate_probability(prob, trials=10000):
    successes = sum(1 for _ in range(trials) if random.random() < prob)
    return successes / trials

# Вероятность выпадения 6 на кубике
simulate_probability(1/6, 10000)  # → ~0.167

# Парадокс дней рождения (23 человека)
def birthday_paradox_simulation(people=23, trials=10000):
    matches = 0
    for _ in range(trials):
        birthdays = [random.randint(1, 365) for _ in range(people)]
        if len(birthdays) != len(set(birthdays)):
            matches += 1
    return matches / trials

birthday_paradox_simulation()  # → ~0.507 (примерно 50%!)
```

### Генерация тестовых данных

```python
# Случайные имена
first_names = ["Иван", "Мария", "Пётр", "Анна", "Алексей"]
last_names = ["Иванов", "Петров", "Сидоров", "Смирнов", "Козлов"]

def random_person():
    return {
        "имя": random.choice(first_names),
        "фамилия": random.choice(last_names),
        "возраст": random.randint(18, 65),
        "зарплата": random.randint(30000, 150000)
    }

# Генерация 100 человек
people = [random_person() for _ in range(100)]
```

### Случайное решение

```python
def make_decision(options, question="Что выбрать?"):
    print(question)
    print(f"Варианты: {', '.join(options)}")
    choice = random.choice(options)
    print(f"Решение: {choice}")
    return choice

make_decision(["пицца", "суши", "бургер"], "Что заказать на ужин?")
```

### Игра: угадай число

```python
def guessing_game():
    number = random.randint(1, 100)
    attempts = 0
    
    print("Я загадал число от 1 до 100. Угадайте его!")
    
    while True:
        guess = int(input("Ваша догадка: "))
        attempts += 1
        
        if guess < number:
            print("Больше!")
        elif guess > number:
            print("Меньше!")
        else:
            print(f"Правильно! Попыток: {attempts}")
            break

# guessing_game()  # раскомментируйте для запуска
```

### Случайное распределение задач

```python
def assign_tasks(tasks, people):
    # Проверка, что задач не больше чем людей * разумное количество
    assignments = {person: [] for person in people}
    
    for task in tasks:
        person = random.choice(people)
        assignments[person].append(task)
    
    return assignments

tasks = ["Задача 1", "Задача 2", "Задача 3", "Задача 4", "Задача 5"]
people = ["Алиса", "Боб", "Карл"]
assignments = assign_tasks(tasks, people)

for person, tasks in assignments.items():
    print(f"{person}: {tasks}")
```

## 🔐 Криптографически безопасные случайные числа

```python
import secrets

# Для паролей, токенов, ключей используйте secrets!
# secrets использует источники случайности ОС

# Случайное целое
secrets.randbelow(100)          # → 0-99
secrets.randbits(8)             # → 8-битное число

# Случайный элемент
secrets.choice([1, 2, 3, 4, 5])

# Безопасный токен (hex)
secrets.token_hex(16)           # → "3e5f9c8a7b2d1e4f..."

# Безопасный токен (URL-safe)
secrets.token_urlsafe(16)       # → "xKmPqWjHzNaB3xK9"

# Безопасный пароль
import string
def secure_password(length=16):
    chars = string.ascii_letters + string.digits + string.punctuation
    return ''.join(secrets.choice(chars) for _ in range(length))
```

## ⚠️ Важные замечания

```python
# ❌ random НЕ подходит для криптографии
# Плохо для: паролей, токенов, ключей шифрования
token = ''.join(random.choice(string.ascii_letters) for _ in range(16))

# ✅ Используйте secrets для безопасности
import secrets
token = secrets.token_urlsafe(16)

# ❌ shuffle изменяет оригинальный список
original = [1, 2, 3, 4, 5]
random.shuffle(original)        # → original изменён!

# ✅ Используйте копию или sample
shuffled = random.sample(original, k=len(original))

# ❌ Нельзя sample больше элементов, чем есть
# random.sample([1, 2, 3], k=5)  # ValueError!

# ✅ Используйте choices для повторений
random.choices([1, 2, 3], k=5)  # → [2, 1, 3, 3, 1]

# ⚠️ Seed делает результаты предсказуемыми
random.seed(42)
print(random.random())          # → всегда одно и то же
# Используйте для тестирования, НЕ для безопасности!
```

## 🎯 Когда использовать что

**random.randint(a, b)** - целые числа, включая оба конца

- Бросок кубика, случайный индекс, случайный год

**random.randrange(a, b)** - целые числа, исключая b

- Когда нужна совместимость с range()

**random.random()** - float от 0.0 до 1.0

- Вероятности, моделирование

**random.uniform(a, b)** - float в диапазоне

- Температура, цены, координаты

**random.choice(seq)** - один элемент

- Выбор из списка вариантов

**random.choices(seq, k=n)** - несколько с повторениями

- Моделирование с возвратом

**random.sample(seq, k=n)** - несколько без повторений

- Лотерея, выборка данных

**random.shuffle(list)** - перемешать на месте

- Перемешивание колоды, случайный порядок

## 🔗 Связанные темы

- [[01 — 📋 Cписки ( list ) 🔹]]
- [[01 — 🔢 Math]]
- [[Целые числа (int) 🔹]]
