## Itertools

---
## Itertools

### Infinite iterators

| Iterator | Arguments     | Example |
|----------|---------------|-----------------------------------------|
|count     |start, [step]  |`count(10) --> 10 11 12 13 14 ...`  |
|cycle     |p              |`cycle('ABCD') --> A B C D A B C D ...` |
|repeat    |elem [,n]      |`repeat(10, 3) --> 10 10 10` |

---
## Itertools
### Iterators terminating on the shortest input sequence

|Iterator              |Example |
|----------------------|------------------------------------------------------------|
|accumulate            |`accumulate([1,2,3,4,5]) --> 1 3 6 10 15` |
|chain                 |`chain('ABC', 'DEF') --> A B C D E F` |
|chain.from\_iterable  |`chain.from_iterable(['ABC', 'DEF']) --> A B C D E F` |
|compress              |`compress('ABCDEF', [1,0,1,0,1,1]) --> A C E F` |
|dropwhile             |`dropwhile(lambda x: x<5, [1,4,6,4,1]) --> 6 4 1` |
|filterfalse           |`filterfalse(lambda x: x%2, range(10)) --> 0 2 4 6 8` |
|groupby               | |
|islice                |`islice('ABCDEFG', 2, None) --> C D E F G` |
|pairwise              |`pairwise('ABCDEFG') --> AB BC CD DE EF FG` |
|starmap               |`starmap(pow, [(2,5), (3,2), (10,3)]) --> 32 9 1000` |
|takewhile             |`takewhile(lambda x: x<5, [1,4,6,4,1]) --> 1 4` |
|tee                   | |
|zip\_longest          |`zip_longest('ABCD', 'xy', fillvalue='-') --> Ax By C- D-` |

---
## Itertools
### Combinatoric iterators

|Iterator                         |Results |
|---------------------------------|--------------------------------------------------------------- |
|product                          |cartesian product, equivalent to a nested for-loop |
|permutations                     |r-length tuples, all possible orderings, no repeated elements |
|combinations                     |r-length tuples, in sorted order, no repeated elements |
|combinations\_with\_replacement  |r-length tuples, in sorted order, with repeated elements |

## 

|Examples                                    |Results |
|--------------------------------------------|--------------------------------------------------- |
|`product('ABCD', repeat=2)`                 |`AA AB AC AD BA BB BC BD CA CB CC CD DA DB DC DD` |
|`permutations('ABCD', 2)`                   |`AB AC AD BA BC BD CA CB CD DA DB DC` |
|`combinations('ABCD', 2)`                   |`AB AC AD BC BD CD` |
|`combinations_with_replacement('ABCD', 2)`  |`AA AB AC AD BB BC BD CC CD DD` |


---
## repeat

---
## repeat

Функция repeat возвращает итератор, который повторяет указанный объект
бесконечно или указанное количество раз:

```python

itertools.repeat(object[, times])
```

---
## repeat
Пример использования repeat для повторения команды:

```python
from itertools import repeat
from concurrent.futures import ThreadPoolExecutor

import netmiko
import yaml


def send_show(device, show):
    with netmiko.ConnectHandler(**device) as ssh:
        ssh.enable()
        result = ssh.send_command(show)
        return result


with open('devices.yaml') as f:
    devices = yaml.safe_load(f)

with ThreadPoolExecutor(max_workers=3) as executor:
    result = executor.map(send_show, devices, repeat('sh clock'))
    for device, output in zip(devices, result):
        print(device['ip'], output)
```

---
## cycle

---
## cycle

Функция cycle создает итератор, которые возвращает элементы итерируемого объекта по кругу:

```python

itertools.cycle(iterable)
```

Пример использования cycle:

```python
from itertools import cycle


spinner = it.cycle('\|/-')
for _ in range(20):
    print(f'\r{next(spinner)}', end='')
    time.sleep(0.5)
```

---
## count

---
## count

Функция count возвращает итератор, который генерирует числа бесконечно, начиная с указанного
в start и используя шаг step:

```python
itertools.count(start=0, step=1)
```

---
## Пример использования count

```python
from itertools import count


In [13]: ip_list
Out[13]:
['192.168.100.1',
 '192.168.100.2',
 '192.168.100.3',
 '192.168.100.4',
 '192.168.100.5']

In [18]: for num, ip in zip(count(1), ip_list):
    ...:     print((num, ip))
    ...:
(1, '192.168.100.1')
(2, '192.168.100.2')
(3, '192.168.100.3')
(4, '192.168.100.4')
(5, '192.168.100.5')
```

---
## zip_longest

---
## zip_longest


Функция zip_longest работает аналогично встроенной функции zip,
но не останавливается на самом коротком итерируемом объекте.

```python

itertools.zip_longest(*iterables, fillvalue=None)
```

---
## Пример использования zip_longest

```python
In [20]: list(zip([1,2,3,4,5], [10,20]))
Out[20]: [(1, 10), (2, 20)]

In [21]: list(zip_longest([1,2,3,4,5], [10,20]))
Out[21]: [(1, 10), (2, 20), (3, None), (4, None), (5, None)]

In [22]: list(zip_longest([1,2,3,4,5], [10,20], fillvalue=0))
Out[22]: [(1, 10), (2, 20), (3, 0), (4, 0), (5, 0)]
```

---
## chain

---
## chain

Функция chain ожидает несколько итерируемых объектов как аргумент и возвращает
единый итератор, который перебирает элементы каждого итерируемого объекта
так, как будто они составляют единый объект:

```python

itertools.chain(*iterables)
```

---
## Пример использования chain

```python
In [4]: line = 'test'

In [5]: items = [1, 2, 3]

In [6]: mapping = {'ios': '15.4', 'vendor': 'Cisco'}

In [7]: for item in chain(line, items, mapping):
   ...:     print(item)
   ...:
t
e
s
t
1
2
3
ios
vendor
```


---
## compress

---
## compress

Функция compress позволяет фильтровать данные: она возвращает те элементы из data, которые
соответветствуют истинному значению в selectors:

```python
itertools.compress(data, selectors)
```

---
## Пример использования compress для фильтрации полей с ненулевым значением

```python
In [9]: headers = ['tx_packets', 'rx_packets', 'tx_bytes', 'rx_bytes', 'broadcasts']

In [10]: data = [294785, 0, 22275381, 0, 253218]

In [12]: list(compress(headers, data))
Out[12]: ['tx_packets', 'tx_bytes', 'broadcasts']

In [14]: list(compress(zip(headers, data), data))
Out[14]: [('tx_packets', 294785), ('tx_bytes', 22275381), ('broadcasts', 253218)]

In [24]: dict(compress(zip(headers, data), data))
Out[24]: {'tx_packets': 294785, 'tx_bytes': 22275381, 'broadcasts': 253218}
```

---
## Пример фильтрации None с compress

```python
In [25]: data2
Out[25]: [294785, 0, 22275381, None, None]

In [26]: headers
Out[26]: ['tx_packets', 'rx_packets', 'tx_bytes', 'rx_bytes', 'broadcasts']

In [27]: list(compress(zip(headers, data2), selectors=map(lambda x: x != None, data2)))
Out[27]: [('tx_packets', 294785), ('rx_packets', 0), ('tx_bytes', 22275381)]
```


---
## tee

---
## tee

Функция tee создает несколько независимых итераторов на основе исходных данных:

```python
itertools.tee(iterable, n=2)
```

---
## Пример использования tee

```python
In [30]: data = [1,2,3,4,5,6]

In [31]: data_iter = iter(data)

In [32]: duplicate_1, duplicate_2 = tee(data_iter)

In [33]: list(duplicate_1)
Out[33]: [1, 2, 3, 4, 5, 6]

In [34]: list(duplicate_2)
Out[34]: [1, 2, 3, 4, 5, 6]
```

---
## Пример использования tee

Важная особенность tee - исходный итератор лучше не использовать,
иначе полученные итераторы начнут перебор не с начала:

```python
In [35]: data_iter = iter(data)

In [36]: duplicate_1, duplicate_2 = tee(data_iter)

In [37]: next(data_iter)
Out[37]: 1

In [38]: next(data_iter)
Out[38]: 2

In [39]: list(duplicate_1)
Out[39]: [3, 4, 5, 6]

In [40]: list(duplicate_2)
Out[40]: [3, 4, 5, 6]
```

---
## Пример использования tee

При этом перебор одной копии, не влияет на вторую:

```python

In [41]: data_iter = iter(data)

In [42]: duplicate_1, duplicate_2 = tee(data_iter)

In [43]: next(duplicate_1)
Out[43]: 1

In [44]: next(duplicate_1)
Out[44]: 2

In [45]: list(duplicate_1)
Out[45]: [3, 4, 5, 6]

In [46]: list(duplicate_2)
Out[46]: [1, 2, 3, 4, 5, 6]
```

---
## islice


---
## islice

Функция islice

```python
itertools.islice(iterable, stop)
itertools.islice(iterable, start, stop[, step])
```

Пример использования:

```python

In [59]: list(islice(range(100), 5))
Out[59]: [0, 1, 2, 3, 4]

In [60]: list(islice(range(100), 5, 10))
Out[60]: [5, 6, 7, 8, 9]

In [61]: list(islice(range(100), 5, 10, 2))
Out[61]: [5, 7, 9]

In [62]: list(islice(range(100), 5, 20, 2))
Out[62]: [5, 7, 9, 11, 13, 15, 17, 19]

In [63]: list(islice(range(100), 5, 20, 3))
Out[63]: [5, 8, 11, 14, 17]
```


---
## groupby

---
## groupby

Функция groupby

```python
itertools.groupby(iterable, key=None)
```

---
## Пример использования groupby

```python
from pprint import pprint
from dataclasses import dataclass
import operator

@dataclass(frozen=True)
class Book:
    title: str
    author: str


In [75]: books
Out[75]:
[Book(title='1984', author='George Orwell'),
 Book(title='The Martian Chronicles', author='Ray Bradbury'),
 Book(title='The Hobbit', author='J.R.R. Tolkien'),
 Book(title='Animal Farm', author='George Orwell'),
 Book(title='Fahrenheit 451', author='Ray Bradbury'),
 Book(title='The Lord of the Rings (1-3)', author='J.R.R. Tolkien'),
 Book(title='Harry Potter and the Sorcerer’s Stone', author='J.K. Rowling'),
 Book(title='To Kill a Mockingbird', author='Harper Lee')]
```

---
## Пример использования groupby

```python
In [83]: sorted_books = sorted(books, key=operator.attrgetter('author'))

In [84]: sorted_books
Out[84]:
[Book(title='1984', author='George Orwell'),
 Book(title='Animal Farm', author='George Orwell'),
 Book(title='To Kill a Mockingbird', author='Harper Lee'),
 Book(title='Harry Potter and the Sorcerer’s Stone', author='J.K. Rowling'),
 Book(title='The Hobbit', author='J.R.R. Tolkien'),
 Book(title='The Lord of the Rings (1-3)', author='J.R.R. Tolkien'),
 Book(title='The Martian Chronicles', author='Ray Bradbury'),
 Book(title='Fahrenheit 451', author='Ray Bradbury')]

```

---
## Пример использования groupby

```python
In [85]: for key, item in groupby(sorted_books, operator.attrgetter('author')):
    ...:     print(key.ljust(20), list(item))
    ...:
George Orwell        [Book(title='1984', author='George Orwell'), Book(title='Animal Farm', author='George Orwell')]
Harper Lee           [Book(title='To Kill a Mockingbird', author='Harper Lee')]
J.K. Rowling         [Book(title='Harry Potter and the Sorcerer’s Stone', author='J.K. Rowling')]
J.R.R. Tolkien       [Book(title='The Hobbit', author='J.R.R. Tolkien'), Book(title='The Lord of the Rings (1-3)', author='J.R.R. Tolkien')]
Ray Bradbury         [Book(title='The Martian Chronicles', author='Ray Bradbury'), Book(title='Fahrenheit 451', author='Ray Bradbury')]

In [86]: books_by_author = {}

In [87]: for key, item in groupby(sorted_books, operator.attrgetter('author')):
    ...:     books_by_author[key] = list(item)
    ...:

In [90]: pprint(books_by_author)
{'George Orwell': [Book(title='1984', author='George Orwell'),
                   Book(title='Animal Farm', author='George Orwell')],
 'Harper Lee': [Book(title='To Kill a Mockingbird', author='Harper Lee')],
 'J.K. Rowling': [Book(title='Harry Potter and the Sorcerer’s Stone', author='J.K. Rowling')],
 'J.R.R. Tolkien': [Book(title='The Hobbit', author='J.R.R. Tolkien'),
                    Book(title='The Lord of the Rings (1-3)', author='J.R.R. Tolkien')],
 'Ray Bradbury': [Book(title='The Martian Chronicles', author='Ray Bradbury'),
                  Book(title='Fahrenheit 451', author='Ray Bradbury')]}
```

---
## dropwhile и takewhile

---
## dropwhile и takewhile

Функция dropwhile ожидает как аргументы функцию, которая возвращает True или False, в зависимости от условия, и итерируемый объект.
Функция dropwhile отбрасывает элементы итерируемого объекта до тех пор, пока функция переданная как аргумент возвращает True.
Как только dropwhile встречает False, он возвращает итератор с оставшимися объектами.

```python
In [1]: from itertools import dropwhile

In [2]: list(dropwhile(lambda x: x < 5, [0,2,3,5,10,2,3]))
Out[2]: [5, 10, 2, 3]
```

Функция takewhile - противоположность функции dropwhile: она возвращает итератор
с теми элементами, которые соответствуют условию, до первого ложного условия:

```python
In [3]: from itertools import takewhile

In [4]: list(takewhile(lambda x: x < 5, [0,2,3,5,10,2,3]))
Out[4]: [0, 2, 3]
```

---
## Пример использования takewhile и dropwhile

```python
def get_cdp_neighbor(sh_cdp_neighbor_detail):
    with open(sh_cdp_neighbor_detail) as f:
        while True:
            begin = dropwhile(lambda x: not 'Device ID' in x, f)
            lines = takewhile(lambda y: not '-----' in y, begin)
            neighbor = ''.join(lines)
            if not neighbor:
                return
            yield neighbor
```


Файл parse_cdp_file.py:

```python
import re
from pprint import pprint
from itertools import dropwhile, takewhile


def get_cdp_neighbor(sh_cdp_neighbor_detail):
    with open(sh_cdp_neighbor_detail) as f:
        while True:
            f = dropwhile(lambda x: not 'Device ID' in x, f)
            lines = takewhile(lambda y: not '-----' in y, f)
            neighbor = ''.join(lines)
            if not neighbor:
                return None
            yield neighbor


def parse_cdp_neighbor(output):
    regex = ('Device ID: (\S+)\n.*?'
             ' +IP address: (?P<ip>\S+).+?'
             'Platform: (?P<platform>\S+ \S+),.+?'
             'Version (?P<ios>\S+),')

    result = {}
    match = re.search(regex, output, re.DOTALL)
    if match:
        device = match.group(1)
        result[device] = match.groupdict()
    return result


def parse_cdp_output(filename):
    result = get_cdp_neighbor(filename)
    all_cdp = {}
    for neighbor in result:
        all_cdp.update(parse_cdp_neighbor(neighbor))
    return all_cdp


if __name__ == "__main__":
    filename = 'sh_cdp_neighbors_detail.txt'
    pprint(parse_cdp_output(filename), width=120)
```


Результат:

```python
$ python parse_cdp_file.py
{'R1': {'ios': '12.4(24)T1', 'ip': '10.1.1.1', 'platform': 'Cisco 3825'},
 'R2': {'ios': '15.2(2)T1', 'ip': '10.2.2.2', 'platform': 'Cisco 2911'},
 'R3': {'ios': '15.2(2)T1', 'ip': '10.3.3.3', 'platform': 'Cisco 2911'},
 'SW2': {'ios': '12.2(55)SE9', 'ip': '10.1.1.2', 'platform': 'cisco WS-C2960-8TC-L'}}
```
