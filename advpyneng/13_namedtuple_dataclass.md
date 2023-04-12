## Генераторы кода
## collections.namedtuple, typing.NamedTuple, dataclass

---
## collections.namedtuple, typing.NamedTuple, dataclass

```python
r1 = ("r1", "15.4", "Cisco", "10.1.1.1")
```

* кортеж
* словарь
* класс
* класс с помощью namedtuple, dataclass

---
## collections.namedtuple, typing.NamedTuple, dataclass

```python
class IPAddress:
    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask

ip1 = IPAddress("10.1.1.1", 24)
```
---
## collections.namedtuple, typing.NamedTuple, dataclass


### class

```python
class IPAddress:
    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask

ip1 = IPAddress("10.1.1.1", 24)
```


### collections.namedtuple

```python
IPAddress = namedtuple("IPAddress", "ip mask")
```

### typing.NamedTuple

```python
class IPAddress(NamedTuple):
    ip: str
    mask: int
```

### dataclasses.dataclass

```python
@dataclass
class IPAddress:
    ip: str
    mask: int
```

---
## collections.namedtuple

```python
IPAddress = namedtuple("IPAddress", "ip mask")
```

### class

```python
@total_ordering
class IPAddress:
    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask

    def __repr__(self):
        return f"IPAddress(ip='{self.ip}', mask={self.mask})"

    def __lt__(self, other):
        if not isinstance(other, type(self)):
            return NotImplemented
        return (self.ip, self.mask) < (other.ip, other.mask)

    def __eq__(self, other):
        if not isinstance(other, type(self)):
            return NotImplemented
        return (self.ip, self.mask) == (other.ip, other.mask)
```

---
## collections.namedtuple

```python
IPAddress = namedtuple("IPAddress", "ip mask")
```

### class

```python
@total_ordering
class IPAddress:
    def __init__(self, ip, mask):
        self._ip = ip
        self._mask = mask

    @property
    def ip(self):
        return self._ip

    @property
    def mask(self):
        return self._mask

    def __repr__(self):
        return f"IPAddress(ip='{self.ip}', mask={self.mask})"

    def __lt__(self, other):
        if not isinstance(other, type(self)):
            return NotImplemented
        return (self.ip, self.mask) < (other.ip, other.mask)

    def __eq__(self, other):
        if not isinstance(other, type(self)):
            return NotImplemented
        return (self.ip, self.mask) == (other.ip, other.mask)
```


---
## collections.namedtuple

---
### collections.namedtuple

namedtuple позволяет создавать новые классы:

* доступ к атрибутам может осуществляться по имени
* доступ к элементами по индексу
* экземпляр класса является итерируемым объектом
* атрибуты неизменяемы
* все возможности обычных кортежей остаются

---
### collections.namedtuple

```python
from collections import namedtuple


NetworkDevice = namedtuple("NetworkDevice", "hostname ios vendor ip")
r1 = NetworkDevice("r1", "15.4", "Cisco", "10.1.1.1")

In [4]: r1
Out[4]: NetworkDevice(hostname='r1', ios='15.4', vendor='Cisco', ip='10.1.1.1')

In [5]: r1.hostname
Out[5]: 'r1'

In [6]: r1.ip
Out[6]: '10.1.1.1'

In [7]: hostname, ios, vendor, ip = r1

In [8]: r5 = NetworkDevice("r1", "15.4", "Cisco", "10.1.1.1")

In [9]: r1 == r5
Out[9]: True

In [10]: r1[0]
Out[10]: 'r1'

In [11]: r1[-1]
Out[11]: '10.1.1.1'
```

---
### collections.namedtuple

```python
In [9]: r1 = NetworkDevice("r1", "15.4", "Cisco", "10.1.1.1")
   ...: r2 = NetworkDevice("r2", "12.4", "Cisco", "10.1.1.2")
   ...: r3 = NetworkDevice("r3", "15.6", "Cisco", "10.1.1.3")
   ...: r4 = NetworkDevice("r4", "15.4", "Cisco", "10.1.1.4")
   ...: r5 = NetworkDevice("r1", "15.4", "Cisco", "10.1.1.1")

In [10]: devices = [r1, r2, r4, r5, r3]

In [11]: sorted(devices)
Out[11]:
[NetworkDevice(hostname='r1', ios='15.4', vendor='Cisco', ip='10.1.1.1'),
 NetworkDevice(hostname='r1', ios='15.4', vendor='Cisco', ip='10.1.1.1'),
 NetworkDevice(hostname='r2', ios='12.4', vendor='Cisco', ip='10.1.1.2'),
 NetworkDevice(hostname='r3', ios='15.6', vendor='Cisco', ip='10.1.1.3'),
 NetworkDevice(hostname='r4', ios='15.4', vendor='Cisco', ip='10.1.1.4')]
```


---
### collections.namedtuple

Метод ``_as_dict``:

```python
In [8]: r1._asdict()
Out[8]: {'hostname': 'r1', 'ios': '15.4', 'vendor': 'Cisco', 'ip': '10.1.1.1'}
```

---
### collections.namedtuple

Метод ``_replace`` возвращает новый экземпляр класса, в котором заменены указанные поля:

```python

In [18]: r1 = RouterClass('r1', '10.1.1.1', '15.4')

In [19]: r1
Out[19]: Router(hostname='r1', ip='10.1.1.1', ios='15.4')

In [20]: r1._replace(ip='10.2.2.2')
Out[20]: Router(hostname='r1', ip='10.2.2.2', ios='15.4')
```


---
### collections.namedtuple

Метод ``_make`` создает новый экземпляр класса из последовательности полей (это метод класса):

```python
In [22]: RouterClass._make(['r3', '10.3.3.3', '15.2'])
Out[22]: Router(hostname='r3', ip='10.3.3.3', ios='15.2')

In [23]: r3 = RouterClass._make(['r3', '10.3.3.3', '15.2'])
```


```python
data = [
    ["sw1", "12.5", "Cisco IOS", "10.1.1.101"],
    ["sw2", "12.5", "Cisco IOS", "10.1.1.102"],
    ["sw3", "12.5", "Cisco IOS", "10.1.1.103"],
    ["sw4", "12.5", "Cisco IOS", "10.1.1.104"],
]

NetworkDevice = namedtuple("NetworkDevice", "hostname ios vendor ip")

In [11]: list(map(NetworkDevice._make, data))
Out[11]:
[NetworkDevice(hostname='sw1', ios='12.5', vendor='Cisco IOS', ip='10.1.1.101'),
 NetworkDevice(hostname='sw2', ios='12.5', vendor='Cisco IOS', ip='10.1.1.102'),
 NetworkDevice(hostname='sw3', ios='12.5', vendor='Cisco IOS', ip='10.1.1.103'),
 NetworkDevice(hostname='sw4', ios='12.5', vendor='Cisco IOS', ip='10.1.1.104')]
```

---
### collections.namedtuple

Пример использования namedtuple:

```python
import sqlite3
from collections import namedtuple


key = 'vlan'
value = 10
db_filename = 'dhcp_snooping.db'

keys = ['mac', 'ip', 'vlan', 'interface', 'switch']
DhcpSnoopRecord = namedtuple('DhcpSnoopRecord', keys)

conn = sqlite3.connect(db_filename)
query = 'select {} from dhcp where {} = ?'.format(','.join(keys), key)

print('-' * 40)
for row in map(DhcpSnoopRecord._make, conn.execute(query, (value,))):
    print(row.mac, row.ip, row.interface, sep='\n')
    print('-' * 40)
```

Вывод:

```
$ python get_data.py
----------------------------------------
00:09:BB:3D:D6:58
10.1.10.2
FastEthernet0/1
----------------------------------------
00:07:BC:3F:A6:50
10.1.10.6
FastEthernet0/3
----------------------------------------
```

---
### collections.namedtuple
Параметр defaults позволяет указывать значения по умолчанию:

```python

In [33]: IPAddress = namedtuple('IPAddress', ['address', 'mask'], defaults=[24])

In [34]: ip1 = IPAddress('10.1.1.1', 28)

In [35]: ip1
Out[35]: IPAddress(address='10.1.1.1', mask=28)

In [36]: ip2 = IPAddress('10.2.2.2')

In [37]: ip2
Out[37]: IPAddress(address='10.2.2.2', mask=24)

```

---
## typing.NamedTuple

---
### typing.NamedTuple

Еще один вариант создания класса с помощью именнованного кортежа - наследование
класса typing.NamedTuple.
Базовые особенности namedtuple сохраняются, плюс есть возможность добавлять свои методы.

```python
from typing import NamedTuple

class IPAddress(NamedTuple):
    ip: str
    mask: int = 24


In [11]: ip1 = IPAddress('10.1.1.1', 28)

In [12]: ip1
Out[12]: IPAddress(ip='10.1.1.1', mask=28)
```
---
### typing.NamedTuple

```python
class IPAddress(NamedTuple):
    ip: str
    mask: int = 24

    def convert_to_bin(self):
        pass


In [14]: ip1 = IPAddress('10.1.1.1', 28)

In [15]: ip1.convert_to_bin
Out[15]: <bound method IPAddress.convert_to_bin of IPAddress(ip='10.1.1.1', mask=28)>
```

---
### typing.NamedTuple

```python
class IPAddress(NamedTuple):
    ip: str
    mask: int

    def __int__(self):
        bin_ip = "".join([f"{int(octet):08b}" for octet in self.ip.split(".")])
        return int(bin_ip, 2)

    def __lt__(self, other):
        return int(self) < int(other)


ip1 = IPAddress("10.1.1.1", 24)
ip2 = IPAddress("10.10.1.2", 24)
ip3 = IPAddress("10.11.1.3", 24)
ip4 = IPAddress("10.20.1.4", 24)
ip5 = IPAddress("10.2.1.1", 24)

ip_list = [ip1, ip2, ip3, ip4, ip5]

In [27]: sorted(ip_list)
Out[27]:
[IPAddress(ip='10.1.1.1', mask=24),
 IPAddress(ip='10.2.1.1', mask=24),
 IPAddress(ip='10.10.1.2', mask=24),
 IPAddress(ip='10.11.1.3', mask=24),
 IPAddress(ip='10.20.1.4', mask=24)]
```

---
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

