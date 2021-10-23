# Декораторы классов

---
## Примеры декораторов классов
### dataclasses.dataclass

```python
@dataclass
class IPAddress:
    ip: str
    mask: int


In [12]: ip1 = IPAddress('10.1.1.1', 28)

In [13]: ip1
Out[13]: IPAddress(ip='10.1.1.1', mask=28)
```

---
## Примеры декораторов классов
### functools.total_ordering

```python
In [1]: from functools import total_ordering

In [2]: @total_ordering
   ...: class IPv4Address:
   ...:     def __init__(self, ip):
   ...:         self.ip = ip
   ...:
...
ValueError: must define at least one ordering operation: < > <= >=
```

---
## Примеры декораторов классов
### functools.total_ordering

```python
class IPv4Address:
    def __init__(self, ip):
        self.ip = ip
        self._int_ip = int(ip_address(ip))

    def __eq__(self, other):
        return self._int_ip == other._int_ip

    def __lt__(self, other):
        return self._int_ip < other._int_ip


In [10]: ip1 = IPv4Address("10.1.1.1")
In [11]: ip2 = IPv4Address("10.1.10.1")

In [12]: ip1 < ip2
Out[12]: True

In [13]: ip1 <= ip2
---------------------------------------------------------------------------
...
TypeError: '<=' not supported between instances of 'IPv4Address' and 'IPv4Address'


In [14]: vars(IPv4Address)
Out[14]:
mappingproxy({'__module__': '__main__',
              '__init__': <function __main__.IPv4Address.__init__(self, ip)>,
              '__eq__': <function __main__.IPv4Address.__eq__(self, other)>,
              '__lt__': <function __main__.IPv4Address.__lt__(self, other)>,
              '__dict__': <attribute '__dict__' of 'IPv4Address' objects>,
              '__weakref__': <attribute '__weakref__' of 'IPv4Address' objects>,
              '__doc__': None,
              '__hash__': None})
```

---
## Примеры декораторов классов
### functools.total_ordering

```python
@total_ordering
class IPv4Address:
    def __init__(self, ip):
        self.ip = ip
        self._int_ip = int(ip_address(ip))

    def __eq__(self, other):
        return self._int_ip == other._int_ip

    def __lt__(self, other):
        return self._int_ip < other._int_ip

In [15]: ip1 < ip2
Out[15]: True

In [16]: ip1 <= ip2
Out[16]: True

In [17]: ip1 >= ip2
Out[17]: False

In [18]: vars(IPv4Address)
Out[18]:
mappingproxy({'__module__': '__main__',
              '__init__': <function __main__.IPv4Address.__init__(self, ip)>,
              '__eq__': <function __main__.IPv4Address.__eq__(self, other)>,
              '__lt__': <function __main__.IPv4Address.__lt__(self, other)>,
              '__dict__': <attribute '__dict__' of 'IPv4Address' objects>,
              '__weakref__': <attribute '__weakref__' of 'IPv4Address' objects>,
              '__doc__': None,
              '__hash__': None,
              '__gt__': <function functools._gt_from_lt(self, other, NotImplemented=NotImplemented)>,
              '__le__': <function functools._le_from_lt(self, other, NotImplemented=NotImplemented)>,
              '__ge__': <function functools._ge_from_lt(self, other, NotImplemented=NotImplemented)>})
```
