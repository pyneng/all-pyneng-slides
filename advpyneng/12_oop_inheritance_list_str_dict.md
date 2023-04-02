### collections UserList, UserDict, UserStr

---
### collections.UserList

Этот класс действует как оболочка для list. Это полезный базовый
класс для создания своих классов, которые могут наследовать UserList
и переопределять существующие методы или добавлять новые.

---
### Почему бы не наследовать встроенные list, dict, str?

При наследовании UserDict

```python
from collections import UserDict

class MyDict(UserDict):
    def __setitem__(self, key, value):
        print("__setitem__", key)


In [20]: d1 = MyDict({1: 100, 2: 200})
__setitem__ 1
__setitem__ 2

In [21]: d1.update({3: 300})
__setitem__ 3
```

Встроенный dict
```python
class MyDict(dict):
    def __setitem__(self, key, value):
        print("__setitem__", key)


In [12]: d1 = MyDict({1: 100, 2: 200})

In [15]: d1.update({3: 300})
```

---
### collections.UserList

```python
from collections import UserList



