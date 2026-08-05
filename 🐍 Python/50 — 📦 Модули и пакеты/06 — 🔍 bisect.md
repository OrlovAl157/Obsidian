---
tags: [python, модули, bisect, алгоритмы, бинарный-поиск]
difficulty: intermediate
---

# 🔍 Модуль bisect — памятка

> Модуль `bisect` реализует бинарный поиск и вставку в **отсортированный** список. Все операции выполняются за O(log n) — без необходимости сортировать список заново после каждой вставки.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🔍 bisect — поиск позиции|bisect — поиск позиции]]
- [[#➕ insort — вставка с сохранением порядка|insort — вставка]]
- [[#🔵 left vs right — разница|left vs right]]
- [[#🎯 Практические паттерны|Практические паттерны]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Функция | Что делает | Возвращает |
|---|---|---|
| `bisect_left(a, x)` | Позиция для вставки x слева от равных | `int` |
| `bisect_right(a, x)` | Позиция для вставки x справа от равных | `int` |
| `bisect(a, x)` | Псевдоним `bisect_right` | `int` |
| `insort_left(a, x)` | Вставить x слева от равных | `None` |
| `insort_right(a, x)` | Вставить x справа от равных | `None` |
| `insort(a, x)` | Псевдоним `insort_right` | `None` |

Все функции принимают необязательные параметры `lo` и `hi` для ограничения диапазона поиска.

---

## 📊 Общая схема

```
Список:  [1, 3, 3, 5, 7]
Индекс:   0  1  2  3  4

bisect_left(a, 3)   → 1   (перед первой тройкой)
bisect_right(a, 3)  → 3   (после последней тройки)
bisect_left(a, 4)   → 3   (между 3 и 5)
bisect_right(a, 4)  → 3   (то же самое — 4 нет в списке)

                ↓ left    ↓ right
[1,   3,   3,   3,   5,   7]
           ↑              ↑
      bisect_left(3)  bisect_right(3)
```

---

## 🔍 bisect — поиск позиции

**Назначение:** найти позицию куда нужно вставить элемент чтобы список остался отсортированным.

```python
import bisect

a = [1, 3, 3, 5, 7]

# bisect_right (он же bisect) — позиция ПОСЛЕ равных элементов
bisect.bisect_right(a, 3)   # → 3
bisect.bisect(a, 3)          # → 3  (то же самое)

# bisect_left — позиция ДО равных элементов
bisect.bisect_left(a, 3)    # → 1

# Элемент которого нет — оба дают одно место
bisect.bisect_left(a, 4)    # → 3
bisect.bisect_right(a, 4)   # → 3

# Края списка
bisect.bisect_left(a, 0)    # → 0  (меньше всех)
bisect.bisect_right(a, 9)   # → 5  (больше всех)
```

**Ограничение диапазона через `lo` и `hi`:**

```python
a = [1, 3, 3, 5, 7]

bisect.bisect_left(a, 3, lo=2)        # → 2  поиск с индекса 2
bisect.bisect_right(a, 3, lo=0, hi=2) # → 2  поиск в a[0:2]
```

**Проверка наличия элемента:**

```python
def contains(a, x):
    i = bisect.bisect_left(a, x)
    return i < len(a) and a[i] == x

a = [1, 3, 5, 7]
contains(a, 3)   # → True
contains(a, 4)   # → False
```

---

## ➕ insort — вставка с сохранением порядка

**Назначение:** вставить элемент в отсортированный список, сохранив порядок. Быстрее чем `append()` + `sort()`.

```python
import bisect

a = [1, 3, 5, 7]

# insort_right (он же insort) — вставить справа от равных
bisect.insort_right(a, 4)
# → [1, 3, 4, 5, 7]

bisect.insort(a, 4)          # то же самое

# insort_left — вставить слева от равных
a = [1, 3, 3, 5, 7]
bisect.insort_left(a, 3)
# → [1, 3, 3, 3, 5, 7]  ← новая тройка встала перед старыми

bisect.insort_right(a, 3)
# → [1, 3, 3, 3, 3, 5, 7]  ← новая тройка встала после старых
```

**Сложность:**

```python
# insort = bisect O(log n) + вставка O(n)
# Итого: O(n) — из-за сдвига элементов при вставке
# Но лучше чем append() + sort() = O(n log n)
```

---

## 🔵 left vs right — разница

Разница видна только когда в списке есть **дубликаты**:

```python
import bisect

a = [1, 3, 3, 3, 5]
#        ↑  ↑  ↑
#    индексы 1, 2, 3

bisect.bisect_left(a, 3)    # → 1  (перед первой тройкой)
bisect.bisect_right(a, 3)   # → 4  (после последней тройки)

bisect.insort_left(a, 3)
# → [1, 3, 3, 3, 3, 5]  новая тройка на позиции 1 (слева)

bisect.insort_right(a, 3)
# → [1, 3, 3, 3, 3, 3, 5]  новая тройка на позиции 4 (справа)
```

**Когда это важно:**

| Ситуация | Используй |
|---|---|
| Нужна позиция первого вхождения | `bisect_left` |
| Нужна позиция после последнего вхождения | `bisect_right` |
| Стабильная сортировка (порядок равных сохранён) | `insort_right` |
| Вставить перед равными | `insort_left` |
| Просто вставить (без разницы) | `insort` |

---

## 🎯 Практические паттерны

**Ранжирование оценок:**

```python
import bisect

def grade(score):
    breakpoints = [60, 70, 80, 90]
    grades = ['F', 'D', 'C', 'B', 'A']
    return grades[bisect.bisect(breakpoints, score)]

grade(55)   # → 'F'
grade(70)   # → 'C'
grade(85)   # → 'B'
grade(95)   # → 'A'
```

**Поддержание отсортированного списка в реальном времени:**

```python
import bisect

stream = []
data = [5, 2, 8, 1, 9, 3]

for x in data:
    bisect.insort(stream, x)
    print(stream)

# [5]
# [2, 5]
# [2, 5, 8]
# [1, 2, 5, 8]
# [1, 2, 5, 8, 9]
# [1, 2, 3, 5, 8, 9]
```

**Поиск ближайшего элемента:**

```python
import bisect

def closest(a, x):
    """Найти ближайший к x элемент в отсортированном списке"""
    i = bisect.bisect_left(a, x)
    if i == 0:
        return a[0]
    if i == len(a):
        return a[-1]
    before = a[i - 1]
    after = a[i]
    return before if abs(before - x) <= abs(after - x) else after

a = [1, 3, 5, 7, 9]
closest(a, 4)   # → 3  (расстояние до 3 и до 5 одинаково, берём меньший)
closest(a, 6)   # → 5
closest(a, 0)   # → 1
closest(a, 10)  # → 9
```

**Подсчёт элементов в диапазоне:**

```python
import bisect

def count_range(a, lo, hi):
    """Количество элементов в диапазоне [lo, hi]"""
    return bisect.bisect_right(a, hi) - bisect.bisect_left(a, lo)

a = [1, 2, 3, 3, 4, 5, 6]
count_range(a, 3, 5)   # → 4  (3, 3, 4, 5)
count_range(a, 1, 6)   # → 7  (все)
```

---

## ⚡ Быстрые примеры

```python
import bisect

a = [1, 3, 5, 7, 9]

# Найти позицию вставки
bisect.bisect(a, 4)          # → 2
bisect.bisect_left(a, 5)     # → 2  (перед 5)
bisect.bisect_right(a, 5)    # → 3  (после 5)

# Вставить элемент
bisect.insort(a, 4)          # → [1, 3, 4, 5, 7, 9]
bisect.insort(a, 6)          # → [1, 3, 4, 5, 6, 7, 9]

# Проверить есть ли элемент
i = bisect.bisect_left(a, 5)
exists = i < len(a) and a[i] == 5   # → True

# Найти все элементы меньше x
a = [1, 2, 3, 4, 5]
a[:bisect.bisect_left(a, 3)]         # → [1, 2]

# Найти все элементы больше x
a[bisect.bisect_right(a, 3):]        # → [4, 5]
```

---

## 💡 Практические замечания

- `bisect` работает **только с отсортированными списками** — на неотсортированных результат непредсказуем
- Поиск позиции O(log n), но вставка `insort` всё равно O(n) из-за сдвига элементов
- Для очень частых вставок с сохранением порядка — рассмотри `SortedList` из пакета `sortedcontainers`
- `bisect` и `insort` — псевдонимы `bisect_right` и `insort_right`
- Работает с любыми сравниваемыми объектами, не только с числами

---

## ⚠️ Частые ошибки

**❌ Применил к несортированному списку:**
```python
a = [3, 1, 5, 2]
bisect.bisect(a, 3)    # ❌ результат непредсказуем!
a.sort()
bisect.bisect(a, 3)    # ✅
```

**❌ Перепутал bisect и index():**
```python
a = [1, 3, 5, 7]
bisect.bisect_left(a, 3)    # → 1  (позиция)
a.index(3)                   # → 1  (тоже позиция, но O(n) и KeyError если нет)

# bisect_left быстрее O(log n) но не проверяет наличие!
i = bisect.bisect_left(a, 4)
a[i]                         # → 5  ← не 4! элемента 4 нет в списке
```

**❌ Ожидал что insort вернёт список:**
```python
result = bisect.insort(a, 4)  # ❌ result = None!
bisect.insort(a, 4)           # ✅ изменяет a на месте
```

**❌ Забыл что insort изменяет список на месте:**
```python
original = [1, 3, 5]
bisect.insort(original, 2)    # original теперь [1, 2, 3, 5] ⚠️
# Если нужна копия:
import copy
new = copy.copy(original)
bisect.insort(new, 4)
```

---

## ✅ Главные правила

✅ Список должен быть **отсортирован** — иначе результат бессмысленный  
✅ `bisect` = `bisect_right`, `insort` = `insort_right` — справа от равных  
✅ `bisect_left` — когда нужна позиция **до** первого совпадения  
✅ `bisect_right` — когда нужна позиция **после** последнего совпадения  
✅ Поиск O(log n), вставка `insort` O(n) — из-за сдвига элементов  
✅ `insort` изменяет список **на месте**, возвращает `None`  
✅ Для проверки наличия — `bisect_left` + проверка `a[i] == x`  

---

## 🔗 Связанные темы

- [[02 — 🎲 Random]]
- [[04 — 🧰 functools]]
- [[Все про Хеш-функции]]
- [[02 — 🎯 sorted()]]

---

#python/модули #bisect #бинарный-поиск #алгоритмы
