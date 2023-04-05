## Collections


---
### collections

* namedtuple - factory функция для создания подклассов кортежа с именованными атрибутами
* deque - контейнер похожий на список с быстрыми добавляениями и удалениями с двух сторон
* ChainMap - класс похожий на словарь для создания единого интерфейса для доступа к нескольким словарям
* Counter - подкласс словаря для подсчета хешируемых объектов
* OrderedDict - упорядоченный словарь
* defaultdict - подкласс словаря, который вызывает указанную функцию для подстановки несуществующих значений
* UserDict, UserList, UserString

---
## Временная сложность алгоритма

В информатике временна́я сложность алгоритма определяется как функция от длины строки,
представляющей входные данные, равная времени работы алгоритма на данном входе[1].
Временная сложность алгоритма обычно выражается с использованием нотации «O» большое,
которая учитывает только слагаемое самого высокого порядка, а также не учитывает
константные множители, то есть коэффициенты.


Временная сложность обычно оценивается путём подсчёта числа элементарных операций,
осуществляемых алгоритмом. Время исполнения одной такой операции при этом берётся
константой, то есть асимптотически оценивается как O(1).

---
### Список


| Operation | Average Case |
|-----------|--------------|
| Copy      | O(n) | 
| Append | O(1) | 
| Pop last  |   O(1) | 
| Pop intermediate[2] | O(n) | 
| Insert | O(n) | 
| Get Item | O(1) | 
| Set Item | O(1) | 
| Delete Item | O(n) |
| Iteration | O(n) | 
| Get Slice | O(k) | 
| Del Slice | O(n) | 
| Set Slice | O(k+n) | 
| Extend | O(k) | 
| Sort | O(n log n) | 
| Multiply | O(nk) |
| x in s |  O(n) |
| min(s), max(s) | O(n) |
| Get Length |  O(1) | 


[Source](https://wiki.python.org/moin/TimeComplexity)

---
### Deque


| Operation | Average Case |
|-----------|--------------|
| copy      | O(n) | 
| append    | O(1) | 
| appendleft | O(1) | 
| pop       |   O(1) | 
| popleft   |   O(1) | 
| extend    | O(k) | 
| extendleft | O(k) | 
| rotate    | O(k) | 
| remove    | O(n) |

[Source](https://wiki.python.org/moin/TimeComplexity)

---
### Dict


| Operation | Average Case |
|-----------|--------------|
| k in d    |  O(1) |
| copy      | O(n) | 
| Get Item | O(1) | 
| Set Item | O(1) | 
| Delete Item | O(1) |
| Iteration | O(n) | 

[Source](https://wiki.python.org/moin/TimeComplexity)

---
## collections.deque

---
### collections.deque

Deque поддерживает потокобезопасные, эффективные с точки зрения памяти
добавления и извлечения с обеих сторон двухсторонней очереди с примерно
одинаковой производительностью O(1) в любом направлении.

```python
class collections.deque([iterable[, maxlen]])
```

Методы deque:

* ``append(x)``
* ``appendleft(x)``
* ``clear()``
* ``copy()``
* ``count(x)``
* ``extend(iterable)``
* ``extendleft(iterable)``
* ``index(x[, start[, stop]])``
* ``insert(i, x)``
* ``pop()``
* ``popleft()``
* ``remove(value)``
* ``reverse()``
* ``rotate(n=1)``
* ``maxlen``


---
### collections.deque

append

```python
In [1]: from collections import deque

In [2]: d = deque([1, 2, 3])

In [3]: d.append(4)

In [4]: d
Out[4]: deque([1, 2, 3, 4])

In [5]: d.appendleft(0)

In [6]: d
Out[6]: deque([0, 1, 2, 3, 4])
```

pop
```python
In [7]: d.pop()
Out[7]: 4

In [9]: d
Out[9]: deque([0, 1, 2, 3])

In [10]: d.popleft()
Out[10]: 0

In [11]: d
Out[11]: deque([1, 2, 3])
```

index
```python
In [12]: d[0]
Out[12]: 1

In [13]: d[-1]
Out[13]: 3
```

rotate
```python
In [14]: d.rotate(1)

In [15]: d
Out[15]: deque([3, 1, 2])

In [16]: d.rotate(-2)

In [17]: d
Out[17]: deque([2, 3, 1])
```

maxlen
```python
In [19]: d = deque([1, 2, 3, 4, 5], maxlen=5)

In [20]: d
Out[20]: deque([1, 2, 3, 4, 5])

In [21]: d.append(6)

In [22]: d
Out[22]: deque([2, 3, 4, 5, 6])

In [23]: d.appendleft(100)

In [24]: d
Out[24]: deque([100, 2, 3, 4, 5])
```

---
### collections.deque

```python
def tail(filename, n=10):
    'Return the last n lines of a file'
    with open(filename) as f:
        return deque(f, n)
```

---
### [Модуль queue](https://docs.python.org/3/library/queue.html)


* ``queue.Queue``
* ``queue.LifoQueue``
* ``queue.PriorityQueue``

---
## collections.ChainMap

---
### collections.ChainMap

ChainMap - класс похожий на словарь для создания единого интерфейса для доступа к нескольким словарям

```python
class collections.ChainMap(*maps)
```

Методы:

* ``maps``
* ``new_child(m=None, **kwargs)``
* ``parents``



---
### collections.ChainMap

```python
In [1]: from collections import ChainMap

In [2]: r1 = {
   ...:    "host": "192.168.100.1",
   ...:    "auth_username": "cisco",
   ...:    "auth_password": "cisco",
   ...:    "auth_secondary": "cisco",
   ...:    "platform": "cisco_iosxe",
   ...:    "timeout_socket": 15,
   ...: }

In [3]: default_params = {
   ...:    "auth_strict_key": False,
   ...:    "timeout_socket": 5,
   ...:    "timeout_transport": 10,
   ...: }

In [4]: scrapli_params = ChainMap(r1, default_params)

In [5]: pprint(scrapli_params)
ChainMap({'auth_password': 'cisco',
          'auth_secondary': 'cisco',
          'auth_username': 'cisco',
          'host': '192.168.100.1',
          'platform': 'cisco_iosxe',
          'timeout_socket': 15},
         {'auth_strict_key': False,
          'timeout_socket': 5,
          'timeout_transport': 10})

In [6]: scrapli_params["timeout_socket"]
Out[6]: 15

In [7]: scrapli_params["timeout_transport"]
Out[7]: 10

In [8]: scrapli_params["host"]
Out[8]: '192.168.100.1'
```

---
### collections.ChainMap

maps, parents

```python
In [12]: scrapli_params.maps
Out[12]:
[{'host': '192.168.100.1',
  'auth_username': 'cisco',
  'auth_password': 'cisco',
  'auth_secondary': 'cisco',
  'platform': 'cisco_iosxe',
  'timeout_socket': 15},
 {'auth_strict_key': False, 'timeout_socket': 5, 'timeout_transport': 10}]

In [13]: scrapli_params.parents
Out[13]: ChainMap({'auth_strict_key': False, 'timeout_socket': 5, 'timeout_transport': 10})
```

---
### collections.ChainMap

``new_child``

```python
In [18]: pprint(scrapli_params)
ChainMap({'auth_password': 'cisco',
          'auth_secondary': 'cisco',
          'auth_username': 'cisco',
          'host': '192.168.100.1',
          'platform': 'cisco_iosxe',
          'timeout_socket': 15},
         {'auth_strict_key': False,
          'timeout_socket': 5,
          'timeout_transport': 10})

In [19]: updated_info = {"host": "10.1.1.1"}

In [20]: new_params = scrapli_params.new_child(updated_info)

In [22]: pprint(new_params)
ChainMap({'host': '10.1.1.1'},
         {'auth_password': 'cisco',
          'auth_secondary': 'cisco',
          'auth_username': 'cisco',
          'host': '192.168.100.1',
          'platform': 'cisco_iosxe',
          'timeout_socket': 15},
         {'auth_strict_key': False,
          'timeout_socket': 5,
          'timeout_transport': 10})

In [23]: new_params["host"]
Out[23]: '10.1.1.1'

In [25]: pprint(scrapli_params)
ChainMap({'auth_password': 'cisco',
          'auth_secondary': 'cisco',
          'auth_username': 'cisco',
          'host': '192.168.100.1',
          'platform': 'cisco_iosxe',
          'timeout_socket': 15},
         {'auth_strict_key': False,
          'timeout_socket': 5,
          'timeout_transport': 10})
```

---
### collections.Counter


---
### collections.Counter

Counter - подкласс словаря для подсчета хешируемых объектов

```python
class collections.Counter([iterable-or-mapping])
```

* ``elements()``
* ``most_common([n])``
* ``subtract([iterable-or-mapping])``
* ``total()`` (New in version 3.10)
* ``fromkeys(iterable)``


---
### collections.Counter

```python
In [2]: c1 = Counter("aaabbbccccddddd")

In [3]: c1
Out[3]: Counter({'a': 3, 'b': 3, 'c': 4, 'd': 5})

In [4]: list(c1.elements())
Out[4]: ['a', 'a', 'a', 'b', 'b', 'b', 'c', 'c', 'c', 'c', 'd', 'd', 'd', 'd', 'd']

In [5]: c1.most_common(2)
Out[5]: [('d', 5), ('c', 4)]
```

---
### collections.Counter

```python
In [3]: c1
Out[3]: Counter({'a': 3, 'b': 3, 'c': 4, 'd': 5})

In [6]: c2 = Counter(a=1, d=5)

In [7]: c2
Out[7]: Counter({'a': 1, 'd': 5})

In [9]: c1 - c2
Out[9]: Counter({'a': 2, 'b': 3, 'c': 4})

In [11]: c1.subtract(c2)

In [12]: c1 + c2
Out[12]: Counter({'a': 3, 'b': 3, 'c': 4, 'd': 5})
```

---
### collections.Counter

```python
import re
import string
from pprint import pprint
from collections import Counter


with open("text.txt") as f:
    content = f.read()

clear_text = re.sub(f"[{string.punctuation}»«]", " ", content.lower())
words = re.split("\s+", clear_text)
stats = Counter(words)

pprint(stats.most_common(20))
```

```python
[('с', 9),
 ('не', 8),
 ('потоков', 6),
 ('в', 6),
 ('из', 5),
 ('это', 5),
 ('print', 5),
 ('при', 4),
 ('потоками', 4),
 ('если', 4),
 ('будет', 4),
 ('на', 4),
 ('работает', 4),
 ('работать', 3),
 ('и', 3),
 ('например', 3),
 ('нормально', 3),
 ('что', 3),
 ('сообщения', 3),
 ('может', 3)]
```

---
### collections.defaultdict

---
### collections.defaultdict

defaultdict - подкласс словаря, который вызывает указанную функцию для подстановки несуществующих значений

```python
class collections.defaultdict(default_factory=None, /[, ...])
```

---
### collections.defaultdict

```python
In [2]: from collections import defaultdict

In [3]: data = "some very important text"

In [4]: d = defaultdict(int)

In [5]: d
Out[5]: defaultdict(int, {})

In [6]: for letter in data:
   ...:     d[letter] += 1
   ...:

In [7]: d
Out[7]:
defaultdict(int,
            {'s': 1,
             'o': 2,
             'm': 2,
             'e': 3,
             ' ': 3,
             'v': 1,
             'r': 2,
             'y': 1,
             'i': 1,
             'p': 1,
             't': 4,
             'a': 1,
             'n': 1,
             'x': 1})

In [9]: sorted(d.items(), key=lambda x: x[-1], reverse=True)
Out[9]:
[('t', 4),
 ('e', 3),
 (' ', 3),
 ('o', 2),
 ('m', 2),
 ('r', 2),
 ('s', 1),
 ('v', 1),
 ('y', 1),
 ('i', 1),
 ('p', 1),
 ('a', 1),
 ('n', 1),
 ('x', 1)]
```

---
### collections.defaultdict

config_sw1.txt
```
interface FastEthernet0/0
 switchport mode access
 switchport access vlan 10
!
interface FastEthernet0/1
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 100,200
 switchport mode trunk
!
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20
!
interface FastEthernet0/3
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 100,300,400,500,600
 switchport mode trunk
```

```python
{'FastEthernet0/0': ['switchport mode access', 'switchport access vlan 10'],
 'FastEthernet0/1': ['switchport trunk encapsulation dot1q',
                     'switchport trunk allowed vlan 100,200',
                     'switchport mode trunk'],
 'FastEthernet0/2': ['switchport mode access', 'switchport access vlan 20'],
 'FastEthernet0/3': ['switchport trunk encapsulation dot1q',
                     'switchport trunk allowed vlan 100,300,400,500,600',
                     'switchport mode trunk'],
 'FastEthernet1/0': ['switchport mode access', 'switchport access vlan 20'],
 'FastEthernet1/1': ['switchport mode access', 'switchport access vlan 30'],
 'FastEthernet1/2': ['switchport trunk encapsulation dot1q',
                     'switchport trunk allowed vlan 400,500,600',
                     'switchport mode trunk']}
```

---
### collections.defaultdict

```python
from pprint import pprint
from collections import defaultdict


def get_ip_from_cfg(filename):
    result = defaultdict(list)
    with open(filename) as f:
        for line in f:
            if line.startswith("interface"):
                intf = line.split()[-1]
            elif line.startswith(" switchport"):
                result[intf].append(line.strip())
    return result


if __name__ == "__main__":
    pprint(get_ip_from_cfg("config_sw1.txt"))
```

---
### collections.OrderedDict

---
### collections.OrderedDict

OrderedDict похож на обычные словари, но имеют некоторые дополнительные возможности,
связанные с операциями упорядочивания. Начиная с Python 3.7, обычные словари
также стали упорядоченными, но при этом в OrderedDict остались несколько полезных возможностей:

* Обычный dict оптимизирован на операции mapping (getitem, setitem, deleteitem)
* OrderedDict оптимизирован под операции переупорядочивание
* Алгоритмически OrderedDict может обрабатывать частые операции переупорядочения лучше, чем dict
* При сравнении равенства словарей, OrderedDict учитывает соответствие порядка
* У OrderedDict есть метод ``move_to_end()`` для эффективного перемещения элемента в конец словаря

---
### collections.OrderedDict

```python
class collections.OrderedDict([items])
```

* ``popitem(last=True)``
* ``move_to_end(key, last=True)``

---
### collections.OrderedDict

```python
In [10]: d1 = {1: 100, 2: 200}

In [11]: d2 = {2: 200, 1: 100}

In [12]: d1 == d2
Out[12]: True

In [13]: from collections import OrderedDict

In [14]: o1 = OrderedDict({1: 100, 2: 200})

In [15]: o2 = OrderedDict({2: 200, 1: 100})

In [16]: o1 == o2
Out[16]: False
```

move_to_end

```python
In [17]: o1
Out[17]: OrderedDict([(1, 100), (2, 200)])

In [18]: o1.move_to_end(1)

In [19]: o1
Out[19]: OrderedDict([(2, 200), (1, 100)])
```

---
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


class Task:
    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return f"Task('{self.name}')"


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

---
### Версия Todo с использованием collections.abc.MutableSequence

```python
from collections.abc import MutableSequence


class Task:
    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return f"Task('{self.name}')"


class ToDo(MutableSequence):
    def __init__(self, tasks):
        self.tasks = tasks

    def __repr__(self):
        return f"ToDo('{self.tasks}')"

    def __getitem__(self, index):
        print("__getitem__", index)
        return self.tasks[index]

    def __setitem__(self, index, value):
        print("__setitem__", index)
        self.tasks[index] = value

    def __delitem__(self, index):
        print("__detitem__", index)
        del self.tasks[index]

    def __len__(self):
        return len(self.tasks)

    def insert(self, index, value):
        self.tasks.insert(index, value)


task1 = Task("task1")
task2 = Task("task2")
task3 = Task("task3")
task4 = Task("task4")
task5 = Task("task5")
todo_list = [task1, task2, task3, task4, task5]
todo = ToDo(todo_list)
```

