```python
from datetime import date
dates = [date(*map(int, d.split('-'))) for d in open(0)]
print((max(dates) - min(dates)).days)
```
😊 Прочитай в документации про функцию open(): https://docs-python.ru/tutorial/vstroennye-funktsii-interpretatora-python/funktsija-open/ Она может принимать в качестве аргумента не только абсолютный/относительный путь к файлу, но и дескриптор файла: https://timeweb.com/ru/community/articles/chto-takoe-faylovyy-deskriptor-prostymi-slovami Под stdin (стандартный вход) в системе зарезервирован дескриптор 0. Таким образом, я читаю входной поток с помощью функции open() напрямую, используя дескриптор. При этом импорт stdin из sys не нужен.