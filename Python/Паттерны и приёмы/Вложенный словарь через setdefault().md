## Вложенный словарь через setdefault()

Элегантный способ создать словарь в словаре без проверок на существование ключей.

```python
for row in rows:
    result.setdefault(row[1], {}).setdefault(row[2], row[0])
```

**Пример:**
```python
rows = [
    ['user1', 'email1@test.com', '2023-01-01'],
    ['user2', 'email1@test.com', '2023-01-02'], 
    ['user3', 'email2@test.com', '2023-01-01']
]

result = {}
for row in rows:
    result.setdefault(row[1], {}).setdefault(row[2], row[0])
```

**Результат:**
```python
{
    'email1@test.com': {
        '2023-01-01': 'user1',
        '2023-01-02': 'user2'
    },
    'email2@test.com': {
        '2023-01-01': 'user3'
    }
}
```

> 🔗 См. также: [[📖 Словарь ( dict )]]