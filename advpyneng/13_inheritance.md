## Наследование (Inheritance)

---
## Наследование (Inheritance)

Наследование позволяет создавать новые классы на основе существующих. Различают
дочерний и родительские классы: дочерний класс наследует родительский.
При наследовании, дочерний класс наследует все методы и атрибуты родительского класса.


В Python синтаксис наследования используется с абстрактными классами
для наследования интерфейса/протокола. Кроме того, синтаксис наследования используется с Mixin.

---
## Композиция (Composition)

```python
from jinja2 import Environment, FileSystemLoader

env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('router_template.txt')
```
---
## Unified Modeling Language (UML)

![uml](https://resources.jetbrains.com/help/img/idea/2021.2/py_diagram_editor.png)


---
## Примеры использования наследования


---
## netmiko

```

BaseConnection
    ^
    |
    |
CiscoBaseConnection
    ^
    |
    |
CiscoIosBase <--------+
    ^                 |
    |                 |
    |                 |
CiscoIosSSH     CiscoIosTelnet
```

---
## scrapli all driver classes

```
    +--------------> BaseDriver <--------------+
    |                                          |
 Driver                                    AsyncDriver
    ^                                          ^
    |                                          |
    |     +-----> BaseGenericDriver <-----+    |
    |     |                               |    |
    |     |                               |    |
GenericDriver                         AsyncGenericDriver
    ^                                          |
    |     +-----> BaseNetworkDriver <-----+    |
    |     |                               |    |
    |     |                               |    |
NetworkDriver                         AsyncNetworkDriver
    ^                                          ^
    |                                          |
    |                                          |
IOSXEDriver                            AsyncIOSXEDriver
```

---
## scrapli classes

```
Driver <>-----> Channel <>-----> Transport
```


---
## scrapli sync driver classes

```
BaseDriver
    ^
    |
    |
 Driver
    ^
    |     +--------> BaseGenericDriver
    |     |
    |     |
GenericDriver
    ^
    |     +--------> BaseNetworkDriver
    |     |
    |     |
NetworkDriver
    ^
    |
    |
IOSXEDriver
```

---
## scrapli all driver classes

```
    +--------------> BaseDriver <--------------+
    |                                          |
 Driver                                    AsyncDriver
    ^                                          ^
    |                                          |
    |     +-----> BaseGenericDriver <-----+    |
    |     |                               |    |
    |     |                               |    |
GenericDriver                         AsyncGenericDriver
    ^                                          |
    |     +-----> BaseNetworkDriver <-----+    |
    |     |                               |    |
    |     |                               |    |
NetworkDriver                         AsyncNetworkDriver
    ^                                          ^
    |                                          |
    |                                          |
IOSXEDriver                            AsyncIOSXEDriver
```

---
## scrapli mro

```
                                                            +----> Driver ----> BaseDriver
                                                            |
                                   +----> GenericDriver ----+
                                   |                        |
                                   |                        +----> BaseGenericDriver
IOSXEDriver ----> NetworkDriver ---+
                                   |
                                   +----> BaseNetworkDriver
```

```
In [10]: IOSXEDriver.mro()
Out[10]:
[IOSXEDriver,
 NetworkDriver,
 GenericDriver,
 Driver,
 BaseDriver,
 BaseGenericDriver,
 BaseNetworkDriver,
 object]
```


---
## netmiko

```python
class BaseConnection:

    def establish_connection(self, width: int = 511, height: int = 1000) -> None:
        """Establish SSH connection to the network device
        Timeout will generate a NetmikoTimeoutException
        Authentication failure will generate a NetmikoAuthenticationException
        :param width: Specified width of the VT100 terminal window (default: 511)
        :type width: int
        :param height: Specified height of the VT100 terminal window (default: 1000)
        :type height: int
        """
        self.channel: Channel
        if self.protocol == "telnet":
            self.remote_conn = telnetlib.Telnet(
                self.host, port=self.port, timeout=self.timeout
            )
            # Migrating communication to channel class
            self.channel = TelnetChannel(conn=self.remote_conn, encoding=self.encoding)
            self.telnet_login()
        elif self.protocol == "serial":
            self.remote_conn = serial.Serial(**self.serial_settings)
            self.channel = SerialChannel(conn=self.remote_conn, encoding=self.encoding)
            self.serial_login()
        elif self.protocol == "ssh":
            ssh_connect_params = self._connect_params_dict()
            self.remote_conn_pre: Optional[paramiko.SSHClient]
            self.remote_conn_pre = self._build_ssh_client()
```

---
## netmiko

[netmiko Channel](https://github.com/ktbyers/netmiko/blob/develop/netmiko/channel.py)

```python
class TelnetChannel(Channel):
    def __init__(self, conn: Optional[telnetlib.Telnet], encoding: str) -> None:
        """
        Placeholder __init__ method so that reading and writing can be moved to the
        channel class.
        """
        self.remote_conn = conn
        # FIX: move encoding to GlobalState object?
        self.encoding = encoding

    def write_channel(self, out_data: str) -> None:
        if self.remote_conn is None:
            raise WriteException(
                "Attempt to write data, but there is no active channel."
            )
        self.remote_conn.write(write_bytes(out_data, encoding=self.encoding))

    def read_buffer(self) -> str:
        """Single read of available data."""
        raise NotImplementedError

    def read_channel(self) -> str:
        """Read all of the available data from the channel."""
        if self.remote_conn is None:
            raise ReadException("Attempt to read, but there is no active channel.")
        return self.remote_conn.read_very_eager().decode("utf-8", "ignore")
```

---
## scrapli
### Добавление asynctelnet

https://github.com/carlmontanari/scrapli/blob/master/scrapli/transport/__init__.py

```python
ASYNCIO_TRANSPORTS = (
    "asynctelnet",
    "asyncssh",
)
CORE_TRANSPORTS = ("telnet", "system", "ssh2", "paramiko", "asynctelnet", "asyncssh")
```

[Плюс добавился собственно AsyncTelnetTransport](https://github.com/carlmontanari/scrapli/blob/master/scrapli/transport/plugins/asynctelnet/transport.py)

---
## scrapli
### Добавление asynctelnet

```
    def _transport_factory(self) -> Tuple[Callable[..., Any], object]:
        """
        Determine proper transport class and necessary arguments to initialize that class
        Args:
            N/A
        Returns:
            Tuple[Callable[..., Any], object]: tuple of transport class and dataclass of transport
                class specific arguments
        Raises:
            N/A
        """
        if self.transport_name in CORE_TRANSPORTS:
            transport_class, _plugin_transport_args_class = self._load_core_transport_plugin()
        else:
            transport_class, _plugin_transport_args_class = self._load_non_core_transport_plugin()


    def _load_core_transport_plugin(
        self,
    ) -> Tuple[Any, Type[BasePluginTransportArgs]]:
        """
        Find non-core transport plugins and required plugin arguments
        Args:
            N/A
        Returns:
            Tuple[Any, Type[BasePluginTransportArgs]]: transport class class and TransportArgs \
                dataclass
        Raises:
            ScrapliTransportPluginError: if the transport plugin is unable to be loaded
        """
        self.logger.debug("load core transport requested")

        try:
            transport_plugin_module = importlib.import_module(
                f"scrapli.transport.plugins.{self.transport_name}.transport"
            )
```


---
### Наследование

После наследования всех методов родительского класса, дочерний класс может:

* оставить их без изменения
* полностью переписать их
* дополнить метод
* добавить свои методы

---
###

Несколько вариантов вызова родительского метода, например, все эти варианты
вызовут метод send_show_command родительского класса из дочернего класса CiscoSSH*:

* command_result = BaseSSH.send_show_command(self, command)
* command_result = super(CiscoSSH, self).send_show_command(command)
* command_result = super().send_show_command(command)

```python
class BaseSSH:
    def __init__(self, ip, username, password):
        self.ip = ip
        self.username = username
        self.password = password
        self._MAX_READ = 10000

        client = paramiko.SSHClient()
        client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

        client.connect(
            hostname=ip,
            username=username,
            password=password,
            look_for_keys=False,
            allow_agent=False)

        self._ssh = client.invoke_shell()
        time.sleep(1)
        self._ssh.recv(self._MAX_READ)


class CiscoSSH(BaseSSH):
    def __init__(self, ip, username, password, enable_password=None,
                 disable_paging=True):
        super().__init__(ip, username, password)
        if enable_password
            self._ssh.send('enable\n')
            self._ssh.send(enable_password + '\n')
        if disable_paging:
            self._ssh.send('terminal length 0\n')
        time.sleep(1)
        self._ssh.recv(self._MAX_READ)
```


---
## super

```python
from scrapli.driver.core import IOSXEDriver

class Custom:
    def send_command(self, *args, **kwargs):
        print("AAAAAAAAAAAAAAA")
        return super().send_command(*args, **kwargs)

class MyDriver(Custom, IOSXEDriver):
    pass

In [17]: r1 = MyDriver("192.168.100.1", auth_username="cisco", auth_password="cisco", auth_secondary="cisco")

In [18]: r1.open()
AAAAAAAAAAAAAAA
AAAAAAAAAAAAAAA

In [20]: r = r1.send_command("sh clock")
AAAAAAAAAAAAAAA

In [21]: r.result
Out[21]: '*06:42:32.149 UTC Sat Nov 6 2021'

In [23]: MyDriver.mro()
Out[23]:
[__main__.MyDriver,
 __main__.Custom,
 scrapli.driver.core.cisco_iosxe.sync_driver.IOSXEDriver,
 scrapli.driver.network.sync_driver.NetworkDriver,
 scrapli.driver.generic.sync_driver.GenericDriver,
 scrapli.driver.base.sync_driver.Driver,
 scrapli.driver.base.base_driver.BaseDriver,
 scrapli.driver.generic.base_driver.BaseGenericDriver,
 scrapli.driver.network.base_driver.BaseNetworkDriver,
 object]
```

---
## Использование наследования

---
### Создание своих вариантов классов для модуля

[click custom type ex08_custom_type.py](https://github.com/pyneng/advpyneng-online-3-sep-dec-2021/blob/main/examples/04_click/ex08_custom_type.py)

```python
class IsIPv4(click.ParamType):
    def convert(self, value, param, ctx):
        print("IsIPv4", value)
        try:
            ip = ipaddress.ip_address(value)
            return int(ip)
        except ValueError:
            self.fail(f"Все пропало: {value} не ip адрес")
```

---
## Исключения

---
## Исключения

```
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
      |    +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning
```

---
## Создание исключений

```python
class MyError(Exception):
    """Свое исключение для модуля."""
    pass
```

---
### Перехват исключений

```python
from scrapli import Scrapli
from scrapli.exceptions import ScrapliException, ScrapliCommandFailure
import yaml


def send_show(device, command):
    try:
        with Scrapli(**device) as ssh:
            reply = await ssh.send_command(command)
            output = reply.result
            reply.raise_for_status()
        return output
    except ScrapliCommandFailure:
        raise
    except ScrapliException as error:
        print(error, device["host"])



if __name__ == '__main__':
    with open('devices_scrapli.yaml') as f:
        devices = yaml.safe_load(f)
```


