## What's new in Python 3.9

---

## Новые методы строк removeprefix, removesuffix

```python
In [24]: s1 = "interface Gi0/1"

In [25]: s1.removeprefix("interface")
Out[25]: ' Gi0/1'

In [26]: s1.removeprefix("interface ")
Out[26]: 'Gi0/1'

In [27]: s1.removeprefix("test")
Out[27]: 'interface Gi0/1'

In [28]: s1.removesuffix("0/1")
Out[28]: 'interface Gi'
```

---

## Dict union operators

---

### Operator `|`

```python
In [1]: d1 = {"name": "R1", "vendor": "Cisco"}

In [2]: d2 = {"location": "Globe Str", "vendor": "Cisco IOS"}

In [3]: d1 | d2
Out[3]: {'name': 'R1', 'vendor': 'Cisco IOS', 'location': 'Globe Str'}

In [4]: d1
Out[4]: {'name': 'R1', 'vendor': 'Cisco'}

In [5]: d2
Out[5]: {'location': 'Globe Str', 'vendor': 'Cisco IOS'}
```

---

### Operator `|=`

```python
In [6]: d1 |= d2

In [7]: d1
Out[7]: {'name': 'R1', 'vendor': 'Cisco IOS', 'location': 'Globe Str'}

In [8]: d2
Out[8]: {'location': 'Globe Str', 'vendor': 'Cisco IOS'}
```

---

### Операторы работают не только на обычных словарях

```python
In [11]: vlans1 = defaultdict(list, {"sw1": [1, 2, 3, 4], "sw2": [3, 4], "sw3": [1, 3]})

In [12]: vlans2 = defaultdict(list, {"sw1": [1, 2, 3, 4, 5], "sw4": [1, 2, 3]})

In [13]: vlans1 | vlans2
Out[13]:
defaultdict(list,
            {'sw1': [1, 2, 3, 4, 5],
             'sw2': [3, 4],
             'sw3': [1, 3],
             'sw4': [1, 2, 3]})
```

---

### Union как специальный метод

```python
class Topology:
    def __init__(self, topology_dict):
        self.topology = self._normalize(topology_dict)

    def _normalize(self, topology_dict):
        normalized_topology = {}
        for box, neighbor in topology_dict.items():
            if not neighbor in normalized_topology:
                normalized_topology[box] = neighbor
        return normalized_topology

    def __or__(self, other):
        return Topology(self.topology | other.topology)
```

---

## Аннотация типов

* [PEP 585, type hinting generics in standard collections](https://www.python.org/dev/peps/pep-0585)
* [PEP 593, flexible function and variable annotations](https://www.python.org/dev/peps/pep-0593)


---

### Начиная с 3.9 вместо List/Dict/Tuple можно писать list/dict/tuple

```python
def ping_ip_list(ip_list: list[str], limit: int = 3) -> tuple[list[str], list[str]]:
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

def ping_ip_list_2(ip_list: List[str], limit: int = 3) -> Tuple[List[str], List[str]]:
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

---

### В typing.get_type_hints добавлен параметр include_extras

```python
get_type_hints(ping_ip)
{'ip': <class 'str'>, 'return': <class 'bool'>}
```

```python
get_type_hints(ping_ip, include_extras=True)
{'ip': typing.Annotated[str, 'IP address'], 'return': <class 'bool'>}
```

---

## Синтаксис декораторов

---

## Обновления в asyncio

* new coroutine loop.shutdown_default_executor()
* new coroutine asyncio.to_thread()

---

## Новые модули

* graphlib
* zoneinfo

---

## Performance

```
Python version                       3.4     3.5     3.6     3.7     3.8    3.9
--------------                       ---     ---     ---     ---     ---    ---

Variable and attribute read access:
    read_local                       7.1     7.1     5.4     5.1     3.9    3.9
    read_nonlocal                    7.1     8.1     5.8     5.4     4.4    4.5
    read_global                     15.5    19.0    14.3    13.6     7.6    7.8
    read_builtin                    21.1    21.6    18.5    19.0     7.5    7.8
    read_classvar_from_class        25.6    26.5    20.7    19.5    18.4   17.9
    read_classvar_from_instance     22.8    23.5    18.8    17.1    16.4   16.9
    read_instancevar                32.4    33.1    28.0    26.3    25.4   25.3
    read_instancevar_slots          27.8    31.3    20.8    20.8    20.2   20.5
    read_namedtuple                 73.8    57.5    45.0    46.8    18.4   18.7
    read_boundmethod                37.6    37.9    29.6    26.9    27.7   41.1

Variable and attribute write access:
    write_local                      8.7     9.3     5.5     5.3     4.3    4.3
    write_nonlocal                  10.5    11.1     5.6     5.5     4.7    4.8
    write_global                    19.7    21.2    18.0    18.0    15.8   16.7
    write_classvar                  92.9    96.0   104.6   102.1    39.2   39.8
    write_instancevar               44.6    45.8    40.0    38.9    35.5   37.4
    write_instancevar_slots         35.6    36.1    27.3    26.6    25.7   25.8

Data structure read access:
    read_list                       24.2    24.5    20.8    20.8    19.0   19.5
    read_deque                      24.7    25.5    20.2    20.6    19.8   20.2
    read_dict                       24.3    25.7    22.3    23.0    21.0   22.4
    read_strdict                    22.6    24.3    19.5    21.2    18.9   21.5

Data structure write access:
    write_list                      27.1    28.5    22.5    21.6    20.0   20.0
    write_deque                     28.7    30.1    22.7    21.8    23.5   21.7
    write_dict                      31.4    33.3    29.3    29.2    24.7   25.4
    write_strdict                   28.4    29.9    27.5    25.2    23.1   24.5

Stack (or queue) operations:
    list_append_pop                 93.4   112.7    75.4    74.2    50.8   50.6
    deque_append_pop                43.5    57.0    49.4    49.2    42.5   44.2
    deque_append_popleft            43.7    57.3    49.7    49.7    42.8   46.4

Timing loop:
    loop_overhead                    0.5     0.6     0.4     0.3     0.3    0.3
```

