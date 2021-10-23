# Декораторы (повторение)

---
## Декоратор

Декоратор в Python это функция, которая используется для изменения
функции, метода или класса.
Декораторы используются для добавления какого-то функционала к функциям/классам.

Синтаксис декоратора - это синтаксический сахар,
эти два определения функций эквивалентны:
```python
def f(...):
    ...
f = verbose(f)

@verbose
def f(...):
    ...
```

---
## functools.wraps

При использовании декораторов, информация исходной функции
заменяется внутренней функцией декоратора:

```python
In [2]: lower
Out[2]: <function __main__.verbose.<locals>.wrapper(*args, **kwargs)>

In [4]: lower?
Signature: lower(*args, **kwargs)
Docstring: <no docstring>
File:      ~/repos/experiments/netdev_try/<ipython-input-1-32089045b87b>
Type:      function
```

---
## functools.wraps

Чтобы исправить это необходимо воспользоваться декоратором wraps
из модуля functools:

```python
from functools import wraps

def verbose(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f'Вызываю функцию {func.__name__}')
        return func(*args, **kwargs)
    return wrapper


@verbose
def lower(string):
    return string.lower()


In [7]: lower
Out[7]: <function __main__.lower(string)>

In [8]: lower?
Signature: lower(string)
Docstring: <no docstring>
File:      ~/repos/experiments/netdev_try/<ipython-input-6-13e6266ce16f>
Type:      function
```

---
## Применение нескольких декораторов

К функции может применяться несколько декораторов. Порядок применения
декораторов будет зависеть от того в каком порядке они записаны.

---
# Декораторы с аргументами

---
### Примеры декораторов с аргументами

pytest
```python
@pytest.fixture(scope='module')
def first_router_from_devices_yaml():
    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)
        r1 = devices[0]
    return r1
```

click
```python
@click.command()
@click.option("--username", "-u", prompt=True)
@click.option("--password", "-p", prompt=True, hide_input=True, confirmation_prompt=True)
def cli(username, password):
    pass
```


---
### Примеры декораторов с аргументами

```python
@main.route('/')
def index():
    pass


@main.route('/labs', methods=['GET', 'POST'])
def labs():
    pass


@main.route('/stats')
def stats():
    pass
```

---
### Примеры декораторов с аргументами

[backoff](https://github.com/litl/backoff)

```python
@backoff.on_exception(
    backoff.expo, requests.exceptions.RequestException
)
def get_url(url):
    return requests.get(url)
```

---
### Декоратор с аргументом

```python
def restrict_args_type(required_type):
    def decorator(func):
        @wraps(func)
        def wrapper(*args):
            if not all(isinstance(arg, required_type) for arg in args):
                raise ValueError(f'Все аргументы должны быть {required_type.__name__}')
            return func(*args)
        return wrapper
    return decorator
```

Применение

```python
@restrict_args_type(str)
def to_upper(*args):
    result = [s.upper() for s in args]
    return result


In [90]: to_upper('a', 2)
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-90-b46c3ca71e5d> in <module>
----> 1 to_upper('a', 2)

<ipython-input-88-ea0c777e0f6e> in wrapper(*args)
      4             def wrapper(*args):
      5                 if not all(isinstance(arg, required_type) for arg in args):
----> 6                     raise ValueError(f'Все аргументы должны быть {required_type.__name__}')
      7                 return func(*args)
      8             return wrapper

ValueError: Все аргументы должны быть str

In [91]: to_upper('a', 'a')
Out[91]: ['A', 'A']
```

---
### Декоратор с аргументом

```python
@restrict_args_type(int)
def to_bin(*args):
    result = [bin(a) for a in args]
    return result


In [94]: to_bin(1,2,3)
Out[94]: ['0b1', '0b10', '0b11']

In [95]: to_bin('a', 'b')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-95-e4007cc06928> in <module>
----> 1 to_bin('a', 'b')

<ipython-input-88-ea0c777e0f6e> in wrapper(*args)
      4             def wrapper(*args):
      5                 if not all(isinstance(arg, required_type) for arg in args):
----> 6                     raise ValueError(f'Все аргументы должны быть {required_type.__name__}')
      7                 return func(*args)
      8             return wrapper

ValueError: Все аргументы должны быть int
```

---
### Декоратор с аргументом

Также при необходимости можно сделать готовые декораторы для определенных
типов данных:

```python
restrict_args_to_str = restrict_args_type(str)

restrict_args_to_int = restrict_args_type(int)

@restrict_args_to_str
def to_upper(*args):
    result = [s.upper() for s in args]
    return result


@restrict_args_to_int
def to_bin(*args):
    result = [bin(a) for a in args]
    return result
```

---
## Встроенные декораторы

* classmethod
* staticmethod
* property
* contextlib: contextmanager
* dataclassess: dataclass

functools:

* cache
* lru_cache
* total_ordering
* singledispatch
* wraps

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
```

---
### classmethod

```python
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

---
### staticmethod

```python
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
### contextlib.contextmanager

```python
from contextlib import contextmanager
from time import time, sleep


@contextmanager
def timecode():
    start = time()
    yield
    execution_time = time() - start
    print(f"Время выполнения: {execution_time:.2f}")


In [9]: with timecode():
   ...:     sleep(3)
   ...:
Время выполнения: 3.00
```

---
### dataclasses.dataclass

```python
@dataclass
class IPAddress:
    ip: str
    mask: int


In [12]: ip1 = IPAddress('10.1.1.1', 28)

In [13]: ip1
Out[13]: IPAddress(ip='10.1.1.1', mask=28)
```

---
### functools.cache

```
4! 1*2*3*4 = 24
5! 1*2*3*4*5 = 120
6! 1*2*3*4*5*6 = 720
```

```python
from functools import cache, lru_cache


@cache
def factorial(n):
    print(f"{n=}")
    return n * factorial(n-1) if n else 1


print(f"{factorial(4)=}")
print(f"{factorial(5)=}")
print(f"{factorial(6)=}")
```

без cache
```
n=4
n=3
n=2
n=1
n=0
factorial(4)=24
n=5
n=4
n=3
n=2
n=1
n=0
factorial(5)=120
n=6
n=5
n=4
n=3
n=2
n=1
n=0
factorial(6)=720
```

с cache:
```
n=4
n=3
n=2
n=1
n=0
factorial(4)=24
n=5
factorial(5)=120
n=6
factorial(6)=720
```

---
### functools lru_cache

```python
from functools import lru_cache


@lru_cache(maxsize=100)
def fib(n):
    print(f"{n=}")
    if n < 2:
        return n
    return fib(n-1) + fib(n-2)


print([fib(n) for n in range(10)])
print([fib(n) for n in range(16)])
```

```
n=0
n=1
n=2
n=3
n=4
n=5
n=6
n=7
n=8
n=9
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
n=10
n=11
n=12
n=13
n=14
n=15
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610]
```

---
### functools lru_cache

```python
@lru_cache(maxsize=1)
def send_show_command(host, username, password, secret, device_type, show_command):
    with ConnectHandler(
        host=host,
        username=username,
        password=password,
        secret=secret,
        device_type=device_type,
    ) as ssh:
        ssh.enable()
        print(f"Вызываю команду {show_command}")
        result = ssh.send_command(show_command)
    return result
```

---
### functools singledispatch

```python
@singledispatch
def send_commands(command, device):
    print("singledispatch")
    raise NotImplementedError("Поддерживается только строка или iterable")


@send_commands.register(str)
def _(command, device):
    print("str")
    with ConnectHandler(**device) as ssh:
        ssh.enable()
        result = ssh.send_command(command)
    return result


@send_commands.register(Iterable)
def _(config_commands, device):
    print("Аргумент iterable")
    with ConnectHandler(**device) as ssh:
        ssh.enable()
        result = ssh.send_config_set(config_commands)
    return result
```

