# 🔍 Памятка: Протокол `in` — `__contains__`, `__iter__`, `__getitem__`

---

## 📌 Суть в одном абзаце

Оператор `x in y` ищет элемент `x` внутри `y`. Python делает это **в три попытки, по порядку**: сначала пробует `y.__contains__(x)`, если его нет — итерируется через `y.__iter__()`, а если и его нет — перебирает `y[0]`, `y[1]`, ... через `y.__getitem__()` до `IndexError`. `not in` — тот же оператор, только с инверсией результата.

```python
x in y       # True/False
x not in y   # то же самое, инвертировано
```

---

## 🎯 Порядок вызовов — жёсткий и всегда один

```
                x in y
                   │
                   ▼
        ┌──────────────────────┐
        │ Есть ли у type(y)    │
        │ метод __contains__?  │
        └──────────┬───────────┘
                   │
        ┌──────────┴───────────┐
        │ да                   │ нет
        ▼                      ▼
  y.__contains__(x)   ┌──────────────────┐
  → bool              │ Есть __iter__ ?  │
                      └────────┬─────────┘
                               │
                    ┌──────────┴──────────┐
                    │ да                  │ нет
                    ▼                     ▼
             for item in y:        ┌──────────────────┐
               if item == x:       │ Есть __getitem__?│
                 return True       └────────┬─────────┘
             return False                   │
                                 ┌──────────┴──────────┐
                                 │ да                  │ нет
                                 ▼                     ▼
                          y[0], y[1], ...        TypeError:
                          до IndexError       "not iterable"
```

**Ключевое правило:** если `__contains__` есть — остальные два **не тронуты вообще**.

---

## 🔧 Что именно происходит в каждом методе

### 1. `__contains__(self, item)` — быстрый путь

Python просто вызывает ваш метод и приводит результат к `bool`:

```python
result = type(y).__contains__(y, x)
return bool(result)
```

- Метод ищется **у типа**, не у экземпляра.
- Может вернуть что угодно — приведётся к `True`/`False`.
- Возвращает готовый ответ, никакой итерации.

```python
class A:
    def __contains__(self, x):
        print("contains")
        return x == 42

42 in A()   # → contains → True
```

**Что делает `list.__contains__` внутри (C-код):**
```python
for obj in lst:
    if obj is item or obj == item:   # сначала is, потом ==
        return True
return False
```

**Что делает `dict.__contains__`:**
```python
h = hash(key)
bucket = table[h % size]
for entry in bucket:
    if entry.hash == h and (entry.key is key or entry.key == key):
        return True
return False
```
O(1) в среднем. Проверяются **ключи**, не значения.

**Что делает `str.__contains__`:** поиск **подстроки** алгоритмом Two-Way (Crochemore–Perrin), O(n).

---

### 2. `__iter__(self)` — fallback, обычный цикл `for`

Сюда идём, только если `__contains__` **не определён**. `in` превращается в перебор:

```python
it = type(y).__iter__(y)
while True:
    try:
        item = next(it)
    except StopIteration:
        return False
    if item is x or item == x:
        return True
```

То есть `x in y` эквивалентно:
```python
for item in y:
    if item == x:
        return True
return False
```

**Что должен вернуть `__iter__`:** итератор — объект с методом `__next__`.

```python
class Bad:
    def __iter__(self):
        return [1, 2, 3]   # ❌ список — не итератор

3 in Bad()
# TypeError: iter() returned non-iterator of type 'list'
```

**Важные следствия:**

1. **Итератор одноразовый** — после `in` он «съеден»:
```python
g = (i for i in range(5))
3 in g   # True
3 in g   # False — генератор исчерпан
```

2. **Сравнение через `==`** с предварительной проверкой `is`.

3. **`dict.__iter__` итерирует по ключам**, поэтому `1 in {'a': 1}` → `False`.

---

### 3. `__getitem__(self, i)` — последний fallback (legacy)

Сюда идём, если **нет ни `__contains__`, ни `__iter__`**. Python «дёргает» элементы по индексу:

```python
i = 0
while True:
    try:
        item = y[i]              # ← вызывается __getitem__(i)
    except IndexError:
        return False             # конец последовательности
    if item is x or item == x:
        return True
    i += 1
```

Python **не знает заранее** длину — просто просит `y[0]`, `y[1]`, `y[2]`, ... пока не получит `IndexError`.

```python
class Get:
    def __getitem__(self, i):
        print(f"просят {i}")
        if i >= 3:
            raise IndexError
        return i

2 in Get()
# просят 0 → 0
# просят 1 → 1
# просят 2 → 2 == 2 → True
```

**Опасность:** если `__getitem__` никогда не бросает `IndexError` — **бесконечный цикл**:
```python
class Infinite:
    def __getitem__(self, i):
        return i     # 🔥 никогда не IndexError

5 in Infinite()      # зависнет
```

---

## 📊 Сводная таблица: что возвращает каждый метод

| Метод | Что возвращает | Как `in` использует |
|---|---|---|
| `__contains__` | `True`/`False` (готовый ответ) | берёт как есть, приводит к bool |
| `__iter__` | итератор (объект с `__next__`) | крутит `next()`, сравнивает `== x` |
| `__getitem__` | **один элемент** по индексу | вызывает `y[0]`, `y[1]`, ..., ждёт `IndexError` |

---

## 🧩 Кто что использует на практике

| Тип справа от `in` | Что вызовется | Сложность |
|---|---|---|
| `list`, `tuple` | `__contains__` | O(n) линейный проход |
| `str` | `__contains__` | O(n) поиск подстроки |
| `dict`, `set` | `__contains__` | O(1) по хешу |
| `range` | `__contains__` | O(1) математика |
| генератор | `__iter__` | O(n), съедает |
| файл | `__iter__` | O(n) |
| класс без `__contains__` | `__iter__` | fallback |
| класс без обоих | `__getitem__` | старый протокол |
| `int`, `float`, `None` | — | `TypeError: not iterable` |

---

## 🔬 Байткод: `in` и `not in` — один опкод

```python
import dis
dis.dis("x in y")
```
```
LOAD_NAME  x
LOAD_NAME  y
CONTAINS_OP  0     ← 0 = "in"
RETURN_VALUE
```

```python
dis.dis("x not in y")
```
```
LOAD_NAME  x
LOAD_NAME  y
CONTAINS_OP  1     ← 1 = "not in" (инверсия)
RETURN_VALUE
```

Вся логика трёх веток сидит внутри C-функции `PySequence_Contains`.

---

## 🧠 Мини-реализация на Python

Как бы выглядел `in` изнутри:

```python
def contains(obj, item):
    # 1. __contains__
    method = type(obj).__contains__
    if method is not None:
        return bool(method(obj, item))

    # 2. __iter__
    if hasattr(type(obj), '__iter__'):
        for x in obj:
            if x is item or x == item:
                return True
        return False

    # 3. __getitem__
    if hasattr(type(obj), '__getitem__'):
        i = 0
        while True:
            try:
                x = obj[i]
            except IndexError:
                return False
            if x is item or x == item:
                return True
            i += 1

    raise TypeError(f"argument of type '{type(obj).__name__}' is not iterable")
```

---

## ⚠️ Частые ошибки и подводные камни

1. **`in` для `dict` проверяет ключи, не значения.**
```python
1 in {'a': 1}    # False
'a' in {'a': 1}  # True
```

2. **`in` для строк ищет подстроку, не символ.**
```python
'ab' in 'abc'    # True
```

3. **Генератор съедается после `in`.**
```python
g = (i for i in range(5))
2 in g   # True
2 in g   # False
```

4. **`__getitem__` без `IndexError` → вечный цикл.**

5. **`x in y` не использует хеш напрямую.**  
   Хеш работает внутри `dict.__contains__` / `set.__contains__`, а не в самом протоколе `in`.

6. **Сравнение идёт через `==`, но сначала проверяется `is`.**  
   Это оптимизация `PyObject_RichCompareBool`.

---

## 💻 Шпаргалка: три класса для демонстрации

```python
# 1. Через __contains__
class Show:
    def __contains__(self, item):
        print(f"__contains__({item!r})")
        return item == 42

42 in Show()   # → __contains__(42) → True


# 2. Через __iter__
class Iter:
    def __iter__(self):
        print("__iter__ вызван")
        return iter([1, 2, 3])

2 in Iter()    # → __iter__ вызван → True


# 3. Через __getitem__
class Get:
    def __getitem__(self, i):
        print(f"__getitem__({i})")
        if i >= 3:
            raise IndexError
        return i

2 in Get()
# → __getitem__(0), __getitem__(1), __getitem__(2), __getitem__(3) → True
```

Запустите — увидите порядок своими глазами.

---

## 🔗 Связанные темы

- `📞 Вызываемые объекты и __call__` — параллельный протокол: `()` ищет `__call__`
- `00 Магические методы` — общий список dunder-методов
- `03 — 📦 Атрибуты объектов и классов` — почему магия ищется у типа

---

## 🎴 Одна картинка на память

```
x in y
  │
  ├─ __contains__?  →  готовый True/False
  │
  ├─ __iter__?      →  цикл for + ==
  │
  ├─ __getitem__?   →  y[0], y[1], ... до IndexError
  │
  └─ ничего         →  TypeError: not iterable
```

**Запомните три слова: contains → iter → getitem.**

Если хотите — могу так же оформить памятку по `__eq__` / `__hash__` (почему `in` для set/dict опирается на них) или по `__len__` / `__bool__`.