## collections.namedtuple

---
### collections.namedtuple

Функция namedtuple позволяет создавать новые классы, которые наследуют tuple и при этом:

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

```python
In [2]: sys.getsizeof?
Docstring:
getsizeof(object [, default]) -> int

Return the size of object in bytes.
Type:      builtin_function_or_method
```

```python
from collections import namedtuple

NetworkDevice = namedtuple("NetworkDevice", "hostname ios vendor ip")

r1 = NetworkDevice("r1", "15.4", "Cisco", "10.1.1.1")
r1_dict = {'hostname': 'r1', 'ios': '15.4', 'vendor': 'Cisco', 'ip': '10.1.1.1'}


In [4]: sys.getsizeof(r1)
Out[4]: 36

In [6]: sys.getsizeof(r1_dict)
Out[6]: 124
```

---
### collections.namedtuple

Метод ``_as_dict`` возвращает OrderedDict:

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

In [13]: class IPAddress(NamedTuple):
    ...:     ip: str
    ...:     mask: int = 24
    ...:
    ...:     def convert_to_bin(self):
    ...:         pass
    ...:

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

