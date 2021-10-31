# Декораторы методов: classmethod, staticmethod, property

---
## property

---
### property

property позволяет:

* создавать атрибуты только для чтения
* создавать атрибуты при обращении к которым динамически вычисляется значение
* добавлять проверки при установке значения атрибута
* сохранять API модуля изменяя код


---
### property

[scrapli MultiResponse](https://github.com/carlmontanari/scrapli/blob/master/scrapli/response.py#L235)

```python
class MultiResponse(ScrapliMultiResponse):
    @property
    def host(self) -> str:
        try:
            response = self.data[0]
        except IndexError:
            return ""
        host = response.host
        return host

    @property
    def failed(self) -> bool:
        if any(response.failed for response in self.data):
            return True
        return False

    @property
    def result(self) -> str:
        result = ""
        for response in self.data:
            result += "\n".join([response.channel_input, response.result])
        return result
```

---
### property

```python
class IPAddress:
    def __init__(self, address, mask):
        self._address = address
        self._mask = int(mask)

    @property
    def mask(self):
        return self._mask

    @mask.setter
    def mask(self, mask):
        if not isinstance(mask, int):
            raise TypeError("Маска должна быть числом")
        if not mask in range(8, 32):
            raise ValueError("Маска должна быть в диапазоне от 8 до 32")
        self._mask = mask
```

---
### property

Python позволяет создавать и изменять переменные экземпляров:

```python
class Robot:
    def __init__(self, name):
        self.name = name


In [2]: bb8 = Robot('BB-8')

In [3]: bb8.name
Out[3]: 'BB-8'

In [4]: bb8.name = 'R2D2'

In [5]: bb8.name
Out[5]: 'R2D2'
```
---
### property

Однако иногда нужно сделать так чтобы при изменении/установке значения переменной,
проверялся ее тип или диапазон значений, также иногда необходимо сделать переменную
неизменяемой и сделать ее доступной только для чтения.
В некоторых языках программирования для этого используются методы get и set,
например:

```python
class IPAddress:
    def __init__(self, address, mask):
        self._address = address
        self._mask = int(mask)

    def set_mask(self, mask):
        if not isinstance(mask, int):
            raise TypeError("Маска должна быть числом")
        if not mask in range(8, 32):
            raise ValueError("Маска должна быть в диапазоне от 8 до 32")
        self._mask = mask

    def get_mask(self):
        return self._mask


In [10]: ip1 = IPAddress('10.1.1.1', 24)

In [12]: ip1.set_mask(23)

In [13]: ip1.get_mask()
Out[13]: 23
```

---
### property

По сравнению со стандартным синтаксисом обращения к атрибутам,
этот вариант выглядит очень громоздко. В Python есть более компактный
вариант сделать то же самое - property.

Property как правило, используется как декоратор метода и превращает метод
в переменную экземпляра с точки зрения пользователя класса.

```python
class IPAddress:
    def __init__(self, address, mask):
        self._address = address
        self._mask = int(mask)

    @property
    def mask(self):
        return self._mask

In [15]: ip1 = IPAddress('10.1.1.1', 24)

In [16]: ip1.mask
Out[16]: 24
```

---
### property

Переменная становится доступной только для чтения:

```python
In [17]: ip1.mask = 30
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-17-e153170a5893> in <module>
----> 1 ip1.mask = 30

AttributeError: can't set attribute'
```

---
### property setter

Также property позволяет добавлять метод setter, который будет отвечать
за изменение значения переменной и, так как это тоже метод, позволяет
включить логику с проверкой или динамическим вычислением значения.

```python
class IPAddress:
    def __init__(self, address, mask):
        self._address = address
        self._mask = int(mask)

    @property
    def mask(self):
        return self._mask

    @mask.setter
    def mask(self, mask):
        if not isinstance(mask, int):
            raise TypeError("Маска должна быть числом")
        if not mask in range(8, 32):
            raise ValueError("Маска должна быть в диапазоне от 8 до 32")
        self._mask = mask


In [20]: ip1 = IPAddress('10.1.1.1', 24)

In [21]: ip1.mask
Out[21]: 24

In [23]: ip1.mask = 30

In [24]: ip1.mask = 320
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-24-8573933afac9> in <module>
----> 1 ip1.mask = 320

<ipython-input-19-d0e571cd5e2b> in mask(self, mask)
     13             raise TypeError("Маска должна быть числом")
     14         if not mask in range(8, 32):
---> 15             raise ValueError("Маска должна быть в диапазоне от 8 до 32")
     16         self._mask = mask
     17

ValueError: Маска должна быть в диапазоне от 8 до 32
```

---
### Варианты создания property
### Стандартный вариант применения property без setter

```python

class Book:
    def __init__(self, title, price, quantity):
        self.title = title
        self.price = price
        self.quantity = quantity

    # метод, который декорирован property становится getter'ом
    @property
    def total(self):
        print('getter')
        return self.price * self.quantity
```

---
### property
### [Пример из scrapli](https://github.com/carlmontanari/scrapli/blob/master/scrapli/response.py#L235)

```python
class MultiResponse(ScrapliMultiResponse):
    @property
    def host(self) -> str:
        try:
            response = self.data[0]
        except IndexError:
            return ""
        host = response.host
        return host

    @property
    def failed(self) -> bool:
        if any(response.failed for response in self.data):
            return True
        return False

    @property
    def result(self) -> str:
        result = ""
        for response in self.data:
            result += "\n".join([response.channel_input, response.result])
        return result
```


---
### Варианты создания property

### Стандартный вариант применения property с setter

```python

class Book:
    def __init__(self, title, price, quantity):
        self.title = title
        self.price = price
        self.quantity = quantity

    # total остается атрибутом только для чтения
    @property
    def total(self):
        return round(self.price * self.quantity, 2)

    # а price доступен для чтения и записи
    @property # этот метод превращается в getter
    def price(self):
        print('price getter')
        return self._price

    # при записи делается проверка значения
    @price.setter
    def price(self, value):
        print('price setter')
        if not isinstance(value, (int, float)):
            raise TypeError('Значение должно быть числом')
        if not value >= 0:
            raise ValueError('Значение должно быть положительным')
        self._price = float(value)
```


---
### Варианты создания property

### Декораторы с явным setter

```python

class Book:
    def __init__(self, title, price, quantity):
        self.title = title
        self.price = price
        self.quantity = quantity

    # создаем пустую property для total
    total = property()

    @total.getter
    def total(self):
        return round(self.price * self.quantity, 2)

    # создаем пустую property для price
    price = property()

    # позже указываем getter
    @price.getter
    def price(self):
        print('price getter')
        return self._price

    @price.setter
    def price(self, value):
        print('price setter')
        if not isinstance(value, (int, float)):
            raise TypeError('Значение должно быть числом')
        if not value >= 0:
            raise ValueError('Значение должно быть положительным')
        self._price = float(value)
```


---
### Варианты создания property

### property без декораторов

```python

class Book:
    def __init__(self, title, price, quantity):
        self.title = title
        self.price = price
        self.quantity = quantity

    def _get_total(self):
        return round(self.price * self.quantity, 2)

    def _get_price(self):
        print('price getter')
        return self._price

    def _set_price(self, value):
        print('price setter')
        if not isinstance(value, (int, float)):
            raise TypeError('Значение должно быть числом')
        if not value >= 0:
            raise ValueError('Значение должно быть положительным')
        self._price = float(value)

    total = property(_get_total)
    price = property(_get_price, _set_price)
```


---
### Варианты создания property
### Второй вариант property без декораторов 

```python
class Book:
    def __init__(self, title, price, quantity):
        self.title = title
        self.price = price
        self.quantity = quantity

    def _get_total(self):
        return round(self.price * self.quantity, 2)

    def _get_price(self):
        print('price getter')
        return self._price

    def _set_price(self, value):
        print('price setter')
        if not isinstance(value, (int, float)):
            raise TypeError('Значение должно быть числом')
        if not value >= 0:
            raise ValueError('Значение должно быть положительным')
        self._price = float(value)

    total = property()
    total = total.getter(_get_total)

    price = property()
    price = price.getter(_get_price)
    price = price.setter(_set_price)
```

---
## classmethod

Иногда нужно реализовать несколько способов создания экземпяра,
при этом в Python можно создавать только один метод ``__init__``.
Конечно, можно реализовать все варианты в одном ``__init__``,
но при этом часто параметры ``__init__`` становятся или слишком общими,
или их слишком много.

Существует другой вариант решения проблемы - создать альтернативный
конструктор с помощью декоратора classmethod.

---
### classmethod
### dict.fromkeys

Пример альтернативного конструктора в стандартной библиотеке:

```python
In [25]: r1 = {
    ...:     'hostname': 'R1',
    ...:     'OS': 'IOS',
    ...:     'Vendor': 'Cisco'
    ...: }

In [28]: dict.fromkeys(['hostname', 'os', 'vendor'])
Out[28]: {'hostname': None, 'os': None, 'vendor': None}

In [29]: dict.fromkeys(['hostname', 'os', 'vendor'], '')
Out[29]: {'hostname': '', 'os': '', 'vendor': ''}
```

---
### classmethod
### [datetime](https://github.com/python/cpython/blob/3.10/Lib/datetime.py)

```python
class datetime(date):
    @classmethod
    def fromtimestamp(cls, t, tz=None):
        """Construct a datetime from a POSIX timestamp (like time.time()).
        A timezone info object may be passed in as well.
        """
        _check_tzinfo_arg(tz)

        return cls._fromtimestamp(t, tz is not None, tz)

    @classmethod
    def utcfromtimestamp(cls, t):
        """Construct a naive UTC datetime from a POSIX timestamp."""
        return cls._fromtimestamp(t, True, None)

    @classmethod
    def now(cls, tz=None):
        "Construct a datetime from time.time() and optional time zone info."
        t = _time.time()
        return cls.fromtimestamp(t, tz)

    @classmethod
    def utcnow(cls):
        "Construct a UTC datetime from time.time()."
        t = _time.time()
        return cls.utcfromtimestamp(t)
```

---
### classmethod

```python
import time
from textfsm import clitable
from base_ssh import BaseSSH

class CiscoSSH(BaseSSH):
    def __init__(self, ip, username, password, enable_password,
                 disable_paging=True):
        super().__init__(ip, username, password)
        self._ssh.send('enable\n')
        self._ssh.send(enable_password + '\n')
        if disable_paging:
            self._ssh.send('terminal length 0\n')
        time.sleep(1)
        self._ssh.recv(self._MAX_READ)
        self._mgmt_ip = None

    @classmethod
    def default_params(cls, ip):
        params = {
            'ip': ip,
            'username': 'cisco',
            'password': 'cisco',
            'enable_password': 'cisco'}
        return cls(**params)
```

```python

In [8]: r1 = CiscoSSH.default_params('192.168.100.1')

In [9]: r1.send_show_command('sh clock')
Out[9]: '*16:38:01.883 UTC Sun Jan 28 2018'

```

---
## classmethod
## [Пример из scrapli](https://github.com/carlmontanari/scrapli/blob/master/scrapli/factory.py#L233)

```python
class Scrapli(NetworkDriver):
    CORE_PLATFORM_MAP = {
        "arista_eos": EOSDriver,
        "cisco_iosxe": IOSXEDriver,
        "cisco_iosxr": IOSXRDriver,
        "cisco_nxos": NXOSDriver,
        "juniper_junos": JunosDriver,
    }
    DRIVER_MAP = {"network": NetworkDriver, "generic": GenericDriver}

    @classmethod
    def _get_driver_class(
        cls, platform_details: Dict[str, Any], variant: Optional[str]
    ) -> Union[Type[NetworkDriver], Type[GenericDriver]]:
        """
        Fetch community driver class based on platform details
        Args:
            platform_details: dict of details about community platform from scrapli_community
                library
            variant: optional name of variant of community platform
        Returns:
            NetworkDriver: final driver class
        Raises:
            N/A
        """
        final_driver: Union[
            Type[NetworkDriver],
            Type[GenericDriver],
        ]

        if variant and platform_details["variants"][variant].get("driver_type"):
            variant_driver_data = platform_details["variants"][variant].pop("driver_type")
            final_driver = variant_driver_data["sync"]
            return final_driver

        if isinstance(platform_details["driver_type"], str):
            driver_type = platform_details["driver_type"]
            standard_final_driver = cls.DRIVER_MAP.get(driver_type, None)
            if standard_final_driver:
                return standard_final_driver

        final_driver = platform_details["driver_type"]["sync"]
        return final_driver
)
```


---
## staticmethod

Статический метод - это метод, который не привязан к состоянию 
экземпляра или класса. Для создания статического метода 
используется декоратор staticmethod.

Преимущества использования staticmethod:

* Это подсказка для тех, кто читает код, которая указывает на то, 
  что метод не зависит от состояния экземпляра класса.


---
## staticmethod

```python
import time
from textfsm import clitable
from base_ssh import BaseSSH

class CiscoSSH(BaseSSH):
    def __init__(self, ip, username, password, enable_password,
                 disable_paging=True):
        super().__init__(ip, username, password)
        self._ssh.send('enable\n')
        self._ssh.send(enable_password + '\n')
        if disable_paging:
            self._ssh.send('terminal length 0\n')
        time.sleep(1)
        self._ssh.recv(self._MAX_READ)
        self._mgmt_ip = None

    @staticmethod
    def _parse_show(command, command_output,
                   index_file='index', templates='templates'):
        attributes = {'Command': command,
                      'Vendor': 'cisco_ios'}
        cli_table = clitable.CliTable(index_file, templates)
        cli_table.ParseCmd(command_output, attributes)
        return [dict(zip(cli_table.header, row)) for row in cli_table]

    def send_show_command(self, command, parse=True):
        command_output = super().send_show_command(command)
        if not parse:
            return command_output
        return self._parse_show(command, command_output)
```

---
## staticmethod
## [Пример из scrapli](https://github.com/carlmontanari/scrapli/blob/master/scrapli/channel/base_channel.py#L325)

```python
    @staticmethod
    def _join_and_compile(channel_outputs: Optional[List[bytes]]) -> Pattern[bytes]:
        """
        Convenience method for read_until_prompt_or_time to join channel inputs into a regex pattern
        Args:
            channel_outputs: list of bytes channel inputs to join into a regex pattern
        Returns:
            Pattern: joined regex pattern or an empty pattern (empty bytes)
        Raises:
            N/A
        """
        regex_channel_outputs = b""
        if channel_outputs:
            regex_channel_outputs = b"|".join(
                [b"(" + channel_output + b")" for channel_output in channel_outputs]
            )
        regex_channel_outputs_pattern = re.compile(pattern=regex_channel_outputs, flags=re.I | re.M)

        return regex_channel_outputs_pattern
```

