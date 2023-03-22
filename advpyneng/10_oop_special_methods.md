# Основы ООП

---
# Специальные методы

---
## Одно подчеркивание перед именем

Одно подчеркивание перед именем указывает, что имя используется как внутреннее, что этот объект является внутренней особенностью реализации и не стоит его использовать напрямую.

```python
class Switch:
    def _get_display_str(self):
        hostname = getattr(self, 'hostname', None)
        model = getattr(self, 'model', None)
        return 'Hostname: {}, Model: {}'.format(hostname, model)

    def __str__(self):
        return self._get_display_str()
```

---
## Два подчеркивания перед именем

Два подчеркивания перед именем метода используются не просто как
договоренность. Такие имена трансформируются в формат «имя класса + имя
метода». Это позволяет создавать уникальные методы и атрибуты классов.

Такое преобразование выполняется только в том случае, если в конце менее двух
подчеркиваний или нет подчеркиваний.

```python
class Switch(object):
    __quantity = 0

    def __configure(self):
        pass


In [15]: dir(Switch)
Out[15]:
['_Switch__configure', '_Switch__quantity', ...]
```


---
## [Специальные методы](https://rszalski.github.io/magicmethods/)

---
## Методы ``__str__``, ``__repr__``

---
### Методы ``__str__``, ``__repr__``

Специальные методы ``__str__`` и ``__repr__`` отвечают за строковое представления объекта

```python
In [1]: from datetime import datetime

In [2]: now = datetime.now()

In [3]: str(now)
Out[3]: '2021-08-22 06:48:39.978094'

In [4]: repr(now)
Out[4]: 'datetime.datetime(2021, 8, 22, 6, 48, 39, 978094)'
```

---
### `__str__`

Метод `__str__` отвечает за строковое отображение информации об объекте. Он вызывается при использовании str и print:

```python
class Switch:
    def __init__(self, hostname, model):
        self.hostname = hostname
        self.model = model

    def __str__(self):
        return f'Switch: {self.hostname}'

In [5]: sw1 = Switch('sw1', 'Cisco 3850')

In [6]: print(sw1)
Switch: sw1

In [7]: str(sw1)
Out[7]: 'Switch: sw1'
```

---
### Операторы сравнения

| Оператор | Метод |
|:--------:|-------|
| == | ``__eq__`` |
| != | ``__ne__`` |
| < | ``__lt__`` |
| > | ``__gt__`` |
| <= | ``__le__`` |
| >= | ``__ge__`` |

---
### Арифметические операции

| Оператор | Метод |
|:--------:|-------|
| + | ``__add__`` |
| - | ``__sub__`` |
| * | ``__mul__`` |
| / | ``__div__`` |


```python
import ipaddress

class IPAddress:
    def __init__(self, ip):
        self.ip = ip

    def __str__(self):
        return f"IPAddress: {self.ip}"

    def __repr__(self):
        return f"IPAddress('{self.ip}')"

    def __add__(self, other):
        ip_int = int(ipaddress.ip_address(self.ip))
        sum_ip_str = str(ipaddress.ip_address(ip_int + other))
        return IPAddress(sum_ip_str)
```

---
### Reflected arithmetic operators

Если при выполнении ``a + b`` выясняется, что у ``a`` нет метода ``__add__`` или он возвращает NotImplemented,
вызывается ``b.__radd__(a)``.

```python
In [3]: ip1 = IPAddress("10.1.1.1")

In [4]: ip1 + 1
Out[4]: IPAddress('10.1.1.2')

In [5]: 1 + ip1
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-5-8661cec8f69e> in <module>
----> 1 1 + ip1

TypeError: unsupported operand type(s) for +: 'int' and 'IPAddress'
```

---
### Augmented assignment

```python
In [18]: ip1 = IPAddress("10.1.1.1")

In [19]: ip1 += 1

In [20]: ip1
Out[20]: IPAddress('10.1.1.2')
```

```python
__iadd__(self, other)
```

---
## Протоколы

---
### Протоколы

Специальные методы отвечают не только за поддержку операций типа сложение,
сравнение, но и за поддержку протоколов. Протокол - это набор методов, которые
должны быть реализованы в объекте, чтобы он поддерживал определенное поведение.
Например, в Python есть такие протоколы: итерации, менеджер контекста,
контейнеры и другие. После создания в объекте определенных методов,
объект будет вести себя как встроенный и использовать интерфейс понятный всем, кто пишет на Python.

---
### [Протоколы](https://docs.python.org/3/library/collections.abc.html#module-collections.abc)

|ABC                           |Inherits from         |Abstract Methods       |Mixin Methods
|--------------------------|-------------------------|---------------------|-----------------------------|
|``Container``       |                      |``__contains__``
|``Hashable``         |                      |``__hash__``
|``Iterable``    |                      |``__iter__``
|``Iterator``         |``Iterable``     |``__next__``           |``__iter__``
|``Reversible``       |``Iterable``     |``__reversed__``
|``Generator``        |``Iterator``     |``send``, ``throw``    |``close``, ``__iter__``, ``__next__``
|``Sized``          |                      |``__len__``
|``Callable``        |                      |``__call__``
|``Collection``      |``Sized``,       |``__contains__``,
|                    |``Iterable``,    |``__iter__``,
|                    |``Container``    |``__len__``
|``Sequence``             |``Reversible``,  |``__getitem__``,       |``__contains__``, ``__iter__``, ``__reversed__``,
|                              |``Collection``   |``__len__``            |``index``, and ``count``
|``MutableSequence``      |``Sequence``     |``__getitem__``,       |Inherited ``Sequence`` methods and
|                              |                      |``__setitem__``,       |``append``, ``reverse``, ``extend``, ``pop``,
|                              |                      |``__delitem__``,       |``remove``, and ``__iadd__``
|                              |                      |``__len__``,
|                              |                      |``insert``
|``ByteString``           |``Sequence``     |``__getitem__``,       |Inherited ``Sequence`` methods
|                              |                      |``__len__``
|``Set``                  |``Collection``   |``__contains__``,      |``__le__``, ``__lt__``, ``__eq__``, ``__ne__``,
|                              |                      |``__iter__``,          |``__gt__``, ``__ge__``, ``__and__``, ``__or__``,
|                              |                      |``__len__``            |``__sub__``, ``__xor__``, and ``isdisjoint``
|``MutableSet``           |``Set``          |``__contains__``,      |Inherited ``Set`` methods and
|                              |                      |``__iter__``,          |``clear``, ``pop``, ``remove``, ``__ior__``,
|                              |                      |``__len__``,           |``__iand__``, ``__ixor__``, and ``__isub__``
|                              |                      |``add``,
|                              |                      |``discard``
|``Mapping``              |``Collection``   |``__getitem__``,       |``__contains__``, ``keys``, ``items``, ``values``,
|                              |                      |``__iter__``,          |``get``, ``__eq__``, and ``__ne__``
|                              |                      |``__len__``
|``MutableMapping``       |``Mapping``      |``__getitem__``,       |Inherited ``Mapping`` methods and
|                              |                      |``__setitem__``,       |``pop``, ``popitem``, ``clear``, ``update``,
|                              |                      |``__delitem__``,       |and ``setdefault``
|                              |                      |``__iter__``,
|                              |                      |``__len__``
|``MappingView``          |``Sized``        |                       |``__len__``
|``ItemsView``            |``MappingView``, |                       |``__contains__``,
|                              |``Set``          |                       |``__iter__``
|``KeysView``             |``MappingView``, |                       |``__contains__``,
|                              |``Set``          |                       |``__iter__``
|``ValuesView``           |``MappingView``, |                       |``__contains__``, ``__iter__``
|                              |``Collection``
|``Awaitable``        |                      |``__await__``
|``Coroutine``        |``Awaitable``    |``send``, ``throw``    |``close``
|``AsyncIterable``    |                      |``__aiter__``
|``AsyncIterator``    |``AsyncIterable``|``__anext__``          |``__aiter__``
|``AsyncGenerator``   |``AsyncIterator``|``asend``, ``athrow``  |``aclose``, ``__aiter__``, ``__anext__``

---
### Протокол итерации

Итерируемый объект (iterable) - это объект, который способен возвращать элементы по одному.
Для Python это любой объект у которого есть метод ``__iter__`` или метод ``__getitem__``.
Если у объекта есть метод ``__iter__``, итерируемый объект превращается в итератор вызовом ``iter(name)``,
где name - имя итерируемого объекта. Если метода ``__iter__`` нет, Python перебирает элементы используя ``__getitem__``.


Итератор (iterator) - это объект, который возвращает свои элементы по одному за раз.
С точки зрения Python - это любой объект, у которого есть метод ``__next__``. Этот метод возвращает следующий элемент, если он есть, или возвращает исключение ``StopIteration``, когда элементы закончились.
Кроме того, итератор запоминает, на каком объекте он остановился в последнюю итерацию.
Также у каждого итератора присутствует метод ``__iter__`` - то есть, любой итератор является итерируемым объектом. Этот метод возвращает сам итератор.

---
### Iterable

An object capable of returning its members one at a time. Examples of iterables
include all sequence types (such as list, str, and tuple) and some non-sequence
types like dict, file objects, and objects of any classes you define with an
``__iter__`` method or with a ``__getitem__`` method that implements sequence
semantics.

Iterables can be used in a for loop and in many other places where a sequence
is needed (``zip``, ``map``, …). When an iterable object is passed as an argument
to the built-in function ``iter``, it returns an iterator for the object. This
iterator is good for one pass over the set of values. When using iterables, it
is usually not necessary to call ``iter()`` or deal with iterator objects yourself.
The for statement does that automatically for you, creating a temporary unnamed
variable to hold the iterator for the duration of the loop. See also iterator,
sequence, and generator.

### Iterator

An object representing a stream of data. Repeated calls to the iterator’s
``__next__`` method (or passing it to the built-in function ``next``) return
successive items in the stream. When no more data are available a StopIteration
exception is raised instead. At this point, the iterator object is exhausted
and any further calls to its ``__next__`` method just raise StopIteration again.

Iterators are required to have an ``__iter__`` method that returns the iterator
object itself so every iterator is also iterable and may be used in most places
where other iterables are accepted. One notable exception is code which
attempts multiple iteration passes. A container object (such as a list)
produces a fresh new iterator each time you pass it to the ``iter`` function or
use it in a for loop. Attempting this with an iterator will just return the
same exhausted iterator object used in the previous iteration pass, making it
appear like an empty container.


CPython implementation detail: CPython does not consistently apply the
requirement that an iterator define ``__iter__``.

---
### Iterable

```python
class TodoList:
    def __init__(self, tasks=None):
        if tasks is None:
            self._tasks = []
        else:
            self._tasks = list(tasks)

    def __getitem__(self, index):
        return self._tasks[index]
```

---
### Iterable

```python
class TodoList:
    def __init__(self, tasks=None):
        if tasks is None:
            self._tasks = []
        else:
            self._tasks = list(tasks)

    def __iter__(self):
        return iter(self._tasks)
```

### Iterator

```python
class MyRepeat:
    def __init__(self, value):
        self.value = value

    def __next__(self):
        return self.value

    def __iter__(self):
        return self
```

---
## Итератор itertools.repeat

```python
from concurrent.futures import ThreadPoolExecutor
from itertools import repeat


def send_show(device_dict, command):
    host = device_dict["host"]
    with Netmiko(**device_dict) as conn:
        conn.enable()
        output = conn.send_command(command)
        return output


def send_show_to_devices(device_list, command, output_file, threads=5):
    host_output_dict = {}
    with ThreadPoolExecutor(max_workers=threads) as ex:
        all_results = ex.map(send_show, device_list, repeat(command))
        for device, out in zip(device_list, all_results):
            host = device["host"]
            host_output_dict[host] = out
    return host_output_dict

```

---
### Протокол последовательности

В самом базовом варианте, протокол последовательности (sequence) включает
два метода: __len__ и __getitem__. В более полном варианте также методы:
__contains__, __iter__, __reversed__, index и count. Если последовательность изменяема,
добавляются еще несколько методов.

```python
class Network:
    def __init__(self, network):
        self.network = network
        subnet = ipaddress.ip_network(self.network)
        self.addresses = [str(ip) for ip in subnet.hosts()]

    def __iter__(self):
        return iter(self.addresses)

    def __len__(self):
        return len(self.addresses)

    def __getitem__(self, index):
        return self.addresses[index]
```

---
### Менеджер контекста

---
### Менеджер контекста

Для использования объекта в менеджере контекста, в классе должны быть определены методы `__enter__` и `__exit__`:

* `__enter__` выполняется в начале блока with и, если метод возвращает значение, оно присваивается в переменную, которая стоит после as.
* `__exit__` гарантированно вызывается после блока with, даже если в блоке возникло исключение.

---
### Менеджер контекста


```python
with open(filename) as f:
    content = f.read()
```


```python
with ConnectHandler(**device) as ssh:
    ssh.enable()
    result = ssh.send_command(command)
```


---
### Менеджер контекста

```python
with pytest.raises(TypeError):
    check_ip(100)
```

```python
with click.progressbar(devices, label="Подключение к устройствам:") as bar:
    for device in bar:
```

```python
with ThreadPoolExecutor(max_workers=30) as executor:
    result = executor.map(connect_ssh, devices, repeat(command))
```

---
### Менеджер контекста

```python
con = sqlite3.connect('sw_inventory3.db')

with con:
    query = 'INSERT into switch values (?, ?, ?, ?)'
    con.executemany(query, data)
```

```python
with threading.Lock:
    value = counter
```

---
### Менеджер контекста

```python
with pytest.raises(TypeError):
    check_ip(100)
```

```python
with click.progressbar(devices, label="Подключение к устройствам:") as bar:
    for device in bar:
```

```python
with open(filename) as f:
    content = f.read()
```

```python
with con:
    query = 'INSERT into switch values (?, ?, ?, ?)'
    con.executemany(query, data)
```

```python
with ConnectHandler(**device) as ssh:
    ssh.enable()
    result = ssh.send_command(command)
```

```python
with ThreadPoolExecutor(max_workers=30) as executor:
    result = executor.map(connect_ssh, devices, repeat(command))
```

```python
with threading.Lock:
    value = counter
```


---
### Менеджер контекста

```python
class CiscoSSH:
    def __init__(self, **device_params):
        self.ssh = netmiko.ConnectHandler(**device_params)
        self.ssh.enable()
        print('CiscoSSH __init__ called')

    def __enter__(self):
        print('CiscoSSH __enter__ called')
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print('CiscoSSH __exit__ called')
        self.ssh.disconnect()
```

---
### Менеджер контекста

```python
with CiscoSSH(**DEVICE_PARAMS) as r1:
    print('Внутри with')
    print(r1.ssh.send_command('sh ip int br'))

CiscoSSH __init__ called
CiscoSSH __enter__ called
Внутри with
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
Ethernet0/2                190.16.200.1    YES NVRAM  up                    up
Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
CiscoSSH __exit__ called
```

---
### Менеджер контекста

Если внутри блока with возникает исключение, оно будет сгенерировано после выполнения метода `__exit__`:
```python
with CiscoSSH(**DEVICE_PARAMS) as r1:
    print('Внутри with')
    print(r1.ssh.send_command('sh ip int br'))
    raise ValueError('Ошибка')

CiscoSSH __init__ called
CiscoSSH __enter__ called
Внутри with
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
Ethernet0/2                190.16.200.1    YES NVRAM  up                    up
Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
CiscoSSH __exit__ called
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-10-4e8b17370785> in <module>()
      2     print('Внутри with')
      3     print(r1.ssh.send_command('sh ip int br'))
----> 4     raise ValueError('Ошибка')

ValueError: Ошибка
```

---
### Менеджер контекста

Если метод `__exit__` возвращает истинное значение, исключение не генерируется:
```python
class CiscoSSH:
    def __init__(self, **device_params):
        self.ssh = netmiko.ConnectHandler(**device_params)
        self.ssh.enable()
        print('CiscoSSH __init__ called')

    def __enter__(self):
        print('CiscoSSH __enter__ called')
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print('CiscoSSH __exit__ called')
        self.ssh.disconnect()
        return True
```

---
### Менеджер контекста

```python
with CiscoSSH(**DEVICE_PARAMS) as r1:
    print('Внутри with')
    print(r1.ssh.send_command('sh ip int br'))
    raise ValueError('Ошибка')

CiscoSSH __init__ called
CiscoSSH __enter__ called
Внутри with
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
Ethernet0/2                190.16.200.1    YES NVRAM  up                    up
Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
CiscoSSH __exit__ called
```

