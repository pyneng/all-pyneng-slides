## more-itertools

---
## Группировка

---
## chunked

Разбивает итерируемый объект на списки указанной длины:

```python
more_itertools.chunked(iterable, n)
```

Пример:

```python
In [6]: list(more_itertools.chunked(data, 2))
Out[6]: [[1, 2], [3, 4], [5, 6], [7]]

In [7]: list(more_itertools.chunked(data, 3))
Out[7]: [[1, 2, 3], [4, 5, 6], [7]]
```

---
## divide

Разбивает итерируемый объект на n частей:

```python
more_itertools.divide(n, iterable)
```

Пример:

```python
In [25]: data
Out[25]: [1, 2, 3, 4, 5, 6, 7]

In [26]: g1, g2, g3 = more_itertools.divide(3, data)

In [27]: list(g1)
Out[27]: [1, 2, 3]

In [28]: list(g2)
Out[28]: [4, 5]

In [29]: list(g3)
Out[29]: [6, 7]
```

---
## split_at

Генерирует списки элементов из итерируемого объекта, где каждый список разделен
тем значением, для которого pred возвращает True (разедлитель не включен). 

```python
more_itertools.split_at(iterable, pred)
```

Пример:

```python
import time

def file_gen(filename):
    with open(filename) as f:
        for idx, line in enumerate(f):
            print(idx)
            yield line

f = file_gen('sh_cdp_neighbors_detail.txt')
for items in more_itertools.split_at(f, lambda x: '------' in x):
    print(items)
    time.sleep(2)
```

---
## unzip

Выполняет операцию противоположную zip:

```python
more_itertools.unzip(iterable)
```

Пример:

```python
In [2]: data = [('status', '*'),
   ...:         ('network', '1.23.78.0'),
   ...:         ('netmask', '24'),
   ...:         ('nexthop', '200.219.145.45'),
   ...:         ('metric', 'NA'),
   ...:         ('locprf', 'NA'),
   ...:         ('weight', '0'),
   ...:         ('path', '28135 18881 3549 6453 4755 45528'),
   ...:         ('origin', 'i')]

In [3]: headers, values = more_itertools.unzip(data)

In [4]: list(headers)
Out[4]:
['status',
 'network',
 'netmask',
 'nexthop',
 'metric',
 'locprf',
 'weight',
 'path',
 'origin']

In [5]: list(values)
Out[5]:
['*',
 '1.23.78.0',
 '24',
 '200.219.145.45',
 'NA',
 'NA',
 '0',
 '28135 18881 3549 6453 4755 45528',
 'i']
```

---
## grouper

```python
more_itertools.grouper(iterable, n, fillvalue=None)
```

Пример:

```python
In [6]: data = [1, 2, 3, 4, 5, 6, 7]

In [8]: list(more_itertools.grouper(data, 3, 0))
Out[8]: [(1, 2, 3), (4, 5, 6), (7, 0, 0)]
```

---
## partition

```python
more_itertools.partition(pred, iterable)
```


Пример:

```python
In [10]: data = [1, 2, 'a', 'b', 5, 'c', 7]

In [15]: is_false, is_true = more_itertools.partition(lambda x: str(x).isdigit(), data)

In [16]: list(is_false)
Out[16]: ['a', 'b', 'c']

In [17]: list(is_true)
Out[17]: [1, 2, 5, 7]
```


---
## spy

```python
more_itertools.spy(iterable, n=1)
```

Пример

```python
def file_gen(filename):
    with open(filename) as f:
        for idx, line in enumerate(f):
            print(idx)
            yield line


In [20]: f = file_gen('sh_cdp_neighbors_detail.txt')

In [21]: f
Out[21]: <generator object file_gen at 0xb28bd4ec>

In [23]: first, f = more_itertools.spy(f)
0

In [24]: first
Out[24]: ['SW1#show cdp neighbors detail\n']

In [25]: f
Out[25]: <itertools.chain at 0xb38c184c>

In [26]: next(f)
Out[26]: 'SW1#show cdp neighbors detail\n'

```

---
## windowed

```python
more_itertools.windowed(seq, n, fillvalue=None, step=1)
```

```python
In [33]: windows = more_itertools.windowed(f, 5)

In [34]: for win in windows:
    ...:     print(win)
    ...:
0
1
2
3
4
('SW1#show cdp neighbors detail\n', '-------------------------\n', 'Device ID: SW2\n', 'Entry address(es):\n', '  IP address: 10.1.1.2\n')
5
('-------------------------\n', 'Device ID: SW2\n', 'Entry address(es):\n', '  IP address: 10.1.1.2\n', 'Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP\n')
6
('Device ID: SW2\n', 'Entry address(es):\n', '  IP address: 10.1.1.2\n', 'Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP\n', 'Interface: GigabitEthernet1/0/16,  Port ID (outgoing port): GigabitEthernet0/1\n')
7
('Entry address(es):\n', '  IP address: 10.1.1.2\n', 'Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP\n', 'Interface: GigabitEthernet1/0/16,  Port ID (outgoing port): GigabitEthernet0/1\n', 'Holdtime : 164 sec\n')
8
('  IP address: 10.1.1.2\n', 'Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP\n', 'Interface: GigabitEthernet1/0/16,  Port ID (outgoing port): GigabitEthernet0/1\n', 'Holdtime : 164 sec\n', '\n')
```


---
## collapse

```python
more_itertools.collapse(iterable, base_type=None, levels=None)
```

Пример

```python
In [37]: iterable = [(1, 2), ([3, 4], [[5], [6]])]

In [38]: list(more_itertools.collapse(iterable))
Out[38]: [1, 2, 3, 4, 5, 6]
```

---
## Агрегирование значений

---
## first и last

```python
more_itertools.first(iterable[, default])
more_itertools.last(iterable[, default])
```

```python
In [42]: data = [1, 2, 'a', 'b', 5, 'c', 7]

In [43]: more_itertools.first(data)
Out[43]: 1

In [44]: more_itertools.last(data)
Out[44]: 7
```

---
## all_equal

```python
more_itertools.all_equal(iterable)
```

```python
In [46]: more_itertools.all_equal([1, 1, 1])
Out[46]: True

In [47]: more_itertools.all_equal([1, 2, 1])
Out[47]: False
```
