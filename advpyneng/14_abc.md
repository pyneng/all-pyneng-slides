## Abstract Base Classes (ABC)

---
## Abstract Base Classes (ABC)

Иногда, при создании иерархии классов, необходимо чтобы ряд классов поддерживал
одинаковый интерфейс, например, одинаковый набор методов. Частично эту задачу
можно решить с помощью наследования, однако далеко не всегда дочерним классам
подойдет реализация метода из родительского класса.

Абстрактный класс - это класс в котором созданы абстрактные методы - методы,
которые обязательно должны присутствовать в дочерних классах. Создавать экзепмляр
абстрактного класса нельзя, его надо наследовать и уже у дочернего класса можно
создать экземпляр. При этом экземпляр дочернего класса можно создать только в том случае,
если у дочернего класса есть реализация всех абстрактных методов.

---
## Abstract Base Classes (ABC)

```python
import abc

class Parent(abc.ABC):
    @abc.abstractmethod
    def get_info(self, parameter):
        """Get parameter info"""

    @abc.abstractmethod
    def set_info(self, parameter, value):
        """Set parameter to value"""
```

Нельзя создать экземпляр класса Parent:

```python

In [3]: p1 = Parent()
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-3-0b1eb161869e> in <module>
----> 1 p1 = Parent()

TypeError: Can't instantiate abstract class Parent with abstract methods get_info, set_info
```

---
## Abstract Base Classes (ABC)

Дочерний класс обязательно должен добавить свою реализацию абстрактных методов,
иначе при создании экземпляра возникнет исключение:

```python
In [4]: class Child(Parent):
   ...:     pass
   ...:

In [5]: c1 = Child()
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-5-07f905b6a091> in <module>
----> 1 c1 = Child()

TypeError: Can't instantiate abstract class Child with abstract methods get_info, set_info
```

---
## Abstract Base Classes (ABC)

После создания методов get_info и set_info, можно создать экземпляр класса Child:

```python
class Child(Parent):
    def ``__init__(self):
        self._parameters = {}

    def get_info(self, parameter):
        return self._parameters.get(parameter)

    def set_info(self, parameter, value):
        self._parameters[parameter] = value
        return self._parameters


In [7]: c1 = Child()

In [8]: c1.set_info('name', 'BB-8')
Out[8]: {'name': 'BB-8'}
```

---
## Пример использования ABC
## scrapli transport classes

```
    +-------------------> BaseTransport(ABC) <-----------------+
    |                                                          |
    |                                                          |
    |                                                          |
Transport(ABC) <---------+                     +-------> AsyncTransport(ABC)
    ^                    |                     |               ^
    |                    |                     |               |
    |                    |                     |               |
    |                    |                     |               |
ParamikoTransport  TelnetTransport    AsynctelnetTransport  AsyncSSH
```

---
## Пример использования ABC
## scrapli transport classes

```python
class BaseTransport(ABC):
    def ``__init__(self, base_transport_args: BaseTransportArgs) -> None:
        self._base_transport_args = base_transport_args

        self.logger = get_instance_logger(
            instance_name="scrapli.transport",
            host=self._base_transport_args.host,
            port=self._base_transport_args.port,
            uid=self._base_transport_args.logging_uid,
        )

    @abstractmethod
    def close(self) -> None:
        """Close the transport session"""

    @abstractmethod
    def write(self, channel_input: bytes) -> None:
        """Write bytes into the transport session"""

    @abstractmethod
    def isalive(self) -> bool:
        """Check if transport is alive"""

    def _pre_open_closing_log(self, closing: bool = False) -> None:
        operation = "closing" if closing else "opening"

        self.logger.debug(
            f"{operation} transport connection to '{self._base_transport_args.host}' on port "
            f"'{self._base_transport_args.port}'"
        )

    def _post_open_closing_log(self, closing: bool = False) -> None:
        operation = "closed" if closing else "opened"

        self.logger.debug(
            f"transport connection to '{self._base_transport_args.host}' on port "
            f"'{self._base_transport_args.port}' {operation} successfully"
        )
```


---
## Пример использования ABC
## scrapli transport classes

```python
class Transport(BaseTransport, ABC):
    @abstractmethod
    def open(self) -> None:
        """Open the transport session"""

    @abstractmethod
    def read(self) -> bytes:
        """Read data from the transport session"""


class AsyncTransport(BaseTransport, ABC):
    @abstractmethod
    async def open(self) -> None:
        """Open the transport session"""

    @abstractmethod
    async def read(self) -> bytes:
        """Read data from the transport session"""
```

---
## Абстрактные классы в стандартной библиотеке Python

---
## collections.abc

В стандартной библиотеке Python есть несколько готовых абстрактных классов, которые
можно использовать для наследования или проверки типа объекта.
Большая часть классов находится в ``collections.abc`` и часть из них показана в таблице ниже.

Полный перечень классов ``collections.abc`` доступен в [документации](https://docs.python.org/3/library/collections.abc.html)

|ABC         |Наследует    |Абстрактные методы     |Mixin методы |
|------------|-------------|-----------------------|-------------|
|Container   |             | ``__contains__`` | |
|Hashable    |             | ``__hash__`` | |
|Iterable    |             | ``__iter__`` | |
|Iterator    |Iterable     | ``__next__``               | ``__iter__``|
|Reversible  |Iterable     | ``__reversed__``
|Generator   |Iterator     |send, throw            | close, ``__iter__``, ``__next__`` |
|Sized       |             | ``__len__``
|Callable    |             | ``__call__``
|Collection  |Sized,       | ``__contains__``,
|            |Iterable,    | ``__iter__``,
|            |Container    | ``__len__``
|Sequence    |Reversible,  | ``__getitem__``,      | ``__contains__``, ``__iter__``, ``__reversed__``, |
|            |Collection   | ``__len__``           |index, count

---
## collections.abc UML

* [python](https://bugs.python.org/file47357/base.png)
* [Fluent Python](https://user-images.githubusercontent.com/33891164/39363928-e9ba4520-49e0-11e8-9cc5-273134e16df4.png)
* [dzone](https://dzone.com/articles/just-a-class-diagram-for-python-3-collections-abst)

---
## collections.abc

Один из вариантов использования абстрактных классов collections.abc - это
проверка того поддерживает ли объект протокол.
Например, проверить является ли объект итерируемым можно таким образом:

```python
In [1]: from collections.abc import Iterable

In [2]: l1 = [1, 2, 3]

In [3]: s1 = 'line'

In [4]: n1 = 5

In [5]: isinstance(l1, Iterable)
Out[5]: True

In [6]: isinstance(s1, Iterable)
Out[6]: True

In [7]: isinstance(n1, Iterable)
Out[7]: False
```

---
## collections.abc

Второй вариант использования классов collections.abc - наследование классов
для поддержки определенного интерфейса.

```python
from collections.abc import Sequence
import ipaddress


class Network(Sequence):
    def __init__(self, network):
        self.network = network
        subnet = ipaddress.ip_network(self.network)
        self.addresses = [str(ip) for ip in subnet.hosts()]

```

Пробуем создать экземпляр класса Network:

```python
In [4]: net1 = Network('10.1.1.192/29')
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-4-9c2ed79a8719> in <module>
----> 1 net1 = Network('10.1.1.192/29')

TypeError: Cant instantiate abstract class Network with abstract methods __getitem__, __len__
```


---
## collections.abc

```python
class Network(Sequence):
    def __init__(self, network):
        self.network = network
        subnet = ipaddress.ip_network(self.network)
        self.addresses = [str(ip) for ip in subnet.hosts()]

    def __getitem__(self, index):
        return self.addresses[index]

    def __len__(self):
        return len(self.addresses)
```

Теперь можно создать экземпляр класса Network и экземпляр поддерживает
обращение по индексу, а также работает функция len:

```python
In [6]: net1 = Network('10.1.1.192/29')

In [7]: net1.addresses
Out[7]:
['10.1.1.193',
 '10.1.1.194',
 '10.1.1.195',
 '10.1.1.196',
 '10.1.1.197',
 '10.1.1.198']

In [8]: len(net1)
Out[8]: 6

In [9]: net1[4]
Out[9]: '10.1.1.197'
```

---
## collections.abc

Кроме того, за счет наследования Sequence, в классе появились методы
``__contains__``, ``__iter__``, ``__reversed__``, index и count:

```python
In [10]: '10.1.1.193' in net1
Out[10]: True

In [11]: i = iter(net1)

In [12]: next(i)
Out[12]: '10.1.1.193'

In [13]: next(i)
Out[13]: '10.1.1.194'

In [14]: list(reversed(net1))
Out[14]:
['10.1.1.198',
 '10.1.1.197',
 '10.1.1.196',
 '10.1.1.195',
 '10.1.1.194',
 '10.1.1.193']

In [15]: net1.index('10.1.1.195')
Out[15]: 2

In [16]: net1.count('10.1.1.197')
Out[16]: 1
```
