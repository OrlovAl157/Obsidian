---
tags:
  - python
  - тема/файлы
  - тема/форматы_данных
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
---

# 📊 Работа с CSV-файлами

## 📌 Коротко

> CSV (Comma-Separated Values) — текстовый формат для табличных данных. Модуль `csv` обрабатывает разделители и кавычки автоматически, поддерживает различные разделители (запятая, точка с запятой, табуляция).

---

## 💡 Полезные советы

- **Всегда используйте `csv` модуль** — не пишите `split(',')` вручную!
- **`DictReader` удобнее** — обращение по именам столбцов вместо индексов
- **`newline=''` обязателен** — иначе появятся пустые строки при записи
- **Заголовок обрабатывается автоматически** — `DictReader` и `DictWriter` это делают
- **Передавайте файловый объект** — не текст, а сам файл
- **Разделители разные** — `,` (CSV), `;` (европейский), `\t` (TSV, tab-separated)
- **Кавычки вокруг полей** — нужны только если поле содержит запятую или кавычку

---

## 🚀 Быстрая справка

| Задача | Метод | Сложность | Пример |
|---|---|---|---|
| Читать CSV | `csv.reader(file)` | O(n) | `rows = csv.reader(f)` |
| Читать как словари | `csv.DictReader(file)` | O(n) | `rows = csv.DictReader(f)` |
| Писать CSV | `csv.writer(file)` | O(n) | `writer.writerow(row)` |
| Писать словари | `csv.DictWriter(file, fields)` | O(n) | `writer.writerow(dict)` |
| Писать заголовок | `.writeheader()` | O(m) | `writer.writeheader()` |
| Записать все строки | `.writerows(rows)` | O(n) | `writer.writerows(data)` |

---

## 📖 Теория

### Что такое CSV?

**CSV** (Comma-Separated Values) — текстовый формат для табличных данных:
- Каждая строка файла = строка таблицы
- Поля разделены запятыми (или другим символом-разделителем)
- Поля с запятыми внутри обрамляются кавычками `"поле, с запятой"`
- Простой формат, поддерживается всеми программами

**Пример CSV файла:**
```csv
name,age,city
Alice,25,Moscow
Bob,30,SPb
"Smith, John",28,"New York"
```

**Преимущества CSV:**
- ✅ Читается Excel, Google Sheets, любым редактором
- ✅ Человеческий формат (видно содержимое)
- ✅ Простой синтаксис
- ✅ Стандарт обмена табличными данными
- ✅ Компактный размер

**Варианты разделителей:**
- **CSV** — запятая `,`
- **TSV** — табуляция `\t` (Tab-Separated Values)
- **Европейский CSV** — точка с запятой `;`
- **Другие** — любой символ через параметр `delimiter`

### Структура CSV

CSV состоит из:
1. **Заголовок** (опционально) — имена столбцов
2. **Данные** — строки с значениями

```
ключ1,ключ2,ключ3
значение1,значение2,значение3
значение4,значение5,значение6
```

### Проблема с ручным разбором

```python
# ❌ ПЛОХО: split(',') не обрабатывает кавычки!
line = 'John,"Smith, Jr.",25'
fields = line.split(',')
# Результат: ['John', '"Smith', ' Jr."', '25']  ← НЕПРАВИЛЬНО!

# ✅ ХОРОШО: csv модуль обрабатывает правильно
import csv
fields = next(csv.reader([line]))
# Результат: ['John', 'Smith, Jr.', '25']  ← ПРАВИЛЬНО!
```

---

## 💻 Чтение CSV

### Способ 1: csv.reader() — по индексам

```python
import csv

with open('data.csv', encoding='utf-8') as file:
    reader = csv.reader(file, delimiter=',', quotechar='"')
    for row in reader:
        print(row)  # ['Alice', '25', 'Moscow']
        print(row[0])  # Alice
        print(row[1])  # 25

# Параметры:
# delimiter — разделитель (по умолчанию ',')
# quotechar — символ кавычек (по умолчанию '"')
```

### Способ 2: csv.DictReader() — по названиям (РЕКОМЕНДУЕТСЯ!)

```python
import csv

# Заголовок обрабатывается автоматически
with open('data.csv', encoding='utf-8') as file:
    reader = csv.DictReader(file, delimiter=',')
    for row in reader:
        print(row)  # {'name': 'Alice', 'age': '25', 'city': 'Moscow'}
        print(row['name'])    # → Alice
        print(row['age'])     # → 25
```

**Преимущества DictReader:**
- Более читаемый код
- Не нужно помнить индексы столбцов
- Безопаснее при перестановке столбцов
- Заголовок обрабатывается автоматически

### Получение списка всех строк

```python
import csv

# Способ 1: через list()
with open('data.csv', encoding='utf-8') as file:
    rows = list(csv.DictReader(file))

# Способ 2: через цикл с append()
rows = []
with open('data.csv', encoding='utf-8') as file:
    for row in csv.DictReader(file):
        rows.append(row)
```

### Сортировка и фильтрация

```python
import csv

with open('data.csv', encoding='utf-8') as file:
    rows = list(csv.DictReader(file))

# Фильтр: только люди старше 25
filtered = [row for row in rows if int(row['age']) > 25]

# Сортировка по возрасту
sorted_data = sorted(rows, key=lambda x: int(x['age']))

# Сортировка по имени (обратно)
sorted_by_name = sorted(rows, key=lambda x: x['name'], reverse=True)
```

---

## ✍️ Запись CSV

### Способ 1: csv.writer() — списки

```python
import csv

data = [
    ['Alice', 25, 'Moscow'],
    ['Bob', 30, 'SPb'],
    ['John', 28, 'NYC']
]

with open('output.csv', 'w', encoding='utf-8', newline='') as file:
    writer = csv.writer(file, delimiter=',')
    
    # Написать заголовок
    writer.writerow(['name', 'age', 'city'])
    
    # Написать все строки
    writer.writerows(data)

# ⚠️ ВАЖНО: newline='' — иначе пустые строки между записями!
```

### Способ 2: csv.DictWriter() — словари (УДОБНЕЕ!)

```python
import csv

data = [
    {'name': 'Alice', 'age': 25, 'city': 'Moscow'},
    {'name': 'Bob', 'age': 30, 'city': 'SPb'},
]

fieldnames = ['name', 'age', 'city']

with open('output.csv', 'w', encoding='utf-8', newline='') as file:
    writer = csv.DictWriter(file, fieldnames=fieldnames, delimiter=',')
    
    # ВАЖНО: написать заголовок первым!
    writer.writeheader()
    
    # Написать строки
    writer.writerows(data)
```

### Параметры quoting

| Опция | Описание |
|---|---|
| `csv.QUOTE_ALL` | Все поля в кавычках |
| `csv.QUOTE_MINIMAL` | Только со спецсимволами (по умолчанию) |
| `csv.QUOTE_NONNUMERIC` | Все нечисловые поля |
| `csv.QUOTE_NONE` | Без кавычек (нужен `escapechar`) |

```python
import csv

with open('output.csv', 'w', encoding='utf-8', newline='') as file:
    writer = csv.writer(file, quoting=csv.QUOTE_NONNUMERIC)
    writer.writerow(['Alice', 25, 'Moscow'])
    # Результат: "Alice",25,"Moscow"
```

---

## 💻 Практические примеры

### Чтение, фильтр, запись

```python
import csv

# Читаем
with open('input.csv', encoding='utf-8') as f:
    rows = list(csv.DictReader(f, delimiter=';'))

# Обработка: фильтр и сортировка
filtered = [row for row in rows if int(row['age']) > 25]
filtered.sort(key=lambda x: x['name'])

# Пишем
with open('output.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['name', 'age', 'city'])
    writer.writeheader()
    writer.writerows(filtered)
```

### Преобразование типов при чтении

```python
import csv

with open('data.csv', encoding='utf-8') as file:
    reader = csv.DictReader(file)
    
    data = []
    for row in reader:
        # Преобразуем типы вручную
        data.append({
            'name': row['name'],
            'age': int(row['age']),      # строка → число
            'salary': float(row['salary']),
            'active': row['active'].lower() == 'true'  # строка → булево
        })

# Теперь можно работать с правильными типами
for person in data:
    if person['age'] > 30 and person['salary'] > 50000:
        print(f"{person['name']} — senior")
```

### Добавление новой колонки

```python
import csv

with open('input.csv', encoding='utf-8') as infile:
    reader = csv.DictReader(infile)
    rows = list(reader)

# Добавляем информацию в каждую строку
for row in rows:
    age = int(row['age'])
    if age < 25:
        row['group'] = 'junior'
    else:
        row['group'] = 'senior'

# Пишем с новой колонкой
with open('output.csv', 'w', encoding='utf-8', newline='') as outfile:
    fieldnames = ['name', 'age', 'city', 'group']
    writer = csv.DictWriter(outfile, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerows(rows)
```

### Объединение нескольких CSV

```python
import csv

def merge_csv_files(input_files, output_file):
    """Объединить несколько CSV файлов"""
    with open(output_file, 'w', encoding='utf-8', newline='') as outfile:
        writer = None
        
        for filename in input_files:
            with open(filename, encoding='utf-8') as infile:
                reader = csv.DictReader(infile)
                
                # Инициализируем writer с первого файла
                if writer is None:
                    writer = csv.DictWriter(outfile, fieldnames=reader.fieldnames)
                    writer.writeheader()
                
                writer.writerows(reader)

merge_csv_files(['file1.csv', 'file2.csv', 'file3.csv'], 'merged.csv')
```

---

## ⚠️ Частые ошибки

```python
import csv

# ❌ Ошибка 1: Забыли newline='' при записи
with open('file.csv', 'w', encoding='utf-8') as f:  # ❌
    writer = csv.writer(f)
    writer.writerow(['a', 'b', 'c'])
# Результат: пустые строки между записями!

# ✅ Правильно
with open('file.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['a', 'b', 'c'])

# ❌ Ошибка 2: Передали строку вместо файла
data = file.read()
rows = csv.reader(data)  # ❌ Ошибка!

# ✅ Правильно: передайте файловый объект
with open('file.csv') as file:
    rows = csv.reader(file)
    for row in rows:
        print(row)

# ❌ Ошибка 3: Читаете reader дважды
with open('file.csv') as file:
    reader = csv.reader(file)
    rows1 = list(reader)
    rows2 = list(reader)  # ❌ Пусто! reader уже прочитан

# ✅ Используйте list() один раз
with open('file.csv') as file:
    rows = list(csv.reader(file))

# ❌ Ошибка 4: Забыли writeheader()
with open('output.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['name', 'age'])
    writer.writerows(data)  # ❌ Нет заголовка!

# ✅ Всегда добавляйте заголовок первым
with open('output.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['name', 'age'])
    writer.writeheader()    # ← ВАЖНО!
    writer.writerows(data)

# ❌ Ошибка 5: Не указали кодировку
with open('file.csv') as f:  # На Windows может быть проблема
    reader = csv.reader(f)

# ✅ Всегда указывайте кодировку
with open('file.csv', encoding='utf-8') as f:
    reader = csv.reader(f)
```

---

## 📋 Сравнение reader() vs DictReader()

| Аспект | `csv.reader()` | `csv.DictReader()` |
|---|---|---|
| Доступ к данным | По индексу: `row[0]` | По названию: `row['name']` |
| Заголовок | Обрабатывается как обычная строка | Обрабатывается автоматически |
| Читаемость | ⚠️ Менее читаемо | ✅ Более читаемо |
| Производительность | ✅ Немного быстрее | ⚠️ Немного медленнее |
| Когда использовать | Простые данные | Когда нужны названия столбцов |

---

## 📝 Шпаргалка

```python
import csv

# ЧТЕНИЕ по индексам
with open('file.csv', encoding='utf-8') as f:
    for row in csv.reader(f, delimiter=','):
        print(row[0])

# ЧТЕНИЕ по названиям колонок
with open('file.csv', encoding='utf-8') as f:
    for row in csv.DictReader(f):
        print(row['name'])

# ЗАПИСЬ из списков
with open('file.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['Alice', 25])
    writer.writerows(data)

# ЗАПИСЬ из словарей
with open('file.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['name', 'age'])
    writer.writeheader()
    writer.writerows(data)
```

---

## 🔗 Связанные темы

- [[00 — Обзор работы с файлами]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[04 — 📖 Словарь ( dict ) 🔹]]

## ❓ Вопросы / Непонятное
