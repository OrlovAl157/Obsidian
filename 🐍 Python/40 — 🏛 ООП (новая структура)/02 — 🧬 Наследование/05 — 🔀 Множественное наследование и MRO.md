---
tags:
  - python
  - ООП
  - наследование
  - статус/завершён
---

# 🔀 Множественное наследование и MRO

## 📌 Коротко

> Множественное наследование — класс наследуется от двух и более классов одновременно. Порядок поиска методов определяется MRO (Method Resolution Order) — алгоритмом C3-линеаризации.

---

## 📖 Синтаксис

```python
class Parent1:
    pass

class Parent2:
    pass

class Child(Parent1, Parent2):  # наследуется от двух классов
    pass
```

---

## 📖 Множественное vs Многоуровневое

| | Множественное | Многоуровневое |
|---|---|---|
| Структура | Ветвистая | Линейная |
| Пример | `class D(B, C)` | `class C(B), class B(A)` |
| Проблемы | Ромбовидное наследование | Нет |

---

## 📖 Проблема ромбовидного наследования

Возникает когда два класса `B` и `C` наследуются от `A`, а `D` наследуется от `B` и `C`:

```
    A
   / \
  B   C
   \ /
    D
```

```python
class A:
    def method(self): print('A')

class B(A):
    def method(self): print('B')

class C(A):
    def method(self): print('C')

class D(B, C):
    pass

D().method()  # B — MRO решает неоднозначность
```

---

## 📖 MRO — Method Resolution Order

Строгий порядок поиска методов в иерархии классов. Для каждого класса свой.

```python
print(D.mro())
# [D, B, C, A, object]

print(D.__mro__)  # то же самое, но кортеж
```

> [!tip] Правило
> В MRO первый — сам класс, последний — `object`.

---

## 📖 Алгоритм построения MRO

**Шаг 1** — поиск в глубину слева направо со всеми повторами:
```
D, B, A, object, C, A, object
```

**Шаг 2** — удаляем повторы, оставляем только **последнее** вхождение:
```
D, B, C, A, object
```

Пример сложнее:
```python
class A: pass
class B(A): pass
class C(B): pass
class D(A): pass
class E(D): pass
class F(C, E): pass

# Шаг 1: F, C, B, A, object, E, D, A, object
# Повторы: A, object → удаляем первые вхождения
# Шаг 2: F, C, B, E, D, A, object ✅
print(F.mro())
```

---

## 📖 Невозможность построения MRO

Два правила которые должны соблюдаться:

1. **Правило наследования** — родители должны идти в том порядке в каком указаны
2. **Правило старшинства** — родитель не может стоять перед своим дочерним классом

Если правила конфликтуют — `TypeError`:

```python
# Ситуация 1 — нарушение старшинства:
class A: pass
class B(A): pass
class C(A, B): pass  # TypeError! A стоит перед B, но B наследник A

# Ситуация 2 — конфликт порядков:
class A: pass
class B: pass
class C(A, B): pass  # требует A перед B
class D(B, A): pass  # требует B перед A
class E(C, D): pass  # TypeError! противоречие
```

---

## 📖 super() и MRO

`super()` не обращается к конкретному родителю — он ищет метод по **всему MRO**:

```python
class A:
    pass

class B(A):
    pass

class C(A):
    def method(self):
        print('Метод класса C')

class D(B, C):
    def method(self):
        super().method()  # MRO: D→B→C→A→object
                          # B не имеет метода → находит в C

D().method()  # Метод класса C
```

---

## 📖 Обратите внимание

Даже простое множественное наследование — ромбовидное, потому что все классы наследуются от `object`:

```python
class A(object): pass
class B(object): pass
class C(A, B): pass
# C → A → B → object — ромб!
```

---

## 📊 Quick Reference

```python
MyClass.mro()        # MRO в виде списка
MyClass.__mro__      # MRO в виде кортежа
issubclass(D, A)     # True — с учётом всей иерархии
isinstance(obj, A)   # True — с учётом всей иерархии
```

---

## 🔗 Связанные темы

- [[01 — 🧬 Введение в наследование]]
- [[02 — 🔁 super() и расширение методов]]
- [[06 — 🎭 Полиморфизм]]
- [[07 — 🧩 Композиция]]
