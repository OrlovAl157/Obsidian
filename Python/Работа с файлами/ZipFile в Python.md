# Памятка по работе с ZIP-архивами

## Таблица методов ZipFile

| Метод                                          |              Описание              |          Пример использования           |
| :--------------------------------------------- | :--------------------------------: | :-------------------------------------: |
| `ZipFile(file, mode, compression, allowZip64)` |       Открыть/создать архив        | `ZipFile('arc.zip', 'w', ZIP_DEFLATED)` |
| `.write(filename, arcname, compress_type)`     |       Добавить файл в архив        |   `zipf.write('file.txt', 'new.txt')`   |
| `.writestr(filename, data, compress_type)`     |      Записать данные напрямую      |    `zipf.writestr('f.txt', 'text')`     |
| `.read(name, pwd)`                             |  Прочитать файл из архива (bytes)  |     `data = zipf.read('file.txt')`      |
| `.open(name, mode, pwd)`                       |       Открыть файл как поток       |     `with zipf.open('f.txt') as f:`     |
| `.extract(member, path, pwd)`                  |         Извлечь один файл          |   `zipf.extract('file.txt', 'out/')`    |
| `.extractall(path, members, pwd)`              |         Извлечь все файлы          |      `zipf.extractall('folder/')`       |
| `.namelist()`                                  |      Список имен всех файлов       |        `files = zipf.namelist()`        |
| `.infolist()`                                  | Детальная информация о всех файлах |        `info = zipf.infolist()`         |
| `.getinfo(name)`                               |   Информация о конкретном файле    |    `info = zipf.getinfo('file.txt')`    |
| `.testzip()`                                   |    Проверить целостность архива    |        `result = zipf.testzip()`        |
| `.setpassword(pwd)`                            |    Установить пароль для чтения    |       `zipf.setpassword(b'pass')`       |
| `.printdir()`                                  |    Вывести содержимое в консоль    |            `zipf.printdir()`            |
| `.close()`                                     |           Закрыть архив            |             `zipf.close()`              |

## Функции модуля zipfile

|Функция|Описание|Пример использования|
|:-:|:-:|:--|
|`is_zipfile(filename)`|Проверить, является ли файл ZIP|`is_zipfile('file.zip')`|

## Объект ZipInfo и его атрибуты

Объекты **ZipInfo** содержат информацию о файлах в архиве. Получаются через методы `getinfo()` и `infolist()`.

|Атрибут/Метод|Описание|Пример|
|:-:|:-:|:--|
|`.filename`|Полное имя файла в архиве|`info.filename` → `'test/file.txt'`|
|`.file_size`|Размер исходного файла (байты)|`info.file_size` → `2324421`|
|`.compress_size`|Размер сжатого файла (байты)|`info.compress_size` → `2322032`|
|`.date_time`|Дата изменения (кортеж)|`info.date_time` → `(2021, 11, 8, 7, 30, 6)`|
|`.compress_type`|Тип сжатия|`info.compress_type` → `8` (ZIP_DEFLATED)|
|`.is_dir()`|Проверка, является ли директорией|`info.is_dir()` → `True/False`|

---

## Параметры ZipFile

### Режимы открытия

- **`'r'`** — чтение (архив должен существовать) - по умолчанию
- **`'w'`** — запись (создаёт новый архив, перезаписывает существующий)
- **`'a'`** — добавление (добавляет файлы в существующий архив)
- **`'x'`** — эксклюзивное создание (ошибка, если архив существует)

### Методы сжатия (параметр compression)

- **`ZIP_STORED`** — без сжатия (по умолчанию, значение 0)
- **`ZIP_DEFLATED`** — стандартное сжатие DEFLATE (значение 8)
- **`ZIP_BZIP2`** — сжатие BZIP2 (значение 12)
- **`ZIP_LZMA`** — сжатие LZMA (значение 14)

```python
from zipfile import ZipFile, ZIP_DEFLATED

# Создание архива с сжатием
with ZipFile('archive.zip', 'w', ZIP_DEFLATED) as zipf:
    zipf.write('file.txt')
```

### Дополнительные параметры ZipFile

- **`allowZip64=True`** — поддержка архивов >4GB (по умолчанию True)
- **`compresslevel`** — уровень сжатия от 0 до 9 (для DEFLATED и BZIP2)

---

## Основные операции

### 1. Создание архива

```python
import zipfile

# Создание нового архива
with zipfile.ZipFile('archive.zip', 'w', zipfile.ZIP_DEFLATED) as zip:
    zip.write('file1.txt')
    zip.write('file2.txt', arcname='renamed.txt')  # с переименованием
    zip.write('folder/file3.txt', arcname='file3.txt')  # без пути
```

### 2. Добавление файлов

```python
# Добавление в существующий архив
with zipfile.ZipFile('archive.zip', 'a') as zip:
    zip.write('new_file.txt')
    
# Добавление строки как файла
with zipfile.ZipFile('archive.zip', 'a') as zip:
    zip.writestr('data.txt', 'Содержимое файла')
```

### 3. Извлечение файлов

```python
# Извлечь все файлы
with zipfile.ZipFile('archive.zip', 'r') as zip:
    zip.extractall('output_folder/')
    
# Извлечь один файл
with zipfile.ZipFile('archive.zip', 'r') as zip:
    zip.extract('file.txt', 'output_folder/')
    
# Извлечь с паролем
with zipfile.ZipFile('archive.zip', 'r') as zip:
    zip.extractall(pwd=b'password')
```

### 4. Чтение содержимого

```python
# Список файлов
with zipfile.ZipFile('archive.zip', 'r') as zip:
    print(zip.namelist())
    
# Чтение файла без извлечения
with zipfile.ZipFile('archive.zip', 'r') as zip:
    content = zip.read('file.txt')
    text = content.decode('utf-8')
    
# Открыть файл как поток
with zipfile.ZipFile('archive.zip', 'r') as zip:
    with zip.open('file.txt') as file:
        for line in file:
            print(line.decode('utf-8'))
```

### 5. Получение информации

```python
with zipfile.ZipFile('archive.zip', 'r') as zip:
    # Информация о конкретном файле
    info = zip.getinfo('file.txt')
    print(f'Размер: {info.file_size} байт')
    print(f'Сжатый размер: {info.compress_size} байт')
    print(f'Дата изменения: {info.date_time}')
    
    # Информация обо всех файлах
    for info in zip.infolist():
        print(f'{info.filename}: {info.file_size} байт')
```

### 6. Работа с паролями

```python
# Создание защищённого архива (только для извлечения)
with zipfile.ZipFile('secure.zip', 'w') as zip:
    zip.write('secret.txt')
    zip.setpassword(b'mypassword')
    
# Извлечение с паролем
with zipfile.ZipFile('secure.zip', 'r') as zip:
    zip.extractall(pwd=b'mypassword')
```

### 7. Проверка архива

```python
# Проверка целостности
with zipfile.ZipFile('archive.zip', 'r') as zip:
    result = zip.testzip()
    if result is None:
        print('Архив в порядке')
    else:
        print(f'Повреждён файл: {result}')
        
# Проверка, является ли файл ZIP-архивом
if zipfile.is_zipfile('archive.zip'):
    print('Это ZIP-архив')
```

---

## Практические примеры

### Архивирование папки с сохранением структуры

```python
import os
import zipfile

def zip_folder(folder_path, output_zip):
    with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as zip:
        for root, dirs, files in os.walk(folder_path):
            for file in files:
                file_path = os.path.join(root, file)
                arcname = os.path.relpath(file_path, folder_path)
                zip.write(file_path, arcname)

zip_folder('my_folder', 'backup.zip')
```

### Извлечение с фильтрацией

```python
# Извлечь только текстовые файлы
with zipfile.ZipFile('archive.zip', 'r') as zip:
    for name in zip.namelist():
        if name.endswith('.txt'):
            zip.extract(name, 'output/')
```

### Создание архива в памяти

```python
from io import BytesIO

# Создание в памяти
zip_buffer = BytesIO()
with zipfile.ZipFile(zip_buffer, 'w', zipfile.ZIP_DEFLATED) as zip:
    zip.writestr('file1.txt', 'Content 1')
    zip.writestr('file2.txt', 'Content 2')

# Получение байтов
zip_bytes = zip_buffer.getvalue()
```

---

## Полезные советы

1. **Всегда используйте контекстный менеджер** (`with`) для автоматического закрытия архива
2. **Для больших файлов** используйте `ZIP_DEFLATED` для экономии места
3. **При архивировании папок** используйте `os.walk()` для рекурсивного обхода
4. **Проверяйте архивы** с помощью `testzip()` после создания
5. **Для защиты данных** помните, что ZIP-шифрование слабое, используйте внешние средства для критичных данных
6. **При работе с кириллицей** указывайте кодировку при чтении: `content.decode('utf-8')`
7. **Используйте `arcname`** для контроля структуры файлов в архиве

---

## Обработка ошибок

```python
import zipfile

try:
    with zipfile.ZipFile('archive.zip', 'r') as zip:
        zip.extractall()
except zipfile.BadZipFile:
    print('Файл повреждён или не является ZIP-архивом')
except FileNotFoundError:
    print('Архив не найден')
except RuntimeError:
    print('Неверный пароль или проблема с извлечением')
```