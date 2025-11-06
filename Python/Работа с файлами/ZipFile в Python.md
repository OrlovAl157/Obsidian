
## 📋 Быстрая справка: Основные методы

| Метод | Назначение | Пример |
|-------|-----------|--------|
| `ZipFile(file, mode)` | Открыть/создать архив | `ZipFile('arc.zip', 'w')` |
| `.write(file, arcname)` | Добавить файл | `zipf.write('file.txt')` |
| `.extractall(path, members)` | Распаковать всё | `zipf.extractall('folder')` |
| `.extract(file, path, pwd)` | Распаковать один | `zipf.extract('file.txt')` |
| `.read(file, pwd)` | Прочитать (bytes) | `zipf.read('file.txt')` |
| `.namelist()` | Список файлов | `files = zipf.namelist()` |
| `.writestr(name, data)` | Записать данные | `zipf.writestr('f.txt', 'text')` |

## 🔑 Параметры ZipFile

| Параметр | По умолчанию | Описание | Пример |
|----------|--------------|----------|--------|
| `mode` | 'r' | Режим: 'r', 'w', 'a', 'x' | `mode='w'` |
| `compression` | ZIP_STORED | Метод сжатия | `ZIP_DEFLATED` |
| `compresslevel` | None | Уровень (0-9) | `compresslevel=9` |

**Методы сжатия:**
- `ZIP_STORED` (0) — без сжатия
- `ZIP_DEFLATED` (8) — стандарт ⭐
- `ZIP_BZIP2` (12) — сильное сжатие
- `ZIP_LZMA` (14) — максимальное сжатие

## 💻 Примеры

### Создание архива

```python
import zipfile

# Один файл
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.write('file.txt')

# Несколько файлов с сжатием
with zipfile.ZipFile('archive.zip', 'w', zipfile.ZIP_DEFLATED) as zipf:
    zipf.write('file1.txt')
    zipf.write('file2.txt')
    zipf.write('data/file3.csv', arcname='file3.csv')  # переименовать

# Архивировать папку
import os

def zip_folder(folder_path, output_zip):
    with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as zipf:
        for root, dirs, files in os.walk(folder_path):
            for file in files:
                file_path = os.path.join(root, file)
                arcname = os.path.relpath(file_path, folder_path)
                zipf.write(file_path, arcname)
```

### Распаковка

```python
# Распаковать всё
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extractall('output')

# Распаковать конкретные файлы (members)
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extractall('output', members=['file1.txt', 'file2.txt'])

# Один файл
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extract('file.txt', path='output')
```

### Чтение без распаковки

```python
# Прочитать в память
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    content = zipf.read('file.txt')  # bytes
    text = content.decode('utf-8')   # str

# Построчно
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    with zipf.open('file.txt') as f:
        for line in f:
            print(line.decode('utf-8'))
```

### Просмотр содержимого

```python
# Список файлов
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    files = zipf.namelist()
    print(files)  # ['file1.txt', 'folder/file2.txt']

# Детальная информация
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    for info in zipf.infolist():
        print(f"{info.filename}: {info.file_size} → {info.compress_size} байт")
        print(f"Сжатие: {100 - (info.compress_size/info.file_size*100):.1f}%")
```

### Добавление в существующий

```python
# Режим 'a' (append)
with zipfile.ZipFile('archive.zip', 'a') as zipf:
    zipf.write('new_file.txt')
```

### Запись данных напрямую

```python
# Без создания файла на диске
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.writestr('generated.txt', 'Это текст')
```

### Работа с паролями

```python
# Чтение защищенного архива
with zipfile.ZipFile('protected.zip', 'r') as zipf:
    zipf.setpassword(b'password123')
    content = zipf.read('secret.txt')

# Распаковка с паролем
with zipfile.ZipFile('protected.zip', 'r') as zipf:
    zipf.extractall(pwd=b'password123')
    # или для конкретных файлов
    zipf.extractall(pwd=b'password123', members=['file1.txt'])
```

⚠️ **Важно:** `zipfile` может **читать** защищенные архивы, но **не создавать** их.

### Проверка архива

```python
# Проверить целостность
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    result = zipf.testzip()
    if result is None:
        print("✅ Архив в порядке")
    else:
        print(f"❌ Поврежден: {result}")

# Проверить, является ли ZIP
is_zip = zipfile.is_zipfile('file.zip')
```

### Обработка ошибок

```python
try:
    with zipfile.ZipFile('archive.zip', 'r') as zipf:
        zipf.extractall()
except zipfile.BadZipFile:
    print("❌ Файл поврежден")
except FileNotFoundError:
    print("❌ Архив не найден")
except PermissionError:
    print("❌ Нет прав доступа")
```

## 🔍 Полезные функции

```python
# Общий размер архива
def get_zip_size(zip_path):
    with zipfile.ZipFile(zip_path, 'r') as zipf:
        return sum(info.file_size for info in zipf.infolist())

# Извлечь по расширению
def extract_by_ext(zip_path, ext, output_dir):
    with zipfile.ZipFile(zip_path, 'r') as zipf:
        files = [f for f in zipf.namelist() if f.endswith(ext)]
        zipf.extractall(output_dir, members=files)

# Объединить архивы
def merge_zips(zip_files, output_zip):
    with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as output:
        for zip_file in zip_files:
            with zipfile.ZipFile(zip_file, 'r') as input_zip:
                for filename in input_zip.namelist():
                    output.writestr(filename, input_zip.read(filename))
```

## 📝 Сводная шпаргалка

```python
import zipfile
import os

# СОЗДАНИЕ
with zipfile.ZipFile('arc.zip', 'w', zipfile.ZIP_DEFLATED, compresslevel=9) as z:
    z.write('file.txt', arcname='renamed.txt')
    z.writestr('gen.txt', 'content')

# РАСПАКОВКА
with zipfile.ZipFile('arc.zip', 'r') as z:
    z.extractall('output')  # всё
    z.extractall('output', members=['file1.txt'])  # выборочно
    z.extract('file.txt')  # один файл

# ЧТЕНИЕ
with zipfile.ZipFile('arc.zip', 'r') as z:
    data = z.read('file.txt')  # bytes
    files = z.namelist()  # список
    info = z.getinfo('file.txt')  # детали

# ДОБАВЛЕНИЕ
with zipfile.ZipFile('arc.zip', 'a') as z:
    z.write('new.txt')

# С ПАРОЛЕМ
with zipfile.ZipFile('protected.zip', 'r') as z:
    z.setpassword(b'pass')
    z.extractall(pwd=b'pass', members=['secret.txt'])

# ПРОВЕРКА
zipfile.is_zipfile('file.zip')
with zipfile.ZipFile('arc.zip', 'r') as z:
    z.testzip()  # None = OK
```

## 🎯 Лучшие практики

✅ **Рекомендуется:**
- Используйте `with` для автозакрытия
- Указывайте `compression=zipfile.ZIP_DEFLATED`
- Используйте `arcname` для контроля путей
- Обрабатывайте `BadZipFile` исключения
- Проверяйте архив перед распаковкой
- Используйте `members` для выборочной распаковки

❌ **Не рекомендуется:**
- Открывать без `with`
- Игнорировать исключения
- Распаковывать без проверки
- Забывать про `arcname`

## ⚠️ Важные особенности

**Параметр `members`:**
```python
# Распаковать только нужные файлы
members_list = ['file1.txt', 'folder/file2.txt']
zipf.extractall('output', members=members_list)
```

**Ограничения:**
- Максимум 4GB без ZIP64 (включен по умолчанию)
- Нельзя создавать защищенные архивы (только читать)
- Кортежи как ключи не поддерживаются

**Кодировка:**
```python
# Python 3 корректно работает с кириллицей
zipf.write('файл.txt')  # OK
```

## 💡 Запомнить

1. `zipfile` — встроенный модуль Python
2. Режимы: `'r'` (чтение), `'w'` (запись), `'a'` (добавление)
3. `ZIP_DEFLATED` — стандартное сжатие
4. `arcname` — имя файла в архиве
5. `members` — список файлов для распаковки
6. Можно читать без полной распаковки
7. Паролем защитить можно только при чтении
8. Всегда используйте `with`

## 🔄 Таблица методов класса ZipFile

|            Метод             |         Описание         |    Возвращает    |
| :--------------------------: | :----------------------: | :--------------: |
| `.write(filename, arcname)`  |  Добавить файл в архив   |       None       |
| `.writestr(filename, data)`  | Записать данные напрямую |       None       |
|        `.read(name)`         |      Прочитать файл      |      bytes       |
|     `.open(name, mode)`      |       Открыть файл       | file-like object |
|   `.extract(member, path)`   |    Извлечь один файл     |    str (путь)    |
| `.extractall(path, members)` |    Извлечь все файлы     |       None       |
|        `.namelist()`         |    Список имен файлов    |    list[str]     |
|        `.infolist()`         |   Детальная информация   |  list[ZipInfo]   |
|       `.getinfo(name)`       |    Информация о файле    |     ZipInfo      |
|         `.testzip()`         |  Проверить целостность   |   str или None   |
|     `.setpassword(pwd)`      |    Установить пароль     |       None       |
|        `.printdir()`         |    Вывести содержимое    |       None       |
