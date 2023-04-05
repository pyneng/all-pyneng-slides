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


class ToDo(UserList):
    def postpone(self):
        first_task = self.data.pop(0)
        self.data.append(first_task)


task1 = Task("task1")
task2 = Task("task2")
task3 = Task("task3")
task4 = Task("task4")
task5 = Task("task5")

todo_list = [task1, task2, task3]
todo = ToDo(todo_list)
```

---
### collections.UserList


```python
In [2]: todo
Out[2]: [Task('task1'), Task('task2'), Task('task3')]

In [3]: todo[0]
Out[3]: Task('task1')

In [4]: todo[:-1]
Out[4]: [Task('task1'), Task('task2')]

In [5]: todo.postpone()

In [6]: todo
Out[6]: [Task('task2'), Task('task3'), Task('task1')]
```

