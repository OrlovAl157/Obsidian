---
tags:
  - python
  - тема/типы_данных
  - коллекции
---

## Пример для быстрой справки

```python
from collections import Counter

# Подсчет символов в строке
letters = Counter('mississippi')

print(letters)
# Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})

print(letters['s'])  # 4
print(letters['z'])  # 0 (нет KeyError!)
```

Все примеры в таблице ниже основаны на этом коде ⬇️

## Быстрая справка

|Операция|Синтаксис|Описание|
|---|---|---|
|**Импорт**|`from collections import Counter`|Подключение класса|
|**Из строки**|`Counter('hello')`|Подсчет символов|
|**Из списка**|`Counter([1, 2, 2, 3, 3, 3])`|Подсчет элементов|
|**Из словаря**|`Counter({'a': 3, 'b': 2})`|Явное указание количества|
|**Из kwargs**|`Counter(a=3, b=2)`|Именованные аргументы|
|**Доступ к элементу**|`c['key']`|Возвращает 0 если ключа нет|
|**Обновление**|`c.update('abc')`|Добавляет (не заменяет!)|
|**Самые частые**|`c.most_common(n)`|n самых частых элементов|
|**Все элементы**|`c.elements()`|Итератор с повторениями|
|**Сумма всех значений**|`c.total()`|Python 3.10+|
|**Вычитание**|`c.subtract(other)`|Уменьшает счетчики|
|**Сложение**|`c1 + c2`|Объединение счетчиков|
|**Вычитание**|`c1 - c2`|Разность (только положительные)|
|**Пересечение**|`c1 & c2`|Минимум из значений|
|**Объединение**|`c1 \| c2`|Максимум из значений|

## Что такое Counter?

**Counter** — это подкласс словаря `dict`, специально разработанный для **подсчета хешируемых объектов**. Counter хранит объекты как ключи, а их количество как значения.

### Зачем нужен Counter?

```python
# ❌ Без Counter (длинно)
text = 'mississippi'
counts = {}
for char in text:
    if char not in counts:
        counts[char] = 0
    counts[char] += 1

# ✅ С Counter (просто)
from collections import Counter
counts = Counter('mississippi')
```

**Преимущества:**

- 🚀 Высокооптимизированный (написан на C)
- 📊 Специализирован для подсчета
- 🔑 Нет KeyError — возвращает 0 для отсутствующих ключей
- ➕ Поддерживает математические операции
- 🛠️ Дополнительные методы: `most_common()`, `elements()`, `total()`

## Создание Counter

### Все способы создания

```python
from collections import Counter

# 1. Из строки
c1 = Counter('mississippi')
# Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})

# 2. Из списка
c2 = Counter([1, 2, 2, 3, 3, 3])
# Counter({3: 3, 2: 2, 1: 1})

# 3. Из кортежа
c3 = Counter((1, 1, 2, 3))
# Counter({1: 2, 3: 1, 2: 1})

# 4. Из словаря (явное указание количества)
c4 = Counter({'apple': 5, 'banana': 2})
# Counter({'apple': 5, 'banana': 2})

# 5. Из именованных аргументов
c5 = Counter(apple=5, banana=2)
# Counter({'apple': 5, 'banana': 2})

# 6. Пустой Counter
c6 = Counter()
# Counter()
```

### Особенности Counter

```python
from collections import Counter

# ✅ Ключи должны быть хешируемыми (как в dict)
c1 = Counter(['apple', 'banana', 'apple'])  # OK
c2 = Counter([1, 2, 1, 3])                   # OK

# ❌ Списки не хешируемы
try:
    c3 = Counter([[1, 2], [1, 2]])  # TypeError
except TypeError:
    print("Списки не могут быть ключами")

# ⚠️ Значения могут быть любыми (но должны быть числами для корректной работы)
c4 = Counter(apple=5, orange=-2, banana=0, melon='N/A')
# Работает, но некорректно для математических операций
```

### fromkeys() не поддерживается

```python
from collections import Counter

# ❌ fromkeys() выдает ошибку
try:
    c = Counter.fromkeys('abc', 0)
except NotImplementedError as e:
    print(e)
    # Counter.fromkeys() is undefined. Use Counter(iterable) instead.

# ✅ Правильно
c = Counter('abc')  # Создает Counter с реальным подсчетом
```

## Доступ к элементам

### Безопасный доступ (нет KeyError)

Главная особенность Counter — при обращении к несуществующему ключу возвращается **0**, а не ошибка:

```python
from collections import Counter

letters = Counter('hello')

print(letters['l'])  # 2
print(letters['z'])  # 0 (нет KeyError!)

# При этом ключ НЕ создается
print('z' in letters)  # False
```

### Итерирование

```python
from collections import Counter

letters = Counter('mississippi')

# Перебор ключей
for letter in letters:
    print(letter, '->', letters[letter])

# Перебор items()
for letter, count in letters.items():
    print(f"{letter}: {count}")

# Перебор значений
for count in letters.values():
    print(count)

# Перебор ключей через keys()
for letter in letters.keys():
    print(letter)
```

## Обновление Counter

### Метод `update()` — добавляет, не заменяет!

В отличие от обычного dict, `update()` **суммирует** значения:

```python
from collections import Counter

# dict — заменяет значения
d = {'a': 1, 'b': 2}
d.update({'a': 10})
print(d)  # {'a': 10, 'b': 2} — значение заменилось

# Counter — суммирует значения
c = Counter(a=1, b=2)
c.update(Counter(a=10))
print(c)  # Counter({'a': 11, 'b': 2}) — значение сложилось!
```

### Различные способы обновления

```python
from collections import Counter

sales = Counter(apple=20, orange=5, banana=10)
print(sales)  # Counter({'apple': 20, 'banana': 10, 'orange': 5})

# 1. Из строки/списка/кортежа
sales.update('apple orange')
print(sales)  # Counter({'apple': 21, 'banana': 10, 'orange': 6})

# 2. Из другого Counter
monday_sales = Counter(apple=3, orange=12, banana=7)
sales.update(monday_sales)
print(sales)  # Counter({'orange': 18, 'apple': 24, 'banana': 17})

# 3. Из словаря
tuesday_sales = {'apple': 4, 'tomato': 6}
sales.update(tuesday_sales)
print(sales)  # Counter({'apple': 28, 'orange': 18, 'banana': 17, 'tomato': 6})

# 4. Именованные аргументы
sales.update(apple=2, watermelon=5)
print(sales)  # Counter({'apple': 30, 'orange': 18, 'banana': 17, ...})
```

## Специальные методы Counter

### Метод `most_common(n=None)`

Возвращает список самых частых элементов с их количеством:

```python
from collections import Counter

letters = Counter('mississippi')
numbers = Counter([5, 6, 7, 1, 3, 9, 9, 1, 2, 5, 5, 7, 7, 9])

# Все элементы (отсортированные по убыванию частоты)
print(letters.most_common())
# [('i', 4), ('s', 4), ('p', 2), ('m', 1)]

# Топ 3
print(letters.most_common(3))
# [('i', 4), ('s', 4), ('p', 2)]

# Топ 2
print(numbers.most_common(2))
# [(5, 3), (7, 3)]
```

### Поиск самых редких элементов

```python
from collections import Counter

letters = Counter('mississippi')

# Самый редкий (последний)
print(letters.most_common()[-1])
# ('m', 1)

# Все в обратном порядке (от редких к частым)
print(letters.most_common()[::-1])
# [('m', 1), ('p', 2), ('s', 4), ('i', 4)]

# 2 самых редких
print(letters.most_common()[:-3:-1])
# [('m', 1), ('p', 2)]
```

### Метод `elements()`

Возвращает итератор, где каждый элемент повторяется столько раз, сколько указано в счетчике:

```python
from collections import Counter

letters = Counter(m=1, i=4, s=4, p=2)

# elements() возвращает итератор
print(list(letters.elements()))
# ['m', 'i', 'i', 'i', 'i', 's', 's', 's', 's', 'p', 'p']

# Порядок соответствует порядку первого появления
numbers = Counter([5, 6, 7, 1, 3, 9, 9, 1, 2, 5, 5, 7, 7, 9])
print(list(numbers.elements()))
# [5, 5, 5, 6, 7, 7, 7, 1, 1, 3, 9, 9, 9, 2]
```

**Важно:** Элементы с количеством ≤ 0 игнорируются:

```python
from collections import Counter

letters = Counter(a=3, b=0, c=-5, d=2)

print(list(letters.elements()))
# ['a', 'a', 'a', 'd', 'd'] — b и c отсутствуют
```

### Метод `total()` (Python 3.10+)

Возвращает сумму всех значений (включая отрицательные):

```python
from collections import Counter

letters = Counter(i=4, s=4, a=0, p=2, b=-98, m=1)

print(letters.total())  # -87

# Для Python < 3.10 используйте:
print(sum(letters.values()))  # -87
```

### Метод `subtract()`

Вычитает количества (в отличие от `update()`, которая складывает):

```python
from collections import Counter

counter1 = Counter(i=4, s=40, a=1, p=20, b=98, z=69)
counter2 = Counter(i=2, s=20, a=6, p=12, m=1, z=69)

counter1.subtract(counter2)

print(counter1)
# Counter({'b': 98, 's': 20, 'p': 8, 'i': 2, 'z': 0, 'm': -1, 'a': -5})
```

**Особенности:**

- Значения могут быть **нулевыми** или **отрицательными**
- Изменяет исходный Counter (как `update()`)

```python
from collections import Counter

# Из строки
counter = Counter(i=4, s=40, a=1, p=20, b=98, z=69)
counter.subtract('iisssssapppz')
print(counter)
# Counter({'b': 98, 'z': 68, 's': 35, 'p': 17, 'i': 2, 'a': 0})

# Именованные аргументы
counter = Counter(a=5, b=10)
counter.subtract(a=2, b=3)
print(counter)
# Counter({'b': 7, 'a': 3})
```

## Математические операции

Counter поддерживает математические операции с другими Counter:

### Сложение `+`

Складывает количества, **исключает** нулевые и отрицательные:

```python
from collections import Counter

c1 = Counter(a=3, b=1, c=5)
c2 = Counter(a=1, b=2, d=3)

result = c1 + c2
print(result)
# Counter({'c': 5, 'a': 4, 'd': 3, 'b': 3})
```

### Вычитание `-`

Вычитает количества, **исключает** нулевые и отрицательные:

```python
from collections import Counter

c1 = Counter(a=5, b=3, c=2)
c2 = Counter(a=2, b=1, d=1)

result = c1 - c2
print(result)
# Counter({'a': 3, 'b': 2, 'c': 2})
# 'd' отсутствует (было бы -1)

result = c2 - c1
print(result)
# Counter() — все значения отрицательные, поэтому пусто
```

### Пересечение `&` (минимум)

Возвращает **минимум** из значений по каждому ключу:

```python
from collections import Counter

c1 = Counter(a=5, b=3, c=1)
c2 = Counter(a=2, b=4, c=1, d=10)

result = c1 & c2
print(result)
# Counter({'a': 2, 'b': 3, 'c': 1})
# a: min(5, 2) = 2
# b: min(3, 4) = 3
# c: min(1, 1) = 1
# d отсутствует (только в c2)
```

### Объединение `|` (максимум)

Возвращает **максимум** из значений по каждому ключу:

```python
from collections import Counter

c1 = Counter(a=5, b=3, c=1)
c2 = Counter(a=2, b=4, c=1, d=10)

result = c1 | c2
print(result)
# Counter({'d': 10, 'a': 5, 'b': 4, 'c': 1})
# a: max(5, 2) = 5
# b: max(3, 4) = 4
# c: max(1, 1) = 1
# d: max(0, 10) = 10
```

### Унарные операторы

```python
from collections import Counter

c = Counter(a=5, b=-3, c=0, d=2)

# Унарный + : оставляет только положительные
positive = +c
print(positive)
# Counter({'a': 5, 'd': 2})

# Унарный - : оставляет только отрицательные (инвертирует знак)
negative = -c
print(negative)
# Counter({'b': 3})

# Эквивалентно
print(Counter() + c)  # Counter({'a': 5, 'd': 2})
print(Counter() - c)  # Counter({'b': 3})
```

### Важное отличие операторов от методов

```python
from collections import Counter

c = Counter(a=3)

# ✅ Операторы — только с Counter
result = c + Counter(b=2)  # OK

# ❌ Операторы — НЕ работают с другими типами
try:
    result = c + {'b': 2}  # TypeError
except TypeError:
    print("Операторы работают только с Counter")

# ✅ Методы — работают с любыми итерируемыми
c.update('abc')       # OK
c.update([1, 2, 3])   # OK
c.update({'x': 5})    # OK
```

## Операторы сравнения (Python 3.10+)

С Python 3.10 Counter поддерживает операторы подмножества/надмножества:

```python
from collections import Counter

c1 = Counter(a=3, b=2)
c2 = Counter(a=2, b=1)

print(c1 > c2)   # True (c1 содержит больше каждого элемента)
print(c2 < c1)   # True

c1 = Counter(a=3, b=2)
c2 = Counter(a=3, b=2, c=1)

print(c1 < c2)   # True (c1 — подмножество c2)
print(c2 > c1)   # True

# Отсутствующие элементы считаются имеющими 0
c1 = Counter(a=1, b=2)
c2 = Counter(a=1, b=2, c=0)

print(c1 == c2)  # True (c=0 считается как отсутствующий)
```

## Сравнение Counter

### Равенство `==` и `!=`

```python
from collections import Counter

c1 = Counter('mississippi')
c2 = Counter(m=1, i=4, s=4, p=2)
c3 = Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})

print(c1 == c2)  # True
print(c1 == c3)  # True

# С Python 3.10+ отсутствующие ключи = 0
c4 = Counter(a=3)
c5 = Counter(a=3, b=0)
print(c4 == c5)  # True (Python 3.10+)
```

## Практические примеры

### Пример 1: Анализ текста

```python
from collections import Counter

text = """
Python is a high-level programming language. 
Python is easy to learn. Python is powerful.
"""

# Подсчет слов
words = text.lower().split()
word_count = Counter(words)

# Топ 5 слов
print(word_count.most_common(5))
# [('python', 3), ('is', 3), ('a', 1), ('high-level', 1), ('programming', 1)]

# Сколько раз встречается "python"
print(word_count['python'])  # 3
```

### Пример 2: Анаграммы

```python
from collections import Counter

def are_anagrams(word1, word2):
    """Проверка, являются ли слова анаграммами"""
    return Counter(word1.lower()) == Counter(word2.lower())

print(are_anagrams('listen', 'silent'))  # True
print(are_anagrams('python', 'java'))    # False
```

### Пример 3: Инвентарь магазина

```python
from collections import Counter

# Текущий инвентарь
inventory = Counter(apple=10, banana=5, orange=8)

# Поступление товара
delivery = Counter(apple=5, banana=10, melon=3)
inventory.update(delivery)
print(inventory)
# Counter({'banana': 15, 'apple': 15, 'orange': 8, 'melon': 3})

# Продажа товара
sales = Counter(apple=3, banana=2, orange=1)
inventory.subtract(sales)
print(inventory)
# Counter({'apple': 12, 'banana': 13, 'orange': 7, 'melon': 3})

# Товары с низким запасом (< 10)
low_stock = [item for item, count in inventory.items() if count < 10]
print(low_stock)  # ['orange', 'melon']
```

### Пример 4: Голосование

```python
from collections import Counter

votes = ['Alice', 'Bob', 'Alice', 'Charlie', 'Bob', 'Alice', 'Diana', 'Bob']

vote_count = Counter(votes)

print("Результаты голосования:")
for candidate, count in vote_count.most_common():
    print(f"{candidate}: {count} голосов")

# Победитель
winner = vote_count.most_common(1)[0][0]
print(f"\nПобедитель: {winner}")
```

### Пример 5: Мультимножества

```python
from collections import Counter

# Мультимножество — множество с повторениями
multiset = Counter([1, 1, 2, 3, 3, 3, 4, 4])

print(multiset)
# Counter({3: 3, 1: 2, 4: 2, 2: 1})

# Обычное множество (уникальные элементы)
regular_set = set(multiset.keys())
print(regular_set)
# {1, 2, 3, 4}

# Общее количество элементов (с повторениями)
print(sum(multiset.values()))  # 8

# Уникальных элементов
print(len(multiset))  # 4
```

### Пример 6: Частотный анализ

```python
from collections import Counter
import string

def analyze_text(text):
    """Частотный анализ текста"""
    # Убираем пунктуацию и приводим к нижнему регистру
    text = text.lower()
    text = ''.join(c for c in text if c not in string.punctuation)
    
    # Подсчет символов
    char_count = Counter(text.replace(' ', ''))
    
    # Подсчет слов
    word_count = Counter(text.split())
    
    return {
        'total_chars': sum(char_count.values()),
        'unique_chars': len(char_count),
        'total_words': sum(word_count.values()),
        'unique_words': len(word_count),
        'most_common_char': char_count.most_common(1)[0],
        'most_common_word': word_count.most_common(1)[0]
    }

text = "Hello world! Hello Python. Python is amazing."
stats = analyze_text(text)

for key, value in stats.items():
    print(f"{key}: {value}")
```

### Пример 7: Объединение результатов

```python
from collections import Counter

# Результаты экзаменов разных групп
group1 = Counter({'A': 5, 'B': 8, 'C': 3, 'D': 1})
group2 = Counter({'A': 3, 'B': 6, 'C': 5, 'D': 2})
group3 = Counter({'A': 4, 'B': 7, 'C': 4, 'E': 1})

# Общие результаты
total = group1 + group2 + group3

print("Общее распределение оценок:")
for grade in sorted(total.keys()):
    print(f"{grade}: {total[grade]} студентов")

# Средний результат (условно A=5, B=4, C=3, D=2, E=1)
grade_values = {'A': 5, 'B': 4, 'C': 3, 'D': 2, 'E': 1}
total_points = sum(total[grade] * grade_values[grade] for grade in total)
total_students = sum(total.values())
average = total_points / total_students
print(f"\nСредний балл: {average:.2f}")
```

## Динамические атрибуты

Как и OrderedDict, Counter имеет атрибут `__dict__`:

```python
from collections import Counter

counter = Counter(green=10, red=25, blue=5)

# Добавляем пользовательские методы
counter.min_value = lambda: min(counter.values())
counter.max_value = lambda: max(counter.values())

print(counter.min_value())  # 5
print(counter.max_value())  # 25

# Через __dict__
counter.__dict__['average'] = lambda: sum(counter.values()) / len(counter)
print(counter.average())  # 13.333...
```

## Когда использовать Counter?

### ✅ Используйте Counter когда:

1. **Нужно подсчитать частоту элементов**
    
    ```python
    Counter('mississippi')
    ```
    
2. **Работа с мультимножествами**
    
    ```python
    multiset = Counter([1, 1, 2, 3, 3, 3])
    ```
    
3. **Нужны топ-N элементов**
    
    ```python
    counter.most_common(5)
    ```
    
4. **Математические операции с частотами**
    
    ```python
    total = counter1 + counter2
    ```
    
5. **Анализ данных и статистика**
    
    - Частотный анализ текста
    - Подсчет голосов
    - Инвентаризация

### ❌ Не используйте Counter когда:

1. **Не нужен подсчет**
    
    ```python
    # Используйте обычный dict
    ```
    
2. **Нужна обычная семантика update()**
    
    ```python
    # dict.update() заменяет, Counter.update() складывает
    ```
    
3. **Значения не числовые**
    
    ```python
    # Counter предназначен для чисел
    ```
    

## Производительность

Counter оптимизирован на C и работает очень быстро:

```python
import timeit
from collections import Counter

# Counter vs dict с get()
counter_time = timeit.timeit(
    "Counter('abcdefghij' * 100)",
    "from collections import Counter",
    number=10000
)

dict_time = timeit.timeit("""
d = {}
for c in 'abcdefghij' * 100:
    d[c] = d.get(c, 0) + 1
""", number=10000)

print(f"Counter: {counter_time:.4f}s")
print(f"dict.get(): {dict_time:.4f}s")
print(f"Counter быстрее в {dict_time/counter_time:.2f}x раз")
```

## Распространенные ошибки

### ❌ Ошибка 1: Путаница update() и subtract()

```python
from collections import Counter

# Неправильно — хотели вычесть
c = Counter(a=10, b=5)
c.update(Counter(a=3))  # Складывает!
print(c)  # Counter({'a': 13, 'b': 5})

# Правильно
c = Counter(a=10, b=5)
c.subtract(Counter(a=3))
print(c)  # Counter({'a': 7, 'b': 5})
```

### ❌ Ошибка 2: Использование операторов с не-Counter

```python
from collections import Counter

c = Counter(a=5)

# Неправильно
try:
    result = c + {'b': 2}  # TypeError
except TypeError:
    print("Операторы работают только с Counter")

# Правильно
c.update({'b': 2})  # Методы работают с любыми итерируемыми
```

### ❌ Ошибка 3: Ожидание KeyError

```python
from collections import Counter

c = Counter(a=5, b=3)

# Counter возвращает 0, а не KeyError
print(c['z'])  # 0 (не ошибка!)

# Если нужна проверка существования
if 'z' in c:
    print(c['z'])
else:
    print("Ключ не найден")
```

### ❌ Ошибка 4: Нечисловые значения

```python
from collections import Counter

# Работает, но некорректно
c = Counter(a='hello', b=5)
c.update(Counter(a='world'))  # Конкатенация строк!
print(c)  # Counter({'b': 5, 'a': 'helloworld'})

# Правильно — только числа
c = Counter(a=5, b=10)
c.update(Counter(a=3))
print(c)  # Counter({'b': 10, 'a': 8})
```

## Полезные ссылки

- [Официальная документация Counter](https://docs.python.org/3/library/collections.html#collections.Counter)
- [PEP 3102 — Keyword-Only Arguments](https://www.python.org/dev/peps/pep-3102/)
- [Исходный код Counter](https://github.com/python/cpython/blob/main/Lib/collections/__init__.py)