
## Что такое CSV?

**CSV** (Comma-Separated Values) — текстовый формат для табличных данных, где:

- Каждая строка файла = строка таблицы
- Поля разделены запятыми (или другим символом)
- Поля с запятыми внутри обрамляются кавычками `"поле, с запятой"`

**TSV** — то же самое, но разделитель — табуляция `\t`

-----

## Ручная работа с CSV

```python
# Чтение
with open('data.csv', encoding='utf-8') as file:
    data = file.read()
    table = [row.split(',') for row in data.splitlines()]
    del table[0]  # удалить заголовок
    
# Сортировка по числовому столбцу
table.sort(key=lambda item: int(item[1]))
```

**Минусы**: не обрабатывает кавычки и запятые внутри полей автоматически.

-----

## Модуль csv

### Импорт

```python
import csv
```

### Чтение с csv.reader

```python
with open('file.csv', encoding='utf-8') as file:
    rows = csv.reader(file, delimiter=',', quotechar='"')
    for row in rows:
        print(row)  # каждая row — это список
```

**Параметры**:

- `delimiter` — разделитель (по умолчанию `','`)
- `quotechar` — символ кавычек (по умолчанию `'"'`)

### Чтение с csv.DictReader (удобнее!)

```python
with open('file.csv', encoding='utf-8') as file:
    rows = csv.DictReader(file, delimiter=';')
    for row in rows:
        print(row['price'])  # обращение по имени столбца
        print(row['product_name'])
```

**Преимущества**:

- Обращение по именам столбцов (не по индексам)
- Заголовок обрабатывается автоматически
- Каждая строка — словарь

**Сортировка**:

```python
rows = csv.DictReader(file)
sorted_data = sorted(rows, key=lambda x: int(x['price']), reverse=True)
```

-----

## Запись данных

### Запись с csv.writer

```python
columns = ['name', 'age', 'city']
data = [['Иван', 25, 'Москва'], ['Анна', 30, 'СПб']]

with open('output.csv', 'w', encoding='utf-8', newline='') as file:
    writer = csv.writer(file, delimiter=';', quoting=csv.QUOTE_NONNUMERIC)
    writer.writerow(columns)     # записать заголовок
    writer.writerows(data)       # записать все строки сразу
```

**Параметр `quoting`**:

- `csv.QUOTE_ALL` — все поля в кавычках
- `csv.QUOTE_MINIMAL` — только поля со спецсимволами
- `csv.QUOTE_NONNUMERIC` — все нечисловые поля
- `csv.QUOTE_NONE` — без кавычек

### Запись с csv.DictWriter

```python
data = [
    {'name': 'Иван', 'age': 25, 'city': 'Москва'},
    {'name': 'Анна', 'age': 30, 'city': 'СПб'}
]

columns = ['name', 'age', 'city']

with open('output.csv', 'w', encoding='utf-8', newline='') as file:
    writer = csv.DictWriter(file, fieldnames=columns, delimiter=';')
    writer.writeheader()      # записать заголовок
    writer.writerows(data)    # записать строки
```

-----

## Важные моменты

### При открытии файла для записи

```python
with open('file.csv', 'w', encoding='utf-8', newline='') as file:
```

**`newline=''`** — обязательно! Иначе появятся лишние пустые строки.

### Reader — это итератор

```python
rows = csv.reader(file)
# Можно преобразовать в список:
all_rows = list(rows)
# Но нельзя итерировать дважды без пересоздания!
```

### Передавайте файловый объект, а не текст

```python
# ❌ Неправильно:
data = file.read()
rows = csv.reader(data)

# ✅ Правильно:
rows = csv.reader(file)
```

-----

## Шпаргалка по выбору метода

|Задача                              |Используй         |
|------------------------------------|------------------|
|Простое чтение, работа по индексам  |`csv.reader()`    |
|Чтение с доступом по именам столбцов|`csv.DictReader()`|
|Простая запись списков              |`csv.writer()`    |
|Запись словарей                     |`csv.DictWriter()`|

-----

## Пример: полный цикл

```python
import csv

# Чтение
with open('input.csv', encoding='utf-8') as file:
    rows = list(csv.DictReader(file, delimiter=';'))

# Обработка
filtered = [row for row in rows if int(row['age']) > 25]
filtered.sort(key=lambda x: x['name'])

# Запись
with open('output.csv', 'w', encoding='utf-8', newline='') as file:
    writer = csv.DictWriter(file, fieldnames=['name', 'age'], delimiter=',')
    writer.writeheader()
    writer.writerows(filtered)
```