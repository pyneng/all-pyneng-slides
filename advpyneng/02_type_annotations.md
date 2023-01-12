# Advanced Python для сетевых инженеров
## Основы аннотации типов

---
### Аннотация типов

Аннотация типов - это дополнительное описание в классах, функциях, переменных,
которое указывает какой тип данных должен быть в этом месте.

```python
def check_ip(ip: str) -> bool:
    try:
        ipaddress.ip_address(ip)
        return True
    except ValueError as err:
        return False


def check_passwd(
    username: str, password: str, min_length: int = 8, check_username: bool = True
) -> bool:
    if len(password) < min_length:
        return False
    elif check_username and username in password:
        return False
    else:
        return True
```

---
### Аннотация типов

Указанные типы не проверяются и не форсируются самим Python. То есть, при выполнении кода,
несоответствие реального типа данных тому, что написано в аннотациях, не вызывает ошибок или
предупреждений. Для проверки типов данных используются отдельные модули, например, mypy.

Mypy выполняет статический анализ кода - проверяет соответствие типов данных без выполнения кода.

> Аннотация типов добавлялась постепенно в Python 3.x. Начиная с версии Python 3.0 была доступна
> аннотация функций, а в Python 3.6 была добавлена аннотация для переменных.

---
### Аннотация типов

Преимущества:

* при создании объектов сразу описаны типы данных
* можно проверять правильность указанных типов с помощью отдельных модулей
* IDE могут делать подсказки, указывать на ошибки на основании аннотации типов

Нюансы:

* как и с тестами, надо потратить время на написание аннотаций (хотя есть софт, который может в этом помочь)
* на данный момент, надо делать довольно большое количество импортов
* желательно использовать Python 3.6+ чтобы были доступны все возможности, в идеале, последнюю версию Python.

---
### Dataclasses

```python
In [11]: @dataclass
    ...: class IPAddress:
    ...:     ip: str
    ...:     mask: int
    ...:

In [12]: ip1 = IPAddress('10.1.1.1', 28)

In [13]: ip1
Out[13]: IPAddress(ip='10.1.1.1', mask=28)
```

> New in version 3.7.

---
### Typer

```python
def main(ip_addresses: List[str], count: int = 3):
    """
    Ping IP_ADDRESS
    """
    for ip in ip_addresses:
        status = ping_ip(ip, count)
        if status:
            print(f"IP-адрес {ip:15} пингуется")
        else:
            print(f"IP-адрес {ip:15} не пингуется")


if __name__ == "__main__":
    typer.run(main)
```

```
$ python ex02_arg_multiple_values.py --help
Usage: ex02_arg_multiple_values.py [OPTIONS] IP_ADDRESSES...

  Ping IP_ADDRESS

Arguments:
  IP_ADDRESSES...  [required]

Options:
  --count INTEGER       [default: 3]
  --help                Show this message and exit.
```

---
### Mypy

Mypy выполняет статический анализ кода - проверяет соответствие типов данных без выполнения кода.
Mypy не единственный проект такого типа. Другие модули: pyre, pytype.

Пример запуска скрипта с помощью mypy:

```
$ mypy example_01_function_check_ip.py
example_01_function_check_ip.py:13: error: Argument 1 to "check_ip" has incompatible type "int"; expected "str"
Found 1 error in 1 file (checked 1 source file)
```

---
### Статический анализ кода

Статический анализ кода (static code analysis) - анализ кода, производимый без выполнения кода.

* linters [pylint](https://github.com/PyCQA/pylint), pycodestyle, Pyflakes, mccabe, Flake8.
* анализ безопасности [pysa](https://engineering.fb.com/2020/08/07/security/pysa/)
* проверка типов данных: mypy, pyre (facebook), pyright (microsoft), pytype (google)


---
## Синтаксис аннотации типов


---
### Синтаксис: базовые типы данных

```python
username: str = 'user1'
length: int = 5
summ: float = 5.5
skip_line: bool = True
line: str = "switchport mode access"
```

---
### Синтаксис: Списки, множества, словари

```python
from typing import List, Set, Dict

vlans: List[int] = [10, 20, 100]
unique_vlans: Set[int] = {1, 6, 10}
book_price_map: Dict[str, float] = {'Good Omens': 22.0}
```

Начиная с Python 3.9:

```python
vlans: list[int] = [10, 20, 100]
unique_vlans: set[int] = {1, 6, 10}
book_price_map: dict[str, float] = {'Good Omens': 22.0}
```

---
### Синтаксис: Кортежи

Кортеж с фиксированным количеством элементов:
```python
sw_info: Tuple[str, str, int] = ("sw1", "15.1(3)", 24)
```

Кортеж с произвольным количеством элементов:
```python
vlans: Tuple[int, ...] = (1, 2, 3)
```

---
### Аннотация функций

```python
def check_ip(ip: str) -> bool:
    try:
        ipaddress.ip_address(ip)
        return True
    except ValueError as err:
        return False


def check_passwd(
    username: str, password: str, min_length: int = 8, check_username: bool = True
) -> bool:
    if len(password) < min_length:
        return False
    elif check_username and username in password:
        return False
    else:
        return True
```

---
### Аннотация функции с исключениями

Если функция может возвращать какой-то результат или исключения, аннотация пишется только для результата:

```python
from typing import Dict, Union, List, Any

def summ(x: int, y: int) -> int:
    if isinstance(x, int) and isinstance(y, int):
        return x + y
    else:
        raise ValueError('Аргументы должны быть числами')
```

---
### Аннотация классов

Аннотация методов пишется так же как аннотация функций. Единственный нюанс методов - self пишется без аннотации.

```python
class IPAddress:
    def __init__(self, ip: str, mask: int) -> None:
        self.ip = ip
        self.mask = mask

    def __repr__(self) -> str:
        return f"IPAddress({self.ip}/{self.mask})"
```


---
### Аннотация в сложных случаях

```python
from typing import Protocol, TypeVar, Callable, Optional, cast

F = TypeVar("F", bound=Callable[..., object])


class FunctionWithAttributes(Protocol[F]):
    buy: Optional[Callable[[int], int]]
    __call__: F


def count_total(num1: int = 0) -> FunctionWithAttributes[F]:
    count_total = cast(FunctionWithAttributes[F], count_total)
    def buy(num2: int) -> int:
        pass
    return count_total
```

---
### Mypy

---
### Mypy

Mypy выполняет статический анализ кода - проверяет соответствие типов данных без выполнения кода.
Mypy не единственный проект такого типа. Другие модули: pyre, pytype.

Пример запуска скрипта с помощью mypy:

```
$ mypy example_01_function_check_ip.py
example_01_function_check_ip.py:13: error: Argument 1 to "check_ip" has incompatible type "int"; expected "str"
Found 1 error in 1 file (checked 1 source file)
```

---
### Игнорирование ошибок

```python
import somemodule # type: ignore
```

---
### strict

Режим strict включает такие флаги:
* --warn-unused-configs
* --disallow-any-generics
* --disallow-subclassing-any
* --disallow-untyped-calls
* --disallow-untyped-defs
* --disallow-incomplete-defs
* --check-untyped-defs
* --disallow-untyped-decorators
* --no-implicit-optional
* --warn-redundant-casts
* --warn-unused-ignores
* --warn-return-any
* --no-implicit-reexport
* --strict-equality

---
### strict


```python
strict
def func1(a: str, b: str) -> str:
    return a + b

def func2(c, d):
    result = func1(4, 6)
    return c + d
```

По умолчанию, mypy игнорирует функции без аннотации типов:

```
$ mypy testme.py
Success: no issues found in 1 source file
```

С параметром strict mypy проверяет эти функции и их работу с другими объектами:
```
$ mypy testme.py --strict
testme.py:4: error: Function is missing a type annotation
testme.py:5: error: Argument 1 to "func1" has incompatible type "int"; expected "str"
testme.py:5: error: Argument 2 to "func1" has incompatible type "int"; expected "str"
Found 3 errors in 1 file (checked 1 source file)
```

---
### Новые возможности в последних версиях Python

---
### TypedDict

```python
class Network(TypedDict):
    network: str
    mask: int


net1 = {"network": "10.1.1.0", "mask": 26}
```

> New in version 3.8.

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

> New in version 3.8.

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

> New in version 3.8.

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

> New in version 3.8.

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

> New in version 3.8.

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

> New in version 3.8.

---
### Final

```python
from typing import Final


class BaseSSH:
    TIMEOUT: Final[int] = 10

class CiscoSSH(BaseSSH):
    TIMEOUT = 1

```

> New in version 3.8.

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

> New in version 3.8.

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

> New in version 3.8.

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

> New in version 3.8.

---

### Начиная с 3.9 вместо List/Dict/Tuple можно писать list/dict/tuple

```python
def ping_ip_list(
    ip_list: list[str], limit: int = 3
) -> tuple[list[str], list[str]]:
    reachable = []
    unreachable = []
    with ThreadPoolExecutor(max_workers=limit) as executor:
        results = executor.map(ping_ip, ip_list)
    for ip, status in zip(ip_list, results):
        if status:
            reachable.append(ip)
        else:
            unreachable.append(ip)
    return reachable, unreachable
```

Вместо

```python
from typing import List, Tuple

def ping_ip_list_2(
    ip_list: List[str], limit: int = 3
) -> Tuple[List[str], List[str]]:
```

---

### [typing.Annotated](https://docs.python.org/3/library/typing.html#typing.Annotated)

Annotated позволяет добавлять в аннотации не только тип, но и другую информацию.

```python
from typing import Annotated, get_type_hints


IPAddress = Annotated[str, "IP address"]


def ping_ip(ip: IPAddress) -> bool:
    param = "-n" if system_name().lower() == "windows" else "-c"
    command = ["ping", param, "1", ip]
    reply = subprocess.run(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    ip_is_reachable = reply.returncode == 0
    return ip_is_reachable
```

> New in version 3.9.

---

### В [typing.get_type_hints](https://docs.python.org/3/library/typing.html#typing.get_type_hints) добавлен параметр include_extras

```python
get_type_hints(ping_ip)
{'ip': <class 'str'>, 'return': <class 'bool'>}
```

```python
get_type_hints(ping_ip, include_extras=True)
{'ip': typing.Annotated[str, 'IP address'], 'return': <class 'bool'>}
```

> New in version 3.9.


