# What’s New In Python 3.8

---
### Assignment expressions

---
### Walrus operator

Было:
```python
n = len(a)
if n > 10:
    print(f"List is too long ({n} elements, expected <= 10)")
```

Стало:
```python
if (n := len(a)) > 10:
    print(f"List is too long ({n} elements, expected <= 10)")
```

---
### Walrus operator

Было:

```python
import re

regex = "vlan (\d+) is flapping between port (\S+) and port (\S+)"

ports = set()

with open("log.txt") as f:
    for line in f:
        match = re.search(regex, line)
        if match:
            vlan, port1, port2 = match.groups()
            ports.update({port1, port2})

print(f"Петля между портами {', '.join(ports)}")
```

Стало:

```python
with open("log.txt") as f:
    for line in f:
        if (match := re.search(regex, line)):
            vlan, port1, port2 = match.groups()
            ports.update({port1, port2})
```

---
### Walrus operator

Было:

```python
def create_user(db):
    username = input("Введите имя пользователя: ")
    while True:
        password = input("Введите пароль: ")
        check = check_password(username, password)
        if check:
            break
    with open(db, 'a') as f:
        f.write(f"{username},{password}\n")
```

Стало:

```python
def create_user(db):
    username = input("Введите имя пользователя: ")
    while not check_password(
        username, password := input("Введите пароль: ")
    ):
        pass
    with open(db, 'a') as f:
        f.write(f"{username},{password}\n")
```

---
### Walrus operator

```python
In [11]: data
Out[11]: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]

In [12]: def func(item):
    ...:     if item in [1, 2, 3]:
    ...:         return item * 100
    ...:

In [13]: [new for item in data if (new := func(item))]
Out[13]: [100, 200, 300]

In [15]: [func(item) for item in data if func(item)]
Out[15]: [100, 200, 300]
```

---
### Positional-only arguments

```python
In [8]: data = [1, 2, 3]

In [9]: data.append?
Signature: data.append(object, /)
Docstring: Append object to the end of the list.
Type:      builtin_function_or_method

```

---
### Keyword-only arguments

```python
In [2]: sorted?
Signature: sorted(iterable, /, *, key=None, reverse=False)
Docstring:
Return a new list containing all items from the iterable in ascending order.

A custom key function can be supplied to customize the sort order, and the
reverse flag can be set to request the result in descending order.
Type:      builtin_function_or_method
```

---
### Positional-only and keyword-only arguments

```python
def check_passwd(username, password, /, *, min_length=8, check_username=True):
    if len(password) < min_length:
        print(f'{password} слишком короткий')
        return False
    elif check_username and username in password:
        print(f'{password} содержит имя пользователя')
        return False
    else:
        print(f'{password} для пользователя {username} прошел все проверки')
        return True

```

---
### f-strings support =

---
### f-strings support =

```python
In [23]: ip = "10.1.1.1"

In [24]: mask = 24

In [25]: f"{ip} {mask}"
Out[25]: '10.1.1.1 24'

In [26]: f"{ip=} {mask=}"
Out[26]: "ip='10.1.1.1' mask=24"
```

---
### f-strings support =

```python
In [9]: vlans = [1, 2, 3]

In [10]: f"{','.join(map(str, vlans))}"
Out[10]: '1,2,3'

In [11]: f"{','.join(map(str, vlans))=}"
Out[11]: "','.join(map(str, vlans))='1,2,3'"
```

---
### Модуль typing

---
### Аннотация типов

Пример аннотации функции:
```python
import ipaddress


def check_ip(ip: str) -> bool:
    try:
        ipaddress.ip_address(ip)
        return True
    except ValueError as err:
        return False
```

Пример аннотации функции со значениями по умолчанию:
```python
def check_passwd(username: str, password: str,
                 min_length: int = 8, check_username: bool = True) -> bool:
    if len(password) < min_length:
        print('Пароль слишком короткий')
        return False
    elif check_username and username in password:
        print('Пароль содержит имя пользователя')
        return False
    else:
        print(f'Пароль для пользователя {username} прошел все проверки')
        return True
```

---
### Аннотация типов


```python
from typing import Union, List


class BaseSSH:
    def __init__(self, ip: str, username: str, password: str) -> None:
        self.ip = ip
        self.username = username
        self.password = password

    def send_config_commands(self, commands: Union[str, List[str]]) -> str:
        if isinstance(commands, str):
            commands = [commands]
        for command in commands:
```

---
### TypedDict

```python
from netmiko import ConnectHandler
from typing import List, Dict, Any


def send_show(device_dict: Dict[str, Any], command: str) -> str:
    with ConnectHandler(**device_dict) as ssh:
        ssh.enable()
        result = ssh.send_command(command)
    return result


device_dict = {
    'device_type': 'cisco_ios',
    'host': '192.168.100.1',
    'username': 'cisco',
    'password': 'cisco',
    'secret': 'cisco',
    'port': 20020,
    }
```

---
### TypedDict

```python
from typing import TypedDict, NamedTuple


class IPAddress(NamedTuple):
    ip: str
    mask: int = 24


ip1 = IPAddress('10.1.1.1', 28)

#IPAddress(ip='10.1.1.1', mask=28)


class IPAddress(TypedDict):
    ipaddress: str
    mask: int

ip1 = IPAddress(ipaddress="8.8.8.8", mask=26)

```

---
### TypedDict

```python
from netmiko import ConnectHandler
from typing import List, TypedDict, NamedTuple


class DeviceParams(TypedDict, total=False):
    device_type: str
    host: str
    username: str
    password: str
    secret: str
    port: int


def send_show(device_dict: DeviceParams, command: str) -> str:
    with ConnectHandler(**device_dict) as ssh:
        ssh.enable()
        result = ssh.send_command(command)
    return result


if __name__ == "__main__":
    r1 = DeviceParams(
        device_type="cisco_ios",
        host="192.168.100.1",
        username="cisco",
        password="cisco",
        secret="cisco",
        port=20020,
    )
    print(send_show(r1, "sh clock"))

```

---
### Literal

```python
from typing import Literal


def get_data_by_key_value(
    db_name: str, key: Literal["mac", "ip", "vlan", "interface"], value: str
) -> str:
    return "line"


print(get_data_by_key_value("database.db", "ip", "8.8.8.8"))
```

---
### Final


```python
import sqlite3
from typing import Final, final

DATABASE: Final[str] = "dhcp_snooping.db"


def create_db(db_name: str, schema: str) -> None:
    with open(schema) as f:
        schema_f = f.read()
        connection = sqlite3.connect(db_name)
        connection.executescript(schema_f)
        connection.close()


if __name__ == "__main__":
    DATABASE = "mydb.db"
    schema_filename = "dhcp_snooping_schema.sql"
    create_db(DATABASE, schema_filename)
```

Ошибка:
```python
$ mypy typing_final.py
typing_final.py:19: error: Cannot assign to final name "DATABASE"
Found 1 error in 1 file (checked 1 source file)

```

---
### Final

```python
from typing import Final


class BaseSSH:
    TIMEOUT: Final[int] = 10

class CiscoSSH(BaseSSH):
    TIMEOUT = 1

```

---
### final

Декоратор final указывает, что метод не может быть переписан

```python
from typing import final


class BaseSSH:
    @final
    def done(self) -> None:
        pass


class CiscoSSH(BaseSSH):
    def done(self) -> None:
        pass

```

---
### final

Декоратор final указывает, что класс не может наследоваться

```python
from typing import final

@final
class CiscoIosSSH:
    pass


class Other(CiscoIosSSH):
    pass

```

---
### Protocol

```python
class ConnectSSH(Protocol):
    def send_command(self, command: str) -> str:
        ...

    def send_config_commands(self, commands: str) -> str:
        ...


class CiscoSSH:

    def send_command(self, command: str) -> str:
        result = self._ssh.send_command(command)
        return result

    def send_config_commands(self, commands: str) -> str:
        result = self._ssh.send_config_set(commands)
        return result


def func(connection: ConnectSSH, command: str) -> str:
    return connection.send_command(command)


if __name__ == "__main__":
    r1 = CiscoSSH("192.168.100.1", "cisco", "cisco", "cisco")
    print(func(r1, "sh clock"))

```

---
### iterable unpacking in yield and return

До Python 3.8 можно было так:
```python
In [27]: data = [1, 2, 3]

In [28]: result = 1, 2, *data

In [29]: result
Out[29]: (1, 2, 1, 2, 3)
```

После можно еще и так:
```python
In [30]: def func():
    ...:     data = [1, 2, 3]
    ...:     return 1, 2, *data
    ...:

In [31]: func()
Out[31]: (1, 2, 1, 2, 3)

```

---
### SyntaxWarning

Python 3.7
```python
In [1]: name = "test"

In [2]: name is "test"
Out[2]: True
```

Python 3.8
```python
In [1]: name = "test"

In [2]: name is "test"
<>:1: SyntaxWarning: "is" with a literal. Did you mean "=="?
<ipython-input-2-ea7008bc46b6>:1: SyntaxWarning: "is" with a literal. Did you mean "=="?
  name is "test"
Out[2]: True
```

---
### SyntaxWarning

Python 3.7

```python
In [3]: [("r1", "Gi0/1") ("r2", "Gi0/2")]
TypeError                  Traceback (most recent call last)
<ipython-input-3-6b8485007d3b> in <module>
--> 1 [("r1", "Gi0/1") ("r2", "Gi0/2")]

TypeError: 'tuple' object is not callable
```

Python 3.8

```python
In [3]: [("r1", "Gi0/1") ("r2", "Gi0/2")]
<>:1: SyntaxWarning: 'tuple' object is not callable; perhaps you missed a comma?
<ipython-input-3-6b8485007d3b>:1: SyntaxWarning: 'tuple' object is not callable; perhaps you missed a comma?
  [("r1", "Gi0/1") ("r2", "Gi0/2")]
TypeError                  Traceback (most recent call last)
<ipython-input-3-6b8485007d3b> in <module>
--> 1 [("r1", "Gi0/1") ("r2", "Gi0/2")]

TypeError: 'tuple' object is not callable
```

---
### Optimizations

Sped-up field lookups in collections.namedtuple(). They are now more than two times faster, making them the fastest form of instance variable lookup in Python.

Python 3.7
```python
In [2]: from timeit import timeit

In [3]: from collections import namedtuple

In [4]: RouterClass = namedtuple('Router', ['hostname', 'ip', 'ios'])

In [5]: r1 = RouterClass('r1', '10.1.1.1', '15.4')

In [6]: timeit("r1.hostname", globals=globals())
Out[6]: 0.23264930900768377
```

Python 3.8
```python
In [3]: from collections import namedtuple

In [4]: from timeit import timeit

In [5]: RouterClass = namedtuple('Router', ['hostname', 'ip', 'ios'])

In [6]: r1 = RouterClass('r1', '10.1.1.1', '15.4')

In [7]: timeit("r1.hostname", globals=globals())
Out[7]: 0.1512959760002559

```

---
### Optimizations

The list constructor does not overallocate the internal item buffer if the input iterable has a known length (the input implements __len__). This makes the created list 12% smaller on average. (Contributed by Raymond Hettinger and Pablo Galindo in bpo-33234.)


Python 3.7
```python
In [1]: import sys

In [2]: sys.getsizeof(list(range(20191014)))
Out[2]: 90859616
```

Python 3.8
```python
In [10]: import sys

In [11]: sys.getsizeof(list(range(20191014)))
Out[11]: 80764084
```


---
### collections, csv - dict

Python 3.8
```python
In [3]: import csv
   ...:
   ...: with open('sw_data.csv') as f:
   ...:     reader = csv.DictReader(f)
   ...:     for row in reader:
   ...:         print(row)
   ...:
{'hostname': 'sw1', 'vendor': 'Cisco', 'model': '3750', 'location': 'London'}
{'hostname': 'sw2', 'vendor': 'Cisco', 'model': '3850', 'location': 'Liverpool'}
{'hostname': 'sw3', 'vendor': 'Cisco', 'model': '3650', 'location': 'Liverpool'}
{'hostname': 'sw4', 'vendor': 'Cisco', 'model': '3650', 'location': 'London'}
```

Python 3.7
```python
In [1]: import csv
   ...:
   ...: with open('sw_data.csv') as f:
   ...:     reader = csv.DictReader(f)
   ...:     for row in reader:
   ...:         print(row)
   ...:
OrderedDict([('hostname', 'sw1'), ('vendor', 'Cisco'), ('model', '3750'), ('location', 'London')])
OrderedDict([('hostname', 'sw2'), ('vendor', 'Cisco'), ('model', '3850'), ('location', 'Liverpool')])
OrderedDict([('hostname', 'sw3'), ('vendor', 'Cisco'), ('model', '3650'), ('location', 'Liverpool')])
OrderedDict([('hostname', 'sw4'), ('vendor', 'Cisco'), ('model', '3650'), ('location', 'London')])

```

---
### logging

```python
$ ipython
Python 3.7.3 (default, May 13 2019, 15:44:23)

In [2]: import logging
   ...:
   ...:
   ...: logging.basicConfig(
   ...:     format='%(threadName)s %(name)s %(levelname)s: %(message)s',
   ...:     level=logging.INFO)

In [4]: logging.debug("test")

In [5]: logging.basicConfig(
   ...:     format='%(threadName)s %(name)s %(levelname)s: %(message)s',
   ...:     level=logging.DEBUG)

In [6]: logging.debug("test")

```

---
### logging

```python
$ ipython
Python 3.8.0 (default, Nov  9 2019, 12:40:50)

In [1]: import logging
   ...:
   ...:
   ...: logging.basicConfig(
   ...:     format='%(threadName)s %(name)s %(levelname)s: %(message)s',
   ...:     level=logging.INFO)

In [2]: logging.debug("test")

In [5]: logging.basicConfig(
   ...:     format='%(threadName)s %(name)s %(levelname)s: %(message)s',
   ...:     level=logging.DEBUG,
   ...:     force=True)

In [6]: logging.debug("test")
MainThread root DEBUG: test
```

---
### pprint

```python
In [10]: from pprint import pp, pprint

In [11]: pprint?
Signature:
pprint(
    object,
    stream=None,
    indent=1,
    width=80,
    depth=None,
    *,
    compact=False,
    sort_dicts=True,
)

In [12]: pp?
Signature: pp(object, *args, sort_dicts=False, **kwargs)

In [15]: d = {1: 10, 5: 50, 3: 30, 2: 20}

In [16]: pp(d)
{1: 10, 5: 50, 3: 30, 2: 20}

In [17]: pprint(d)
{1: 10, 2: 20, 3: 30, 5: 50}
```

---
### asyncio REPL

```python
$ python -m asyncio
asyncio REPL 3.8.0 (default, Nov  9 2019, 12:40:50)
[GCC 4.9.2] on linux
Use "await" directly instead of "asyncio.run()".
Type "help", "copyright", "credits" or "license" for more information.
>>> import asyncio
>>> await asyncio.sleep(10, result='hello')
'hello'
>>>
```
