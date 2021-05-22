# What's new in Python 3.9

---

# What's new in Python 3.9

* новые модули graphlib, zoneinfo
* новые методы строк removeprefix, removesuffix
* новые операции со словарями `|` и `|=`
* в аннотации типов вместо List/Dict/Tuple можно писать list/dict/tuple
* новый тип typing.Annotated
* в декораторах можно писать плюс-минус любое выражение, которое возвращает правильный вызываемый объект
* в asyncio новые корутины: asyncio.to_thread (вместо loop.run_in_executor), loop.shutdown_default_executor

---

## Новые методы строк removeprefix, removesuffix

```python
In [4]: s1 = "interface Gi0/1"

In [5]: s1.removeprefix("interface")
Out[5]: ' Gi0/1'

In [6]: s1.removeprefix("interface ")
Out[6]: 'Gi0/1'

In [7]: s1.removeprefix("test")
Out[7]: 'interface Gi0/1'

In [8]: s1.removesuffix("0/1")
Out[8]: 'interface Gi'
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

---

## Синтаксис декораторов

[PEP 614 Relaxing Grammar Restrictions On Decorators](https://www.python.org/dev/peps/pep-0614/)

```python
def force_arg_type(required_type):
    def decorator(func):
        @wraps(func)
        def inner(*args):
            if not all([isinstance(arg, required_type) for arg in args]):
                raise ValueError(
                    f"Все аргументы должны быть {required_type.__name__}"
                )
            return func(*args)
        return inner
    return decorator


force_type = {
    "numbers": force_arg_type(int),
    "strings": force_arg_type(str),
}


@force_type["numbers"]
def summ(a, b):
    return a + b
```

---

### До Python3.9 это ошибка синтаксиса

```python
$ python3.8 ex05_decorator.py
  File "ex05_decorator.py", line 23
    @force_type["numbers"]
               ^
SyntaxError: invalid syntax

```

---

## Обновления в asyncio

* new coroutine [loop.shutdown_default_executor](https://docs.python.org/3/library/asyncio-eventloop.html#asyncio.loop.shutdown_default_executor)
* new coroutine [asyncio.to_thread](https://docs.python.org/3/library/asyncio-task.html#asyncio.to_thread)

---

## asyncio.to_thread

Сопрограмма asyncio.to_thread позволяет запустить блокирующую операцию в потоке.

До Python 3.9 использовалась loop.run_in_executor. При этом to_thread это по
сути обертка вокруг loop.run_in_executor с использованием ThreadPoolExecutor
по умолчанию, с максимальным количеством потоков по умолчанию:

```python
async def to_thread(func, /, *args, **kwargs):
    loop = events.get_running_loop()
    ctx = contextvars.copy_context()
    func_call = functools.partial(ctx.run, func, *args, **kwargs)
    return await loop.run_in_executor(None, func_call)
```

Начиная с версии Python 3.8 значение по умолчанию для max_workers высчитывается так

```
min(32, os.cpu_count() + 4)
```

---

## Новые модули

* [graphlib](https://docs.python.org/3/library/graphlib.html#module-graphlib)
* [zoneinfo](https://docs.python.org/3/library/zoneinfo.html#module-zoneinfo)

---

### graphlib

Модуль graphlib предоставляет функциональность топологической
сортировки графа.

Топологическая сортировка — упорядочивание вершин бесконтурного ориентированного
графа согласно частичному порядку, заданному ребрами орграфа на множестве его вершин.

---

### graphlib

```python
from graphlib import TopologicalSorter

dependencies = {
    "realpython-reader": {"feedparser", "html2text"},
    "feedparser": {"sgmllib3k"},
}

ts = TopologicalSorter(dependencies)
print(list(ts.static_order()))
# ['html2text', 'sgmllib3k', 'feedparser', 'realpython-reader']
```

---

### graphlib

```
             |- SW11
     |- SW2 -|- SW12
SW1 -|
     |- SW3 -|- SW13
             |- SW14
```

```python
from graphlib import TopologicalSorter


topology = {
    "SW1": ["SW2", "SW3"],
    "SW2": ["SW11", "SW12"],
    "SW3": ["SW13", "SW14"]
}

top = TopologicalSorter(topology)
print(list(top.static_order()))
# ['SW11', 'SW12', 'SW13', 'SW14', 'SW2', 'SW3', 'SW1']
```

---

### graphlib

```python
tasks_dict = {
    "Подготовка VM": [
        "Обновить Python до 3.9",
        "Клонировать репозиторий курса",
        "Установить pyneng.py",
    ],
    "Клонировать репозиторий курса": [
        "Подготовка репозитория",
    ],
    "Подготовка репозитория": [
        "Исправить ошибки в тестах",
        "Исправить ошибки в заданиях",
    ],
    "Исправить ошибки в тестах": [
        "Изменить в тестах порядок в assert",
        "Вынести общие функции в pyneng_common_functions",
    ],
    "Исправить ошибки в заданиях": ["Исправить ошибки в заданиях из списка todo"],
}

tasks = TopologicalSorter(tasks_dict)
pprint(list(tasks.static_order()))
['Обновить Python до 3.9',
 'Установить pyneng.py',
 'Изменить в тестах порядок в assert',
 'Вынести общие функции в pyneng_common_functions',
 'Исправить ошибки в заданиях из списка todo',
 'Исправить ошибки в тестах',
 'Исправить ошибки в заданиях',
 'Подготовка репозитория',
 'Клонировать репозиторий курса',
 'Подготовка VM']
```

---

### zoneinfo

```python
In [1]: import zoneinfo

In [2]: len(zoneinfo.available_timezones())
Out[2]: 608
```

Если во второй строке значение 0, можно поставить локальную базу из pip:
```
pip install tzdata
```

---

### zoneinfo

```python
from datetime import datetime, timezone, timedelta
from zoneinfo import ZoneInfo

In [16]: meeting = datetime(2021, 5, 16, 10, 0, tzinfo=ZoneInfo("Europe/Kiev"))

In [17]: meeting
Out[17]: datetime.datetime(2021, 5, 16, 10, 0, tzinfo=zoneinfo.ZoneInfo(key='Europe/Kiev'))

In [18]: meeting.tzinfo
Out[18]: zoneinfo.ZoneInfo(key='Europe/Kiev')

In [19]: meeting.utcoffset()
Out[19]: datetime.timedelta(seconds=10800)

In [21]: h = timedelta(hours=1)

In [22]: meeting.utcoffset() / h
Out[22]: 3.0
```
---

### zoneinfo

```python

In [5]: meeting
Out[5]: datetime.datetime(2021, 5, 16, 10, 0, tzinfo=zoneinfo.ZoneInfo(key='Europe/Kiev'))

In [6]: meeting.astimezone(ZoneInfo("Australia/Melbourne"))
Out[6]: datetime.datetime(2021, 5, 16, 17, 0, tzinfo=zoneinfo.ZoneInfo(key='Australia/Melbourne'))
```

---

## Annual release cycle

[PEP 602, CPython adopts an annual release cycle](https://www.python.org/dev/peps/pep-0602)

---

## Performance ([microseconds](https://en.wikipedia.org/wiki/Microsecond))

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

---

## Мелкие изменения

script.py

```python
print(__file__)
```

```
$ python3.8 script.py
ex01_dict_union_class.py

$ python3.9 script.py
/home/vagrant/repos/pyneng-bonus-lectures/examples/08_python39/ex01_dict_union_class.py
```

---

### Улучшен help в typing

Python 3.8
```python
In [4]: Union?
Signature:   Union(*args, **kwds)
Type:        _SpecialForm
String form: typing.Union
File:        /usr/local/lib/python3.8/typing.py
Docstring:
Internal indicator of special typing constructs.
See _doc instance attribute for specific docs.
```

Python 3.9

```python
In [3]: Union?
Signature:   Union(*args, **kwds)
Type:        _SpecialForm
String form: typing.Union
File:        /usr/local/lib/python3.9/typing.py
Docstring:
Union type; Union[X, Y] means either X or Y.

To define a union, use e.g. Union[int, str].  Details:
- The arguments must be types and there must be at least one.
- None as an argument is a special case and is replaced by
  type(None).
- Unions of unions are flattened, e.g.::

    Union[Union[int, str], float] == Union[int, str, float]

- Unions of a single argument vanish, e.g.::

    Union[int] == int  # The constructor actually returns int

- Redundant arguments are skipped, e.g.::

    Union[int, str, int] == Union[int, str]

- When comparing unions, the argument order is ignored, e.g.::

    Union[int, str] == Union[str, int]

- You cannot subclass or instantiate a union.
- You can use Optional[X] as a shorthand for Union[X, None].
```

---

### Новый параметр cancel_futures в concurrent.futures.Executor.shutdown

Cancels all pending futures which have not started running, instead of waiting
for them to complete before shutting down the executor.

```python
In [1]: from concurrent.futures import ThreadPoolExecutor

In [2]: ex = ThreadPoolExecutor()

In [3]: ex.shutdown?
Signature: ex.shutdown(wait=True, *, cancel_futures=False)
Docstring:
Clean-up the resources associated with the Executor.

It is safe to call this method several times. Otherwise, no other
methods can be called after this one.

Args:
    wait: If True then shutdown will not return until all running
        futures have finished executing and the resources used by the
        executor have been reclaimed.
    cancel_futures: If True then shutdown will cancel all pending
        futures. Futures that are completed or running will not be
        cancelled.
File:      /usr/local/lib/python3.9/concurrent/futures/thread.py
Type:      method

```

---

### Модуль ipaddress

До 3.9.5

```python
In [4]: import ipaddress

In [6]: ipaddress.ip_address("02.1.1.1")
Out[6]: IPv4Address('2.1.1.1')
```

Начиная с 3.9.5

```python
In [3]: ipaddress.ip_address("02.1.1.1")
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-3-d2deb7abae82> in <module>
----> 1 ipaddress.ip_address("02.1.1.1")
...

ValueError: '02.1.1.1' does not appear to be an IPv4 or IPv6 address
```

---

### replace с явным count

python 3.8

```python
Python 3.8.0 (default, Nov  9 2019, 12:40:50)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.18.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]: "".replace("", "a", 1)
Out[1]: ''
```

python 3.9

```python
$ ipython
Python 3.9.4 (default, Apr 15 2021, 06:05:52)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.23.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]: "".replace("", "a", 1)
Out[1]: 'a'
```

