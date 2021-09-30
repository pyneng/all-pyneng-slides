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
### TypedDict

```python
class Network(TypedDict):
    network: str
    mask: int


net1 = {"network": "10.1.1.0", "mask": 26}
```

> New in version 3.8.

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
```


```python
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
### Mypy

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
