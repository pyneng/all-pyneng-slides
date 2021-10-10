# What's new in Python 3.10

---

# What's new in Python 3.10

* новый синтаксис match/case: structural pattern matching
* новый синтаксис: менеджеры контекста в скобках
* новый параметр strict в zip
* лучше сообщения об ошибках
* аннотация типов: добавлен синтаксис | вместо Union
* аннотация типов: тип Alias
* аннотация типов: Parameter Specification Variables
* аннотация типов: TypeGuard
* anext, aiter

---
## structural pattern matching

[Сопоставление с образцом](https://ru.wikipedia.org/wiki/%D0%A1%D0%BE%D0%BF%D0%BE%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5_%D1%81_%D0%BE%D0%B1%D1%80%D0%B0%D0%B7%D1%86%D0%BE%D0%BC)

Вдохновление: Scala, Rust

---
## structural pattern matching

```python
result = ["test", "test2"]
```

Python 3.10
```python
match result:
    case reachable, unreachable:
        ...
```

Python 3.9
```python
if isinstance(result, Sequence) and len(result) == 2:
    reachable, unreachable = result
    ...
```

---
### Matching specific values

```python
match command:
    case "quit":
        print("Goodbye!")
        quit_game()
    case "look" | "test":
        current_room.describe()
```

---
### Matching sequences

```python
In [9]: topology
Out[9]:
{('R1', 'Eth0/0'): ('SW1', 'Eth0/1'),
 ('R2', 'Eth0/0'): ('SW1', 'Eth0/2'),
 ('R2', 'Eth0/1'): ('SW2', 'Eth0/11'),
 ('R3', 'Eth0/0'): ('SW1', 'Eth0/3'),
 ('R3', 'Eth0/1'): ('R4', 'Eth0/0'),
 ('R3', 'Eth0/2'): ('R5', 'Eth0/0')}

In [10]: for key in topology:
    ...:     match key:
    ...:         case ("R3", port):
    ...:             print("R3 connections:", port)
    ...:
R3 connections: Eth0/0
R3 connections: Eth0/1
R3 connections: Eth0/2
```

---
### Matching sequences, Or patterns

```python
In [9]: topology
Out[9]:
{('R1', 'Eth0/0'): ('SW1', 'Eth0/1'),
 ('R2', 'Eth0/0'): ('SW1', 'Eth0/2'),
 ('R2', 'Eth0/1'): ('SW2', 'Eth0/11'),
 ('R3', 'Eth0/0'): ('SW1', 'Eth0/3'),
 ('R3', 'Eth0/1'): ('R4', 'Eth0/0'),
 ('R3', 'Eth0/2'): ('R5', 'Eth0/0')}

In [11]: for item in list(topology.items()):
    ...:     match item:
    ...:         case (("SW1", port_sw), _) | (_, ("SW1", port_sw)):
    ...:             print("SW1 connections:", port_sw)
    ...:
SW1 connections: Eth0/1
SW1 connections: Eth0/2
SW1 connections: Eth0/3
```

---
### Matching sequences

```python
with open("config_sw1.txt") as f:
    for line in f:
        match line.split():
            case ["interface", intf]:
                print(intf)
            case [*other, "vlan", vlans]:
                print(vlans)
```

---
### Capturing matched sub-patterns

```python
match command.split():
    case ["go", ("north" | "south" | "east" | "west") as direction]:
        current_room = current_room.neighbor(direction)
```

---
### Adding conditions to patterns

```python
ip_address = input("введите ip-адрес: ")

match ip_address.split("."):
    case octets if set(octets) == {"255"}:
        print("local broadcast")
    case octets if set(octets) == {"0"}:
        print("unassigned")
    case [octet1, *others] if 1 <= int(octet1) <= 223:
        print("unicast")
    case [octet1, *others] if 224 <= int(octet1) <= 239:
        print("multicast")
    case _:
        print("unused")
```

---
### Mappings

```python
devices = {
    'r1': {
        'hostname': 'london_r1',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '4451',
    },
    'r2': {
        'hostname': 'london_r2',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '4451',
    },
    'sw1': {
        'hostname': 'london_sw1',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '3850',
    }
}

for dev, device_params in devices.items():
    match device_params:
        case {"model": "4451"}:
            print(dev)
            print(device_params)
```

---
### Mappings

```python
for action in actions:
    match action:
        case {"text": message, "color": c}:
            ui.set_text_color(c)
            ui.display(message)
        case {"sleep": duration}:
            ui.wait(duration)
        case {"sound": url, "format": "ogg"}:
            ui.play(url)
        case {"sound": _, "format": _}:
            warning("Unsupported audio format")
```

---
### Matching builtin classes

```python
for action in actions:
    match action:
        case {"text": str(message), "color": str(c)}:
            ui.set_text_color(c)
            ui.display(message)
        case {"sleep": float(duration)}:
            ui.wait(duration)
        case {"sound": str(url), "format": "ogg"}:
            ui.play(url)
        case {"sound": _, "format": _}:
            warning("Unsupported audio format")
```

---

```python
match event.get():
    case Click(position=(x, y)):
        handle_click_at(x, y)
    case KeyPress(key_name="Q") | Quit():
        game.quit()
    case KeyPress(key_name="up arrow"):
        game.go_north()
    ...
    case KeyPress():
        pass # Ignore other keystrokes
    case other_event:
        raise ValueError(f"Unrecognized event: {other_event}")
```

---
## новый синтаксис: менеджеры контекста в скобках

```python
with (CtxManager() as example):
    ...

with (
    CtxManager1(),
    CtxManager2()
):
    ...

with (CtxManager1() as example,
      CtxManager2()):
    ...

with (CtxManager1(),
      CtxManager2() as example):
    ...

with (
    CtxManager1() as example1,
    CtxManager2() as example2
):
    ...
```

---
### Лучше сообщения об ошибках

Python 3.9
```python
File "example.py", line 3
    some_other_code = foo()
                    ^
SyntaxError: invalid syntax
```

Python 3.10
```python
File "example.py", line 1
    expected = {9: 1, 18: 2, 19: 2, 27: 3, 28: 3, 29: 3, 36: 4, 37: 4,
               ^
SyntaxError: '{' was never closed
```

---
### Лучше сообщения об ошибках

Python 3.10
```python
>>> if rocket.position > event_horizon
  File "<stdin>", line 1
    if rocket.position > event_horizon
                                      ^
SyntaxError: expected ':'
```


---
### Лучше сообщения об ошибках

Python 3.10
```python
>>> items = {
... x: 1,
... y: 2
... z: 3,
  File "<stdin>", line 3
    y: 2
       ^
SyntaxError: invalid syntax. Perhaps you forgot a comma?
```


---
### Лучше сообщения об ошибках

Python 3.10
```python
>>> if rocket.position = event_horizon:
  File "<stdin>", line 1
    if rocket.position = event_horizon:
                       ^
SyntaxError: cannot assign to attribute here. Maybe you meant '==' instead of '='?
```

---
### Лучше сообщения об ошибках

Python 3.10
```python
>>> try:
...     build_dyson_sphere()
... except NotEnoughScienceError, NotEnoughResourcesError:
  File "<stdin>", line 3
    except NotEnoughScienceError, NotEnoughResourcesError:
           ^
SyntaxError: multiple exception types must be parenthesized
```

---
### Лучше сообщения об ошибках

```python
>>> schwarzschild_black_hole = None
>>> schwarschild_black_hole
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'schwarschild_black_hole' is not defined. Did you mean: schwarzschild_black_hole?
```

---
### Новый параметр strict в zip

```python
In [1]: list(zip(('a', 'b', 'c'), (1, 2, 3, 4)))
Out[1]: [('a', 1), ('b', 2), ('c', 3)]

In [2]: list(zip(('a', 'b', 'c'), (1, 2, 3), strict=True))
Out[2]: [('a', 1), ('b', 2), ('c', 3)]

In [3]: list(zip(('a', 'b', 'c'), (1, 2, 3, 4), strict=True))
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-3-482d3cbd202a> in <module>
----> 1 list(zip(('a', 'b', 'c'), (1, 2, 3, 4), strict=True))

ValueError: zip() argument 2 is longer than argument 1
```

---
### Аннотация типов: добавлен синтаксис | вместо Union

[PEP 604 Allow writing union types as X | Y](https://www.python.org/dev/peps/pep-0604/)

Python 3.9
```python
def square(number: Union[int, float]) -> Union[int, float]:
    return number ** 2
```

Python 3.10
```python
def square(number: int | float) -> int | float:
    return number ** 2
```

Этот же синтаксис можно использовать в isinstance и issubclass
```python
>>> isinstance(1, int | str)
True
```

---
### Аннотация типов: TypeGuard

[PEP 647 User-Defined Type Guards](https://www.python.org/dev/peps/pep-0647/)

```python
def func(val: str | float):
    # "isinstance" type guard
    if isinstance(val, str):
        # Type of val is narrowed to str
        ...
    else:
        # Type of val is narrowed to float
        ...
```

---
### Аннотация типов: TypeGuard

```python
def is_str_list(val: list[object]) -> bool:
    """Determines whether all objects in the list are strings"""
    return all(isinstance(x, str) for x in val)


def func1(val: list[object]):
    if is_str_list(val):
        print(" ".join(val)) # Error: invalid type
```

Ошибка
```
$ mypy ex06_annotations_type_guard.py
ex06_annotations_type_guard.py:7: error: Argument 1 to "join" of "str" has incompatible type "List[object]"; expected "Iterable[str]"
Found 1 error in 1 file (checked 1 source file)
```

---
### Аннотация типов: TypeGuard

```python
from typing import TypeGuard


def is_str_list(val: list[object]) -> TypeGuard[list[str]]:
    """Determines whether all objects in the list are strings"""
    return all(isinstance(x, str) for x in val)


def func1(val: list[object]):
    if is_str_list(val):
        print(" ".join(val))  # Error: invalid type
```

---
## Аннотация типов: тип Alias

[PEP 613, Explicit Type Aliases](https://www.python.org/dev/peps/pep-0613)


Python 3.9
```python
trCache = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
```

Python 3.10
```python
StrCache: TypeAlias = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
```

---
## Аннотация типов: Parameter Specification Variables

[PEP 612, Parameter Specification Variables](https://www.python.org/dev/peps/pep-0612)

```python
from typing import Awaitable, Callable, ParamSpec, TypeVar

P = ParamSpec("P")
R = TypeVar("R")


def add_logging(f: Callable[P, R]) -> Callable[P, Awaitable[R]]:
    async def inner(*args: P.args, **kwargs: P.kwargs) -> R:
        await log_to_database()
        return f(*args, **kwargs)
    return inner


@add_logging
def takes_int_str(x: int, y: str) -> int:
    return x + 7

await takes_int_str(1, "A") # Accepted
await takes_int_str("B", 2) # Correctly rejected by the type checker
```

---
## Новая функция inspect.get_annotations

```python
from __future__ import annotations
from pprint import pprint
from typing import Any, TypeAlias
import inspect


DictStrAny: TypeAlias = dict[str, str | int | float | bool]


def send_show(device: DictStrAny, command: str) -> str:
    pass


def connect_devices(devices: list[DictStrAny], command: str) -> dict[Any, str]:
    pass


if __name__ == "__main__":
    pprint(inspect.get_annotations(connect_devices))
    pprint(inspect.get_annotations(connect_devices, eval_str=True))
```

Вывод:
```python
{'command': 'str', 'devices': 'list[DictStrAny]', 'return': 'dict[Any, str]'}
{'command': <class 'str'>,
 'devices': list[dict[str, str | int | float | bool]],
 'return': dict[typing.Any, str]}
```

---
## Асинхронные итераторы. Добавлены функции aiter, anext


