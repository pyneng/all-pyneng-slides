## Dataclass

---
### Dataclass

Data classes во многом похожи на именованные кортежи,
но имеют более широкие возможности. Например, атрибуты класса
могут быть изменяемые.

Часто в Python необходимо создавать классы в которых указаны только несколько переменных.
При этом, для реализации таких операций как сравнение экземпляров класса требуется создать
несколько специальных методов, добавить сюда строковое представление объекта
и для создания довольно простого класса, требуется много кода.

Data classes это новый функционал, он входит в стандартную бибилиотеку  начиная с Python 3.7.
Для предыдущих версий надо ставить отдельный модуль dataclasses или использовать сторонний
типа модуля attr.

---
### Dataclass

```python
from dataclasses import dataclass

@dataclass
class IPAddress:
    ip: str
    mask: int


In [12]: ip1 = IPAddress('10.1.1.1', 28)

In [13]: ip1
Out[13]: IPAddress(ip='10.1.1.1', mask=28)
```

---
### Dataclass

```python
@dataclass(order=True)
class IPAddress:
    ip: str
    mask: int


In [12]: ip1 = IPAddress('10.1.1.1', 28)

In [14]: ip1 == ip2
Out[14]: False

In [15]: ip1 < ip2
Out[15]: True
```

---
### Dataclass

scrapli/scrapli/transport/base/base_transport.py

```python
@dataclass()
class BaseTransportArgs:
    transport_options: Dict[str, Any]
    host: str
    port: int = 22
    timeout_socket: float = 10.0
    timeout_transport: float = 30.0
    logging_uid: str = ""
```

scrapli/scrapli/transport/plugins/system/transport.py 
```python
from scrapli.transport.base import BasePluginTransportArgs

@dataclass()
class PluginTransportArgs(BasePluginTransportArgs):
    auth_username: str
    auth_private_key: str = ""
    auth_strict_key: bool = True
    ssh_config_file: str = ""
    ssh_known_hosts_file: str = ""
```

---
### Dataclass - генераторы кода

```python
from functools import total_ordering

@total_ordering
class IPAddress:
    def __init__(self, ip):
        self.ip = ip

    def __str__(self):
        return self.ip

    def __repr__(self):
        return f"{type(self).__name__}('{self.ip}')"

    def __lt__(self, other):
        if not isinstance(other, IPAddress):
            raise TypeError(
                f"'<' not supported between instances of "
                f"'{type(self).__name__}' and '{type(other).__name__}'"
            )
        return int(self) < int(other)

    def __eq__(self, other):
        if not isinstance(other, IPAddress):
            raise TypeError(
                f"'+' not supported between instances of "
                f"'{type(self).__name__}' and '{type(other).__name__}'"
            )
        return int(self) == int(other)

    def __int__(self):
        bin_ip = "".join([
            f"{int(octet):08b}" for octet in self.ip.split(".")
        ])
        return int(bin_ip, 2)
```


---
### Dataclass - генераторы кода

```python
from dataclasses import dataclass, field


@dataclass(order=True)
class IPAddress:
    ip: str = field(compare=False)
    _ip: int = field(init=False, repr=False)
    mask: int

    def __post_init__(self):
        self._ip = int(self)

    def __int__(self):
        bin_ip = "".join([
            f"{int(octet):08b}" for octet in self.ip.split(".")
        ])
        return int(bin_ip, 2)
```

---
### сгенерированный код

```python
from dataclasses import dataclass
@dataclass
class Color:
    hue: int
    saturation: float
    lightness: float = 0.5
```

---
### сгенерированный код

```python
from dataclasses import Field, _MISSING_TYPE, _DataclassParams

class Color:
    'Color(hue: int, saturation: float, lightness: float = 0.5)'
    def __init__(self, hue: int, saturation: float, lightness: float = 0.5) -> None:
        self.hue=hue
        self.saturation=saturation
        self.lightness=lightness

    def __repr__(self):
        return (self.__class__.__qualname__ +
                f"(hue={self.hue!r}, saturation={self.saturation!r}, "
                f"lightness={self.lightness!r})")

    def __eq__(self,other):
        if other.__class__ is self.__class__:
            return (self.hue,self.saturation,self.lightness) == (other.hue,other.saturation,other.lightness)
        return NotImplemented

    __hash__ = None
    hue: int
    saturation: float
    lightness: float = 0.5
```

---
### сгенерированный код

```python
from dataclasses import dataclass


@dataclass(order=True, frozen=True)
class Color:
    hue: int
    saturation: float
    lightness: float = 0.5
```

---
### сгенерированный код


```python
def __lt__(self, other):
    if other.__class__ is self.__class__:
        return (self.hue, self.saturation, self.lightness) < (other.hue, other.saturation, other.lightness)
    return NotImplemented

def __le__(self, other):
    if other.__class__ is self.__class__:
        return (self.hue, self.saturation, self.lightness) <= (other.hue, other.saturation, other.lightness)
    return NotImplemented

def __gt__(self, other):
    if other.__class__ is self.__class__:
        return (self.hue, self.saturation, self.lightness) > (other.hue, other.saturation, other.lightness)
    return NotImplemented

def __ge__(self, other):
    if other.__class__ is self.__class__:
        return (self.hue, self.saturation, self.lightness) >= (other.hue, other.saturation, other.lightness)
    return NotImplemented

def __setattr__(self, name, value):
    if type(self) is cls or name in ('hue', 'saturation', 'lightness'):
        raise FrozenInstanceError(f"cannot assign to field {name!r}")
    super(cls, self).__setattr__(name, value)

def __delattr__(self, name):
    cls = self.__class__
    if type(self) is cls or name in ('hue', 'saturation', 'lightness'):
        raise FrozenInstanceError(f"cannot delete field {name!r}")
    super(cls, self).__delattr__(name)

    def __hash__(self):
        return hash((self.hue, self.saturation, self.lightness))
```

---
### Dataclass

Модуль dataclasses предоставляет декоратор dataclass с помощью которого
можно существенно упростить создание классов:

```python
In [1]: from dataclasses import dataclass

In [3]: dataclass?
Signature:
dataclass(
    cls=None,
    /,
    *,
    init=True,
    repr=True,
    eq=True,
    order=False,
    unsafe_hash=False,
    frozen=False,
    match_args=True,
    kw_only=False,
    slots=False,
)
Docstring:
Returns the same class as was passed in, with dunder methods
added based on the fields defined in the class.

Examines PEP 526 __annotations__ to determine fields.

If init is true, an __init__() method is added to the class. If
repr is true, a __repr__() method is added. If order is true, rich
comparison dunder methods are added. If unsafe_hash is true, a
__hash__() method function is added. If frozen is true, fields may
not be assigned to after instance creation. If match_args is true,
the __match_args__ tuple is added. If kw_only is true, then by
default all fields are keyword-only. If slots is true, an
__slots__ attribute is added.
File:      /usr/local/lib/python3.10/dataclasses.py
Type:      function
```

---
### Dataclass

Пример класса IPAddress:

```python
class IPAddress:
    def __init__(self, ip, mask):
        self._ip = ip
        self._mask = mask

    def __repr__(self):
        return f"IPAddress({self.ip}/{self.mask})"
```

И соответствующего класса созданного с помощью dataclass:

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
### Dataclass

Для создания класса данных используется аннотация типов.
Декоратор dataclass использует указанные переменные и дополнительные настройки
для создания атрибутов для экземпляров класса, а также методов ``__init__``, ``__repr__`` и других.

Все переменные, которые определены на уровне класса, по умолчанию, будут прописаны
в методе ``__init__`` и будут ожидаться как аргументы при создании экземпляра.

Типы указанные в определении класса не преобразуют атрибуты и не проверяют
реальный тип данных аргументов.

---
### Метод ``__post_init__``

Метод ``__post_init__`` позволяет добавлять дополнительную логику работы с переменными экземпляра.
Например, можно проверить тип данных или сделать дополнительные вычисления:

```python
@dataclass
class IPAddress:
    ip: str
    mask: int

    def __post_init__(self):
        if not isinstance(self.mask, int):
            self.mask = int(self.mask)


In [46]: ip1 = IPAddress('10.10.1.1', '24')

In [47]: ip1.mask
Out[47]: 24
```

---
### Параметры order и frozen

При декорировании класса можно указать дополнительные параметры:

* frozen - контролирует можно ли менять значения переменных
* order - если равен True, добавляет к классу методы ``__lt__``, ``__le__``, ``__gt__``, ``__ge__``

Если параметр order равен True, экземпляры класса можно сравнивать и упорядочивать:

```python
@dataclass(order=True)
class IPAddress:
    ip: str
    mask: int


In [12]: ip1 = IPAddress('10.1.1.1', 28)

In [14]: ip1 == ip2
Out[14]: False

In [15]: ip1 < ip2
Out[15]: True
```

---
### dataclass

В данном случае, при сравнении и сортировке экземпляров класса возникает проблема
из-за лексикографической сортировки - экземпляры сортируются не так как хотелось бы:

```python
In [24]: ip1 = IPAddress('10.10.1.1', 24)

In [25]: ip2 = IPAddress('10.2.1.1', 24)

In [26]: ip2 > ip1
Out[26]: True

In [27]: ip_list = [ip1, ip2]

In [28]: ip_list
Out[28]: [IPAddress(ip='10.10.1.1', mask=24), IPAddress(ip='10.2.1.1', mask=24)]

In [30]: sorted(ip_list)
Out[30]: [IPAddress(ip='10.10.1.1', mask=24), IPAddress(ip='10.2.1.1', mask=24)]
```

---
### Функция field

Функция field позволяет указывать параметры работы с отдельными переменными.

Например, с помощью field можно указать, что какая-то переменная не должна отображаться
в ``__repr__``:

```python

@dataclass
class User:
    username: str
    password: str = field(repr=False)


In [49]: user1 = User('John', '12345')

In [50]: user1
Out[50]: User(username='John')
```

---
### Функция field


```python
In [5]: dataclasses.field?
Signature:
dataclasses.field(
    *,
    default=<dataclasses._MISSING_TYPE object at 0xb7018868>,
    default_factory=<dataclasses._MISSING_TYPE object at 0xb7018868>,
    init=True,
    repr=True,
    hash=None,
    compare=True,
    metadata=None,
    kw_only=<dataclasses._MISSING_TYPE object at 0xb7018868>,
)
Docstring:
Return an object to identify dataclass fields.

default is the default value of the field.  default_factory is a
0-argument function called to initialize a field's value.  If init
is true, the field will be a parameter to the class's __init__()
function.  If repr is true, the field will be included in the
object's repr().  If hash is true, the field will be included in the
object's hash().  If compare is true, the field will be used in
comparison functions.  metadata, if specified, must be a mapping
which is stored but not otherwise examined by dataclass.  If kw_only
is true, the field will become a keyword-only parameter to
__init__().

It is an error to specify both default and default_factory.
File:      /usr/local/lib/python3.10/dataclasses.py
Type:      function
```

---
### ``__post_init__``

Все переменные, которые определены на уровне класса, по умолчанию, будут прописаны
в методе ``__init__`` и будут ожидаться как аргументы при создании экземпляра.
Иногда в классе могут присутствовать переменные, которые вычисляются на основании
аргументов ``__init__``, а не передаются как аргументы. В этом случае, можно
воспользоваться параметром init в field и вычислить значение динамически в ``__post_init__``:

```python
@dataclass
class Book:
    title: str
    price: int
    quantity: int
    total: int = field(init=False)

    def __post_init__(self):
        self.total = self.price * self.quantity


In [52]: book = Book('Good Omens', 35, 5)

In [53]: book.total
Out[53]: 175

In [54]: book
Out[54]: Book(title='Good Omens', price=35, quantity=5, total=175)
```


---
### Функция field


Функция field также поможет исправить ситуацию с сортировкой в классе IPAddress.
Указав ``compare=False`` при создании переменной, можно исключить ее из сравнения
и сортировки. Также в классе добавлена дополнительная переменная ``_ip``,
которая содержит IP-адрес в виде числа. Для этой переменной ``init=False``, так как
это значение не надо передавать при создании экземпляра, и ``repr=False``, так
как переменная не должна отображаться в строковом представлении:

```python
@dataclass(order=True)
class IPAddress:
    ip: str = field(compare=False)
    _ip: int = field(init=False, repr=False)
    mask: int

    def __post_init__(self):
        self._ip = int(ipaddress.ip_address(self.ip))


In [40]: ip1 = IPAddress('10.10.1.1', 24)

In [41]: ip2 = IPAddress('10.2.1.1', 24)

In [42]: ip_list = [ip1, ip2]

In [43]: sorted(ip_list)
Out[43]: [IPAddress(ip='10.2.1.1', mask=24), IPAddress(ip='10.10.1.1', mask=24)]

In [44]: ip1 > ip2
Out[44]: True
```

---
### Функции asdict, astuple, replace

```python
In [2]: from dataclasses import asdict, astuple, replace, dataclass

In [3]: @dataclass(order=True, frozen=True)
   ...: class IPAddress:
   ...:     ip: str
   ...:     mask: int = 24
   ...:

In [4]: ip1 = IPAddress('10.1.1.1', 28)

In [5]: asdict(ip1)
Out[5]: {'ip': '10.1.1.1', 'mask': 28}

In [6]: astuple(ip1)
Out[6]: ('10.1.1.1', 28)

In [8]: replace(ip1, mask=24)
Out[8]: IPAddress(ip='10.1.1.1', mask=24)

In [9]: ip3 = replace(ip1, mask=24)

In [10]: ip3
Out[10]: IPAddress(ip='10.1.1.1', mask=24)
```

---
### Работа с property

```python
@dataclass
class Book:
    title: str
    price: float
    _price: float = field(init=False, repr=False)
    quantity: int = 0 # TypeError: non-default argument 'quantity' follows default argument

    @property
    def total(self):
        return round(self.price * self.quantity, 2)

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        if not isinstance(value, (int, float)):
            raise TypeError('Значение должно быть числом')
        if not value >= 0:
            raise ValueError('Значение должно быть положительным')
        self._price = float(value)


In [79]: b1 = Book('Good Omens', 35, 5)

In [80]: b1.price
Out[80]: 35.0

In [81]: b1.total
Out[81]: 175.0

In [82]: b1.price = 30

In [83]: b1.total
Out[83]: 150.0
```

