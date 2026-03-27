---

tags:

- python
- тема/файлы
- тема/протоколы
- статус/готово

---

# 🔒 Оператор `with` — контекстный менеджер

## 📌 Коротко

> `with` гарантирует освобождение ресурса **всегда** — даже если произошла ошибка. Это синтаксический сахар над `try-finally`. Объект должен реализовать протокол `__enter__` / `__exit__`.

---

## 🚀 Быстрая справка

|Задача|Код|
|---|---|
|Открыть файл|`with open('f.txt') as f:`|
|Без переменной|`with open('f.txt'):`|
|Несколько файлов|`with open('a') as a, open('b') as b:`|
|Объект заранее|`f = open('f.txt')` → `with f:`|
|Проверить закрыт?|`f.closed` → `True/False`|
|Режим чтения?|`f.readable()` / `f.writable()`|
|Подавить ошибку|`__exit__` → `return True`|
|Пробросить ошибку|`__exit__` → `return False`|
|С обработкой ошибок|`try:` → `with open(...):` → `except:`|

---

## 📖 Зачем нужен `with`

### Проблема — файл может не закрыться

```python
# ❌ Опасно:
f = open('file.txt', 'w')
f.write('hello')   # если здесь ошибка → f.close() не вызовется!
f.close()
```

### Решение 1 — try-finally (громоздко)

```python
f = open('file.txt', 'w')
try:
    f.write('hello')
finally:
    f.close()   # вызовется всегда!
```

### Решение 2 — `with` (лаконично)

```python
with open('file.txt', 'w') as f:
    f.write('hello')
# f.close() вызывается автоматически здесь — даже при ошибке!
```

> 💡 `with` — это тот же `try-finally` под капотом, только короче и читаемее.

---

## 🔍 Цепочка вызовов — что происходит внутри

```
with open('file.txt', 'w') as f:
  │
  ▼
Шаг 1: open('file.txt', 'w')
        → создаётся объект файла (TextIOWrapper)

Шаг 2: файл.__enter__()
        → открывает файл
        → возвращает self

Шаг 3: f = результат __enter__
        → f теперь ссылается на объект файла

Шаг 4: выполняется тело блока

Шаг 5: файл.__exit__(exc_type, exc_val, exc_tb)
        → закрывает файл — всегда!
        → если ошибки нет: все три аргумента = None
        → если ошибка есть: аргументы содержат информацию об ошибке
```

---

## 🔧 Протокол контекстного менеджера

Два магических метода:

|Метод|Когда вызывается|Что делает|
|---|---|---|
|`__enter__`|при входе в блок `with`|инициализирует ресурс, возвращает объект для `as`|
|`__exit__`|при выходе из блока|освобождает ресурс, обрабатывает ошибки|

### `__exit__` — три аргумента

```python
def __exit__(self, exc_type, exc_val, exc_tb):
    self.close()
    return False   # не подавляем ошибку
```

- `exc_type` — тип исключения (`None` если ошибки не было)
- `exc_val` — само исключение (`None` если ошибки не было)
- `exc_tb` — traceback (`None` если ошибки не было)

### Подавление ошибок

```python
def __exit__(self, exc_type, exc_val, exc_tb):
    self.close()
    return True    # ← ошибка подавлена, программа продолжится
    return False   # ← ошибка летит дальше, программа упадёт
```

```python
# Пример — подавляем только ZeroDivisionError:
def __exit__(self, exc_type, exc_val, exc_tb):
    self.close()
    if exc_type == ZeroDivisionError:
        return True   # игнорируем
    return False      # остальные ошибки — пробрасываем
```

---

## ⚠️ Без ошибки vs с ошибкой

### Без ошибки

```
__enter__() → тело блока → __exit__(None, None, None)
```

### С ошибкой

```
__enter__() → тело блока → ошибка!
→ __exit__(ZeroDivisionError, 'division by zero', <tb>)
→ return False → ошибка летит дальше
→ return True  → ошибка подавлена
```

---

## 📋 Синтаксис

```python
# Базовый:
with open('file.txt') as f:
    ...

# Без as (если объект не нужен):
with open('file.txt'):
    ...

# Объект создан заранее:
f = open('file.txt')
with f:
    f.write('hello')

# Несколько объектов:
with open('in.txt') as fin, open('out.txt', 'w') as fout:
    fout.write(fin.read())

# Python 3.10+ — со скобками:
with (
    open('in.txt') as fin,
    open('out.txt', 'w') as fout
):
    fout.write(fin.read())

# with + try-except (для обработки ошибок открытия):
try:
    with open('file.txt') as f:
        print(f.read())
except FileNotFoundError as e:
    print(f'Файл не найден: {e}')
```

---

## 💡 Полезные мелочи

```python
# Проверить закрыт ли файл:
with open('file.txt') as f:
    print(f.closed)   # → False
print(f.closed)       # → True

# Проверить режим файла:
with open('file.txt', 'w') as f:
    print(f.readable(), f.writable())  # → False True

with open('file.txt', 'r') as f:
    print(f.readable(), f.writable())  # → True False
```

> ⚠️ Количество открытых файлов в ОС ограничено! На Windows ~10 000. Всегда закрывай файлы через `with`.

---

## 🆚 `with` vs `try-finally`

||`with`|`try-finally`|
|---|---|---|
|Краткость|✅ короче|❌ длиннее|
|Читаемость|✅ лучше|❌ шаблонный код|
|Универсальность|❌ нужен протокол|✅ любые объекты|

---

## 🔗 Связанные темы

- [[Протокол контекстного менеджера **enter** **exit**]]
- [[Работа с файлами]]
- [[Исключения — try except finally]]

## ❓ Вопросы / Непонятное

- ...