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


---
## Примеры декораторов классов

```python
CLASS_MAPPER_BASE = {}

def register_class(cls):
    CLASS_MAPPER_BASE[cls.device_type] = cls.__name__
    return cls


@register_class
class CiscoSSH:
    device_type = 'cisco_ios'
    def __init__(self, ip, username, password):
        pass


@register_class
class JuniperSSH:
    device_type = 'juniper'
    def __init__(self, ip, username, password):
        pass
```


---
## Примеры декораторов классов
### Декоратор добавляет метод pprint

```python
from rich import print as rprint


def add_pprint(cls):
    def pprint(self, methods=False):
        methods_class_attrs = vars(type(self))
        methods = {
            name: method
            for name, method in methods_class_attrs.items()
            if not name.startswith("__") and callable(method)
        }
        self_attrs = vars(self)
        rprint(self_attrs)
        if methods:
            rprint(methods)

    cls.pprint = pprint
    return cls
```

---
## Примеры декораторов классов
### Декоратор добавляет метод pprint

```python
@add_pprint
class IPv4Address:
    def __init__(self, ip):
        self.ip = ip
        self._int_ip = int(ip_address(ip))

    def as_int(self):
        return self._int_ip


In [15]: ip1 = IPv4Address("10.1.1.1")

In [16]: ip1.pprint()
{'ip': '10.1.1.1', '_int_ip': 167837953}
{
    'as_int': <function IPv4Address.as_int at 0xb4235df0>,
    'pprint': <function add_pprint.<locals>.pprint at 0xb4235388>
}

In [17]: ip1.pprint(methods=True)
{'ip': '10.1.1.1', '_int_ip': 167837953}
{
    'as_int': <function IPv4Address.as_int at 0xb4235df0>,
    'pprint': <function add_pprint.<locals>.pprint at 0xb4235388>
}
```

---
## Примеры декораторов классов

```python
def create_init(cls):
    attrs = list(cls.__annotations__.keys())
    init = f"def __init__(self, {', '.join(attrs)}):\n"
    for attr in attrs:
        init += f"    self.{attr} = {attr}\n"
    print(init)
    exec(init)
    init_method = locals()["__init__"]
    return init_method


def my_dataclass(cls):
    print("Декорируем класс")
    cls.__init__ = create_init(cls)
    return cls


@my_dataclass
class IPAddress:
    ip: str
    mask: int
```

---
## Класс как декоратор 
### Декоратор без аргументов

```python
class verbose:
    def __init__(self, func):
        print(f"Декорация функции {func.__name__}")
        self.func = func

    def __call__(self, *args, **kwargs):
        print(f"У функции {self.func.__name__} такие аргументы")
        print(f"{args=}")
        print(f"{kwargs=}")
        result = self.func(*args, **kwargs)
        return result


@verbose
def upper(string):
    return string.upper()
```

---
## Класс как декоратор 
### Декоратор с аргументами

```python
from functools import update_wrapper

class verbose:
    def __init__(self, message):
        print("вызов verbose")
        self.msg = message

    def __call__(self, func):
        print(f"Декорация функции {func.__name__}")
        def inner(*args, **kwargs):
            print(f"У функции {func.__name__} такие аргументы")
            print(f"{args=}")
            print(f"{kwargs=}")
            result = func(*args, **kwargs)

        update_wrapper(wrapper=inner, wrapped=func)
        return inner


@verbose("hello")
def upper(string):
    return string.upper()
```

---
## Примеры декораторов

* [scrapli ChannelTimeout](https://github.com/carlmontanari/scrapli/blob/master/scrapli/decorators.py#L193)
* [click](https://github.com/pallets/click/blob/main/src/click/decorators.py)
* [backoff](https://github.com/litl/backoff/blob/master/backoff/_decorator.py)
* [functools.lru_cache](https://github.com/python/cpython/blob/3.10/Lib/functools.py#L479)
* [dataclesses.dataclass](https://github.com/python/cpython/blob/3.10/Lib/dataclasses.py#L1150)
