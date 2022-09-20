# Python для сетевых инженеров 

---
## Типы данных в Python

---
### Типы данных в Python

В Python есть несколько стандартных типов данных:
* Integers (целые числа)
* Strings (строки)
* Lists (списки)
* Dictionaries (словари)
* Tuples (кортежи)
* Sets (множества)
* Boolean (булевы значения)

---
### Типы данных в Python

Изменяемые:
* Списки
* Словари
* Множества

Неизменяемые
* Числа
* Строки
* Кортежи

---
### Типы данных в Python

Упорядоченные:
* Списки
* Кортежи
* Строки
* Словари

Неупорядоченные:
* Множества


---
### Типы данных в Python

| Название    | Название        | Пример |
|-------------|-----------------|--------------|
| String      | строка          | ``"interface Gi0/0"`` |
|             |                 |
| List        | список          | ``[1, 2, 3]`` |
|             |                 |
| Dictionary  | словарь         | ``{"username": "user1", "permissions": 100}`` |
|             |                 |
| Tuple       | кортеж          | ``("line console 0", "login local")`` |
|             |                 |
| Set         | множество       | ``{3, 10, 100, 4, 5}`` |
|             |                 |
| Boolean     | булево значение | ``True, False`` |

---
### Типы данных в Python

|             |               |
|-------------|--------------|
| String      | Для хранения текста и произвольных данных, для которых в Python нет типа |
| List        | Набор данных, очень часто однотипных данных: список вланов, команд, строк файла |
| Dictionary  | Используется для данных типа поле: значение |
| Tuple       | Неизменяемый набор данных, который часто возвращается из внешних источников - БД, CSV. |
|             | Также используется когда данные это набор значений: (x, y) |
| Set         | Для хранения уникальных данных. Плюс часто используется промежуточно для |
|             | операций с множествами |
| Boolean     | Указывает, что какое-то значение истинное или ложное. Например {"routing": True} |
| None        | Используется, когда надо указать, что в этом месте нет никакого значения |


|             |              |
|-------------|--------------|
| String      | "interface Gi0/0" |
| List        | [1, 2, 3] |
| Dictionary  | {"username": "user1", "permissions": 100} |
| Tuple       | ("line console 0", "login local") |
| Set         | {3, 10, 100, 4, 5} |
| Boolean     | True, False |


---
## Переменные

---
### Переменные

Переменные в Python:
* не требуют объявления типа переменной (Python - язык с динамической типизацией)
* являются ссылками на область памяти

Правила именования переменных:
* имя переменной может состоять только из букв, цифр и знака подчеркивания
* имя не может начинаться с цифры
* имя не может содержать специальных символов @, $, %

---
### Переменные

```python
In [1]: a = 3

In [2]: b = 'Hello'

In [3]: c, d = 9, 'Test'

In [4]: print(a, b, c, d)
3 Hello 9 Test
```

Обратите внимание, что в Python не нужно указывать, что a это число, а b это строка.


---
### Имена переменных

В Python есть рекомендации по именованию функций, классов и переменных:
* имена переменных обычно пишутся полностью большими или маленькими буквами
  * DB_NAME
  * db_name
* имена функций задаются маленькими буквами, с подчеркиваниями между словами
  * get_names
* имена классов задаются словами с заглавными буквами, без пробелов
  * CiscoSwitch


---
## Числа

---
### Числа

```python
In [1]: 1 + 2
Out[1]: 3

In [2]: 1.0 + 2
Out[2]: 3.0

In [3]: 10 - 4
Out[3]: 6

In [4]: 2**3
Out[4]: 8
```

---
## Строки (Strings)

---
### Строки

Строка в Python:
* последовательность символов, заключенная в кавычки
* неизменяемый упорядоченный тип данных

```python
In [9]: 'Hello'
Out[9]: 'Hello'

In [10]: "Hello"
Out[10]: 'Hello'
```

---
### Строки

```python
tunnel = """
interface Tunnel0
 ip address 10.10.10.1 255.255.255.0
 ip mtu 1416
 ip ospf hello-interval 5
 tunnel source FastEthernet1/0
 tunnel protection ipsec profile DMVPN
"""

In [12]: tunnel
Out[12]: '\ninterface Tunnel0\n ip address 10.10.10.1 255.255.255.0\n ip mtu 1416\n ip ospf hello-interval 5\n tunnel source FastEthernet1/0\n tunnel protection ipsec profile DMVPN\n'

In [13]: print(tunnel)

interface Tunnel0
 ip address 10.10.10.1 255.255.255.0
 ip mtu 1416
 ip ospf hello-interval 5
 tunnel source FastEthernet1/0
 tunnel protection ipsec profile DMVPN
```

---
### Действия со строками

```python
In [1]: line = "test"

In [2]: line.
          capitalize   find         isdecimal    istitle      partition    rstrip       translate
          casefold     format       isdigit      isupper      replace      split        upper
          center       format_map   isidentifier join         rfind        splitlines   zfill
          count        index        islower      ljust        rindex       startswith
          encode       isalnum      isnumeric    lower        rjust        strip
          endswith     isalpha      isprintable  lstrip       rpartition   swapcase
          expandtabs   isascii      isspace      maketrans    rsplit       title
```

---
### Методы строк

```
In [10]: from rich import inspect

In [13]: inspect(str, methods=True)
╭───────────────────────────────────────────────────────────── <class 'str'> ─────────────────────────────────────────────────────────────╮
│ def str(...)                                                                                                                            │
│                                                                                                                                         │
│ str(object='') -> str                                                                                                                   │
│ str(bytes_or_buffer[, encoding[, errors]]) -> str                                                                                       │
│                                                                                                                                         │
│   capitalize = def capitalize(self, /): Return a capitalized version of the string.                                                     │
│     casefold = def casefold(self, /): Return a version of the string suitable for caseless comparisons.                                 │
│       center = def center(self, width, fillchar=' ', /): Return a centered string of length width.                                      │
│        count = def count(...) S.count(sub[, start[, end]]) -> int                                                                       │
│       encode = def encode(self, /, encoding='utf-8', errors='strict'): Encode the string using the codec registered for encoding.       │
│     endswith = def endswith(...) S.endswith(suffix[, start[, end]]) -> bool                                                             │
│   expandtabs = def expandtabs(self, /, tabsize=8): Return a copy where all tab characters are expanded using spaces.                    │
│         find = def find(...) S.find(sub[, start[, end]]) -> int                                                                         │
│       format = def format(...) S.format(*args, **kwargs) -> str                                                                         │
│   format_map = def format_map(...) S.format_map(mapping) -> str                                                                         │
│        index = def index(...) S.index(sub[, start[, end]]) -> int                                                                       │
│      isalnum = def isalnum(self, /): Return True if the string is an alpha-numeric string, False otherwise.                             │
│      isalpha = def isalpha(self, /): Return True if the string is an alphabetic string, False otherwise.                                │
│      isascii = def isascii(self, /): Return True if all characters in the string are ASCII, False otherwise.                            │
│    isdecimal = def isdecimal(self, /): Return True if the string is a decimal string, False otherwise.                                  │
│      isdigit = def isdigit(self, /): Return True if the string is a digit string, False otherwise.                                      │
│ isidentifier = def isidentifier(self, /): Return True if the string is a valid Python identifier, False otherwise.                      │
│      islower = def islower(self, /): Return True if the string is a lowercase string, False otherwise.                                  │
│    isnumeric = def isnumeric(self, /): Return True if the string is a numeric string, False otherwise.                                  │
│  isprintable = def isprintable(self, /): Return True if the string is printable, False otherwise.                                       │
│      isspace = def isspace(self, /): Return True if the string is a whitespace string, False otherwise.                                 │
│      istitle = def istitle(self, /): Return True if the string is a title-cased string, False otherwise.                                │
│      isupper = def isupper(self, /): Return True if the string is an uppercase string, False otherwise.                                 │
│         join = def join(self, iterable, /): Concatenate any number of strings.                                                          │
│        ljust = def ljust(self, width, fillchar=' ', /): Return a left-justified string of length width.                                 │
│        lower = def lower(self, /): Return a copy of the string converted to lowercase.                                                  │
│       lstrip = def lstrip(self, chars=None, /): Return a copy of the string with leading whitespace removed.                            │
│    maketrans = def maketrans(...) Return a translation table usable for str.translate().                                                │
│    partition = def partition(self, sep, /): Partition the string into three parts using the given separator.                            │
│      replace = def replace(self, old, new, count=-1, /): Return a copy with all occurrences of substring old replaced by new.           │
│        rfind = def rfind(...) S.rfind(sub[, start[, end]]) -> int                                                                       │
│       rindex = def rindex(...) S.rindex(sub[, start[, end]]) -> int                                                                     │
│        rjust = def rjust(self, width, fillchar=' ', /): Return a right-justified string of length width.                                │
│   rpartition = def rpartition(self, sep, /): Partition the string into three parts using the given separator.                           │
│       rsplit = def rsplit(self, /, sep=None, maxsplit=-1): Return a list of the words in the string, using sep as the delimiter string. │
│       rstrip = def rstrip(self, chars=None, /): Return a copy of the string with trailing whitespace removed.                           │
│        split = def split(self, /, sep=None, maxsplit=-1): Return a list of the words in the string, using sep as the delimiter string.  │
│   splitlines = def splitlines(self, /, keepends=False): Return a list of the lines in the string, breaking at line boundaries.          │
│   startswith = def startswith(...) S.startswith(prefix[, start[, end]]) -> bool                                                         │
│        strip = def strip(self, chars=None, /): Return a copy of the string with leading and trailing whitespace removed.                │
│     swapcase = def swapcase(self, /): Convert uppercase characters to lowercase and lowercase characters to uppercase.                  │
│        title = def title(self, /): Return a version of the string where each word is titlecased.                                        │
│    translate = def translate(self, table, /): Replace each character in the string using the given translation table.                   │
│        upper = def upper(self, /): Return a copy of the string converted to uppercase.                                                  │
│        zfill = def zfill(self, width, /): Pad a numeric string with zeros on the left, to fill a field of the given width.              │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```


---
## Список (List)

---
### Список

Список:
* изменяемый упорядоченный тип данных.
* последовательность элементов, разделенных между собой запятой и заключенных в квадратные скобки.

```python
In [1]: list1 = [10, 20, 30, 77]
In [2]: list2 = ['one', 'dog', 'seven']
In [3]: list3 = [1, 20, 4.0, 'word']
```

---
### Список

Так как список - это упорядоченный тип данных, то, как и в строках, в списках
можно обращаться к элементу по номеру, делать срезы:
```python
In [4]: list3 = [1, 20, 4.0, 'word']

In [5]: list3[1]
Out[5]: 20

In [6]: list3[1::]
Out[6]: [20, 4.0, 'word']

In [7]: list3[-1]
Out[7]: 'word'

In [8]: list3[::-1]
Out[8]: ['word', 4.0, 20, 1]
```

---
### Список

Так как списки изменяемые, элементы списка можно менять:
```python
In [13]: list3
Out[13]: [1, 20, 4.0, 'word']

In [14]: list3[0] = 'test'

In [15]: list3
Out[15]: ['test', 20, 4.0, 'word']
```

---
### Список

Можно создавать и список списков. И, как и в обычном списке, можно обращаться к элементам во вложенных списках:
```python
interfaces = [
    ['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up'],
    ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'],
    ['FastEthernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']
]

In [17]: interfaces[0][0]
Out[17]: 'FastEthernet0/0'

In [18]: interfaces[2][0]
Out[18]: 'FastEthernet0/2'

In [19]: interfaces[2][1]
Out[19]: '10.0.2.1'
```

---
### Методы списков

```
In [2]: vlans = [1, 2, 3, 4, 10, 11, 12, 100]

In [3]: vlans.
               append()  count()   insert()  reverse()
               clear()   extend()  pop()     sort()
               copy()    index()   remove()
```

help(list)
```
 |  append(self, object, /)
 |      Append object to the end of the list.
 |
 |  clear(self, /)
 |      Remove all items from list.
 |
 |  copy(self, /)
 |      Return a shallow copy of the list.
 |
 |  count(self, value, /)
 |      Return number of occurrences of value.
 |
 |  extend(self, iterable, /)
 |      Extend list by appending elements from the iterable.
 |
 |  index(self, value, start=0, stop=2147483647, /)
 |      Return first index of value.
 |
 |      Raises ValueError if the value is not present.
 |
 |  insert(self, index, object, /)
 |      Insert object before index.
 |
 |  pop(self, index=-1, /)
 |      Remove and return item at index (default last).
 |
 |      Raises IndexError if list is empty or index is out of range.
 |
 |  remove(self, value, /)
 |      Remove first occurrence of value.
 |
 |      Raises ValueError if the value is not present.
 |
 |  reverse(self, /)
 |      Reverse *IN PLACE*.
 |
 |  sort(self, /, *, key=None, reverse=False)
 |      Sort the list in ascending order and return None.
 |
 |      The sort is in-place (i.e. the list itself is modified) and stable (i.e. the
 |      order of two equal elements is maintained).
 |
 |      If a key function is given, apply it once to each list item and sort them,
 |      ascending or descending, according to their function values.
 |
 |      The reverse flag can be set to sort in descending order.
 |
```

---
### Методы списков

```
In [26]: inspect(vlans, methods=True)
╭───────────────────────────────────────── <class 'list'> ──────────────────────────────────────────╮
│ Built-in mutable sequence.                                                                        │
│                                                                                                   │
│ ╭───────────────────────────────────────────────────────────────────────────────────────────────╮ │
│ │ [1, 2, 3, 4, 10, 11, 12, 100]                                                                 │ │
│ ╰───────────────────────────────────────────────────────────────────────────────────────────────╯ │
│                                                                                                   │
│  append = def append(object, /): Append object to the end of the list.                            │
│   clear = def clear(): Remove all items from list.                                                │
│    copy = def copy(): Return a shallow copy of the list.                                          │
│   count = def count(value, /): Return number of occurrences of value.                             │
│  extend = def extend(iterable, /): Extend list by appending elements from the iterable.           │
│   index = def index(value, start=0, stop=2147483647, /): Return first index of value.             │
│  insert = def insert(index, object, /): Insert object before index.                               │
│     pop = def pop(index=-1, /): Remove and return item at index (default last).                   │
│  remove = def remove(value, /): Remove first occurrence of value.                                 │
│ reverse = def reverse(): Reverse *IN PLACE*.                                                      │
│    sort = def sort(*, key=None, reverse=False): Sort the list in ascending order and return None. │
╰───────────────────────────────────────────────────────────────────────────────────────────────────╯
```

---
## Словарь (Dictionary)

---
### Словарь (ассоциативный массив, хеш-таблица)

* изменяемый упорядоченный тип данных 
* данные в словаре - это пары ```ключ: значение```
* доступ к значениям осуществляется по ключу, а не по номеру, как в списках
* словари упорядочены в порядке добавления данных
* так как словари изменяемы, то элементы словаря можно менять, добавлять, удалять
* ключ должен быть объектом неизменяемого типа:
 * число
 * строка
 * кортеж
* значение может быть данными любого типа

---
### Словарь

Пример словаря:
```python
london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

london = {
        'id': 1,
        'name': 'London',
        'it_vlan': 320,
        'user_vlan': 1010,
        'mngmt_vlan': 99,
        'to_name': None,
        'to_id': None,
        'port': 'G1/0/11',
}
```

---
### Словарь

Для того, чтобы получить значение из словаря, надо обратиться по ключу, таким же образом, как это было в списках, только вместо номера будет использоваться ключ:
```python
In [1]: london = {'name': 'London1', 'location': 'London Str'}

In [2]: london['name']
Out[2]: 'London1'

In [3]: london['location']
Out[3]: 'London Str'
```

---
### Словарь

Аналогичным образом можно добавить новую пару ключ:значение:
```python
In [4]: london['vendor'] = 'Cisco'

In [5]: print(london)
{'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}
```

---
### Словарь

В словаре в качестве значения можно использовать словарь:
```python
london_co = {
    'r1': {'hostname': 'london_r1',
           'ios': '15.4',
           'ip': '10.255.0.1',
           'location': '21 New Globe Walk',
           'model': '4451',
           'vendor': 'Cisco'},
    'r2': {'hostname': 'london_r2',
           'ios': '15.4',
           'ip': '10.255.0.2',
           'location': '21 New Globe Walk',
           'model': '4451',
           'vendor': 'Cisco'},
    'sw1': {'hostname': 'london_sw1',
            'ios': '3.6.XE',
            'ip': '10.255.0.101',
            'location': '21 New Globe Walk',
            'model': '3850',
            'vendor': 'Cisco'},
}
```

---
### Словарь

Получить значения из вложенного словаря можно так:
```python
In [7]: london_co['r1']['ios']
Out[7]: '15.4'

In [8]: london_co['r1']['model']
Out[8]: '4451'

In [9]: london_co['sw1']['ip']
Out[9]: '10.255.0.101'
```

---
### Методы cловаря


```
In [28]: dict.
               clear()      get()        mro()        setdefault()
               copy()       items()      pop()        update()
               fromkeys()   keys()       popitem()    values()
```

---
### Методы cловаря


```
In [27]: inspect(dict, methods=True)
╭─────────────────────────────────────────────────────── <class 'dict'> ───────────────────────────────────────────────────────╮
│ def dict(...)                                                                                                                │
│                                                                                                                              │
│ dict() -> new empty dictionary                                                                                               │
│ dict(mapping) -> new dictionary initialized from a mapping object's                                                          │
│     (key, value) pairs                                                                                                       │
│ dict(iterable) -> new dictionary initialized as if via:                                                                      │
│     d = {}                                                                                                                   │
│     for k, v in iterable:                                                                                                    │
│         d[k] = v                                                                                                             │
│ dict(**kwargs) -> new dictionary initialized with the name=value pairs                                                       │
│     in the keyword argument list.  For example:  dict(one=1, two=2)                                                          │
│                                                                                                                              │
│      clear = def clear(...) D.clear() -> None.  Remove all items from D.                                                     │
│       copy = def copy(...) D.copy() -> a shallow copy of D                                                                   │
│   fromkeys = def fromkeys(iterable, value=None, /): Create a new dictionary with keys from iterable and values set to value. │
│        get = def get(self, key, default=None, /): Return the value for key if key is in the dictionary, else default.        │
│      items = def items(...) D.items() -> a set-like object providing a view on D's items                                     │
│       keys = def keys(...) D.keys() -> a set-like object providing a view on D's keys                                        │
│        pop = def pop(...)                                                                                                    │
│              D.pop(k[,d]) -> v, remove specified key and return the corresponding value.                                     │
│              If key is not found, d is returned if given, otherwise KeyError is raised                                       │
│    popitem = def popitem(self, /): Remove and return a (key, value) pair as a 2-tuple.                                       │
│ setdefault = def setdefault(self, key, default=None, /): Insert key with a value of default if key is not in the dictionary. │
│     update = def update(...)                                                                                                 │
│              D.update([E, ]**F) -> None.  Update D from dict/iterable E and F.                                               │
│              If E is present and has a .keys() method, then does:  for k in E: D[k] = E[k]                                   │
│              If E is present and lacks a .keys() method, then does:  for k, v in E: D[k] = v                                 │
│              In either case, this is followed by: for k in F:  D[k] = F[k]                                                   │
│     values = def values(...) D.values() -> an object providing a view on D's values                                          │
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

---
## Кортеж (Tuple)

---
### Кортеж

Кортеж - это неизменяемый упорядоченный тип данных.

Кортеж в Python - это последовательность элементов, которые разделены между собой запятой и заключены в скобки.

Грубо говоря, кортеж - это список, который нельзя изменить. То есть, в кортеже есть только права на чтение. Это может быть защитой от случайных изменений.

---
### Кортеж

```python
tuple_keys = ('hostname', 'location', 'vendor', 'model', 'ios', 'ip')
```

Кортеж из одного элемента (обратите внимание на запятую):
```python
In [3]: tuple2 = ('password',)
```

---
### Кортеж

Кортеж из списка:
```python
In [4]: list_keys = ['hostname', 'location', 'vendor', 'model', 'ios', 'ip']

In [5]: tuple_keys = tuple(list_keys)

In [6]: tuple_keys
Out[6]: ('hostname', 'location', 'vendor', 'model', 'ios', 'ip')
```

К объектам в кортеже можно обращаться, как и к объектам списка, по порядковому номеру:
```python
In [7]: tuple_keys[0]
Out[7]: 'hostname'
```

---
### Кортеж

Но так как кортеж неизменяем, присвоить новое значение нельзя:
```python
In [8]: tuple_keys[1] = 'test'
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-9-1c7162cdefa3> in <module>()
----> 1 tuple_keys[1] = 'test'

TypeError: 'tuple' object does not support item assignment
```

---
### Методы кортежа

```
In [35]: inspect(tuple, methods=True)
╭───────────────────────────────────── <class 'tuple'> ─────────────────────────────────────╮
│ def tuple(iterable=(), /):                                                                │
│                                                                                           │
│ Built-in immutable sequence.                                                              │
│                                                                                           │
│ count = def count(self, value, /): Return number of occurrences of value.                 │
│ index = def index(self, value, start=0, stop=2147483647, /): Return first index of value. │
╰───────────────────────────────────────────────────────────────────────────────────────────╯
```

---
## Множество (Set)

---
### Множество

Множество - это изменяемый неупорядоченный тип данных. В множестве всегда содержатся только уникальные элементы.

Множество в Python - это последовательность элементов, которые разделены между собой запятой и заключены в фигурные скобки.

```python
items = {40, 100, 10, 20, 30}
```

---
### Множество

С помощью множества можно легко убрать повторяющиеся элементы:
```python
In [1]: vlans = [10, 20, 30, 40, 100, 10]

In [2]: set(vlans)
Out[2]: {10, 20, 30, 40, 100}

In [3]: set1 = set(vlans)

In [4]: print(set1)
{40, 100, 10, 20, 30}
```

---
### Методы множеств

```
In [36]: inspect(set, methods=True)
╭─────────────────────────────────────────────────────────── <class 'set'> ────────────────────────────────────────────────────────────╮
│ def set(...)                                                                                                                         │
│                                                                                                                                      │
│ set() -> new empty set object                                                                                                        │
│ set(iterable) -> new set object                                                                                                      │
│                                                                                                                                      │
│                         add = def add(...) Add an element to a set.                                                                  │
│                      update = def update(...) Update a set with the union of itself and others.                                      │
│                      remove = def remove(...) Remove an element from a set; it must be a member.                                     │
│                     discard = def discard(...) Remove an element from a set if it is a member.                                       │
│                         pop = def pop(...)                                                                                           │
│                               Remove and return an arbitrary set element.                                                            │
│                               Raises KeyError if the set is empty.                                                                   │
│                       clear = def clear(...) Remove all elements from this set.                                                      │
│                        copy = def copy(...) Return a shallow copy of a set.                                                          │
│                                                                                                                                      │
│                  difference = def difference(...) Return the difference of two or more sets as a new set.                            │
│           difference_update = def difference_update(...) Remove all elements of another set from this set.                           │
│                intersection = def intersection(...) Return the intersection of two sets as a new set.                                │
│         intersection_update = def intersection_update(...) Update a set with the intersection of itself and another.                 │
│                  isdisjoint = def isdisjoint(...) Return True if two sets have a null intersection.                                  │
│                    issubset = def issubset(...) Report whether another set contains this set.                                        │
│                  issuperset = def issuperset(...) Report whether this set contains another set.                                      │
│        symmetric_difference = def symmetric_difference(...) Return the symmetric difference of two sets as a new set.                │
│ symmetric_difference_update = def symmetric_difference_update(...) Update a set with the symmetric difference of itself and another. │
│                       union = def union(...) Return the union of sets as a new set.                                                  │
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```


---
### Операции с множествами

Множества полезны тем, что с ними можно делать различные операции и находить объединение множеств, пересечение и так далее.

```
                 Union                              Intersection
              Объединение                            Пересечение
                   _________                              _________
           _______|#########|                     _______|__       |
          |#######|##|######|                    |       |##|      |
          |#######|##|######|                    |       |##|      |
          |#######|##|######|                    |       |##|      |
          |#######|##|######|                    |_______|##|      |
                  |#########|                            |_________|
          
          
                 Difference                      Symmetric difference
                 Разность                       Симметрическая разность
                   _________                              _________
           _______|__       |                     _______|#########|
          |#######|  |      |                    |#######|  |######|
          |#######|  |      |                    |#######|  |######|
          |#######|  |      |                    |#######|  |######|
          |#######|__|      |                    |#######|__|######|
                  |_________|                            |#########|
```

---
### Операции с множествами

Объединение множеств можно получить с помощью метода __```union()```__ или оператора __```|```__:
```python
In [1]: vlans1 = {10, 20, 30, 50, 100}
In [2]: vlans2 = {100, 101, 102, 102, 200}

In [3]: vlans1.union(vlans2)
Out[3]: {10, 20, 30, 50, 100, 101, 102, 200}

In [4]: vlans1 | vlans2
Out[4]: {10, 20, 30, 50, 100, 101, 102, 200}
```

---
### Операции с множествами

Пересечение множеств можно получить с помощью метода __```intersection()```__ или оператора __```&```__:
```python
In [5]: vlans1 = {10, 20, 30, 50, 100}
In [6]: vlans2 = {100, 101, 102, 102, 200}

In [7]: vlans1.intersection(vlans2)
Out[7]: {100}

In [8]: vlans1 & vlans2
Out[8]: {100}
```


---
## Преобразование типов

---
## Преобразование типов

В Python есть несколько полезных встроенных функций, которые позволяют преобразовать данные из одного типа в другой.

---
### ```int()```

```int()``` - преобразует строку в int:
```python
In [1]: int("10")
Out[1]: 10
```

С помощью функции int можно преобразовать и число в двоичной записи в десятичную (двоичная запись должна быть в виде строки) 
```python
In [2]: int("11111111", 2)
Out[2]: 255
```

---
### ```bin()```

Преобразовать десятичное число в двоичный формат можно с помощью ```bin()```:
```python
In [3]: bin(10)
Out[3]: '0b1010'

In [4]: bin(255)
Out[4]: '0b11111111'
```

---
### ```hex()```

Аналогичная функция есть и для преобразования в шестнадцатеричный формат:
```python
In [5]: hex(10)
Out[5]: '0xa'

In [6]: hex(255)
Out[6]: '0xff'
```

---
### ```list()```

Функция ```list()``` преобразует аргумент в список: 
```python
In [7]: list("string")
Out[7]: ['s', 't', 'r', 'i', 'n', 'g']

In [8]: list({1,2,3})
Out[8]: [1, 2, 3]

In [9]: list((1,2,3,4))
Out[9]: [1, 2, 3, 4]
```

---
### ```set()```

Функция ```set()``` преобразует аргумент в множество: 
```python
In [10]: set([1, 2, 3, 3, 4, 4, 4, 4])
Out[10]: {1, 2, 3, 4}

In [11]: set((1, 2, 3, 3, 4, 4, 4, 4))
Out[11]: {1, 2, 3, 4}

In [12]: set("string string")
Out[12]: {' ', 'g', 'i', 'n', 'r', 's', 't'}
```

Эта функция очень полезна, когда нужно получить уникальные элементы в последовательности.

---
### ```tuple()```

Функция ```tuple()``` преобразует аргумент в кортеж: 
```python
In [13]: tuple([1, 2, 3, 4])
Out[13]: (1, 2, 3, 4)

In [14]: tuple({1, 2, 3, 4})
Out[14]: (1, 2, 3, 4)

In [15]: tuple("string")
Out[15]: ('s', 't', 'r', 'i', 'n', 'g')
```

Это может пригодиться в том случае, если нужно получить неизменяемый объект.

---
### ```str()```

Функция ```str()``` преобразует аргумент в строку: 
```python
In [16]: str(10)
Out[16]: '10'
```


---
## Форматирование строк

---
### Форматирование строк

Два варианта форматирования строк:
* с оператором ```%``` (более старый вариант)
* методом ```format()``` (новый вариант)
* f-строки - новый вариант, который появился в Python 3.6. 


---
### Форматирование строк с методом format

Пример использования метода format:
```python
In [1]: "interface FastEthernet0/{}".format('1')
Out[1]: 'interface FastEthernet0/1'
```

Специальный символ ```{}``` указывает, что сюда подставится значение, которое передается методу format.
При этом, каждая пара фигурных скобок обозначает одно место для подстановки.

---
### Форматирование строк

С помощью форматирования строк можно выводить результат столбцами.
В форматировании строк можно указывать, какое количество символов выделено на данные.
Если количество символов в данных меньше, чем выделенное количество символов, недостающие символы заполняются пробелами.

---
### Форматирование строк

Например, таким образом можно вывести данные столбцами одинаковой ширины по 15 символов с выравниванием по правой стороне:
```python
In [3]: vlan, mac, intf = ['100', 'aabb.cc80.7000', 'Gi0/1']

In [4]: print("{:>15} {:>15} {:>15}".format(vlan, mac, intf))
            100  aabb.cc80.7000           Gi0/1
```


---
### Форматирование строк

Шаблон для вывода может быть и многострочным:
```python
In [6]: ip_template = '''
   ...: ip address:
   ...: {}
   ...: '''

In [7]: print(ip_template.format('10.1.1.1'))

ip address:
10.1.1.1
```


---
### Форматирование строк

С помощью форматирования строк можно конвертировать числа в двоичный формат:
```python
In [11]: '{:b} {:b} {:b} {:b}'.format(192, 100, 1, 1)
Out[11]: '11000000 1100100 1 1'
```

При этом по-прежнему можно указывать дополнительные параметры, например, ширину столбца:
```python
In [12]: '{:8b} {:8b} {:8b} {:8b}'.format(192, 100, 1, 1)
Out[12]: '11000000  1100100        1        1'
```

А также можно указать, что надо дополнить числа нулями, вместо пробелов:
```python
In [13]: '{:08b} {:08b} {:08b} {:08b}'.format(192, 100, 1, 1)
Out[13]: '11000000 01100100 00000001 00000001'
```

