# Logging

---
## Logging

```
$ python ex08_logging_api_stderr_file_netmiko.py
2023-02-05 14:21:12,990 __main__ DEBUG START
2023-02-05 14:21:12,991 __main__ INFO ===>  Connection: 192.168.100.1
2023-02-05 14:21:12,995 __main__ INFO ===>  Connection: 192.168.100.2
2023-02-05 14:21:13,946 __main__ DEBUG <===  Received:   192.168.100.2
2023-02-05 14:21:13,946 __main__ DEBUG Получен вывод команды sh ip int br с 192.168.100.2
2023-02-05 14:21:15,716 __main__ ERROR Ошибка Authentication to device failed.
2023-02-05 14:21:15,716 __main__ INFO ===>  Connection: 192.168.100.3
2023-02-05 14:21:16,541 __main__ DEBUG <===  Received:   192.168.100.3
2023-02-05 14:21:16,541 __main__ DEBUG Получен вывод команды sh ip int br с 192.168.100.3
```

```
09:29:43 scrapli DEBUG: Scrapli factory initialized
09:29:43 scrapli INFO: Driver '<class 'scrapli.driver.core.cisco_iosxe.sync_driver.IOSXEDriver'>' selected from scrapli core drivers
09:29:43 scrapli.driver DEBUG: load core transport requested
09:29:43 scrapli.driver DEBUG: core transport 'system' loaded successfully
09:29:43 scrapli.driver DEBUG: generating combined network comms prompt pattern
09:29:43 scrapli.driver DEBUG: setting 'comms_prompt_pattern' value to '(^[\w.\-@/:]{1,63}>$)|(^[\w.\-@/:]{1,63}#$)|(^[\w.\-@/:]{1,63}\([\w.\-@/:+]{0,32}\)#$)|(^([\w.\-@/+>:]+\(tcl\)[>#]|\+
>)$)'
09:29:43 scrapli.driver INFO: opening connection to '192.168.100.1' on port '22'
09:29:43 scrapli.transport DEBUG: opening transport connection to '192.168.100.1' on port '22'
09:29:43 scrapli.transport DEBUG: created transport 'open_cmd': '['ssh', '192.168.100.1', '-p', '22', '-o', 'ConnectTimeout=5', '-o', 'ServerAliveInterval=10', '-l', 'cisco', '-o', 'StrictH
ostKeyChecking=no', '-o', 'UserKnownHostsFile=/dev/null', '-F', '/dev/null']'
09:29:43 scrapli.transport DEBUG: transport connection to '192.168.100.1' on port '22' opened successfully
09:29:43 scrapli.channel DEBUG: attempting in channel ssh authentication
09:29:43 scrapli.channel DEBUG: read: b"Warning: Permanently added '192.168.100.1' (RSA) to the list of known hosts.\n"
09:29:47 scrapli.channel DEBUG: read: b'Password: '
09:29:47 scrapli.channel DEBUG: write: REDACTED
09:29:47 scrapli.channel DEBUG: write: '\n'
09:29:47 scrapli.channel DEBUG: read: b'\n\nR1>'
09:29:47 scrapli.channel DEBUG: write: '\n'
09:29:47 scrapli.channel DEBUG: read: b'\n'
09:29:47 scrapli.channel DEBUG: read: b'R1>'
09:29:47 scrapli.driver INFO: attempting to acquire 'privilege_exec' privilege level
09:29:47 scrapli.driver DEBUG: determined current privilege level is one of '['exec']'
09:29:47 scrapli.driver DEBUG: determined privilege escalation necessary
09:29:47 scrapli.channel INFO: sending interactive input: enable; expecting: ^(?:enable\s){0,1}password:\s?$; hidden_input: False
09:29:47 scrapli.channel DEBUG: write: 'enable'
09:29:47 scrapli.channel DEBUG: read: b'e'
```

---
## logging.basicConfig

```python
logging.basicConfig(
    format='{threadName} {name} {levelname}: {message}',
    style="{",
    level=logging.INFO
)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

```python
logging.basicConfig(
    filename='mylog.log',
    format='{threadName} {name} {levelname}: {message}',
    style="{",
    level=logging.DEBUG,
)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

---
## Запись в файл и вывод на stderr

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

formatter = logging.Formatter(
    '{asctime} {name} {levelname} {message}', datefmt='%H:%M:%S', style='{'
)

### stderr
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
console.setFormatter(formatter)

logger.addHandler(console)

### File
logfile = logging.FileHandler('logfile3.log')
logfile.setLevel(logging.WARNING)
logfile.setFormatter(formatter)

logger.addHandler(logfile)

## messages
logger.debug('Сообщение уровня debug')
logger.info('Сообщение уровня info')
logger.warning('Сообщение уровня warning')
```
---
## Logging в модуле

```python
import logging


log = logging.getLogger(__name__)
log.addHandler(logging.NullHandler())


def send_show(device_dict, command):
    ip = device_dict["host"]
    log.info(f"===>  Connection: {ip}")
```

---
### Настройка logging через словарь

```yaml
version: 1
formatters:
  simple:
    format: '{asctime} {name} {levelname} {message}'
handlers:
  console:
    class: logging.StreamHandler
    level: DEBUG
    formatter: simple
    stream: ext://sys.stdout
loggers:
  simpleExample:
    level: DEBUG
    handlers: [console]
    propagate: no
root:
  level: DEBUG
  handlers: [console]
```

---
## logging

```python
logging.basicConfig(
    format='{threadName} {name} {levelname}: {message}',
    datefmt='%H:%M:%S',
    style="{",
    level=logging.INFO
)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

formatter = logging.Formatter(
    '{threadName} {name} {levelname}: {message}', datefmt='%H:%M:%S', style='{'
)

### stderr
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
console.setFormatter(formatter)

logger.addHandler(console)

logger.debug('Сообщение уровня debug')
logger.info('Сообщение уровня info')
logger.warning('Сообщение уровня warning')
```


---
## logging

```python
logging.basicConfig(
    format='{threadName} {name} {levelname}: {message}',
    datefmt='%H:%M:%S',
    style="{",
    level=logging.INFO
)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

---
## logging


```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

formatter = logging.Formatter(
    '{threadName} {name} {levelname}: {message}', datefmt='%H:%M:%S', style='{'
)

### stderr
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
console.setFormatter(formatter)

logger.addHandler(console)

logger.debug('Сообщение уровня debug')
logger.info('Сообщение уровня info')
logger.warning('Сообщение уровня warning')
```


---
## Уровни

| Уровень      |    | Когда используется                          |
|--------------|----|------------------------------------------|
| ``CRITICAL`` | 50 | Серьезная ошибка из-за которой программа не может подолжить работу |
| ``ERROR``    | 40 | Возникла ошибка и из-за нее не получилось выполнить часть задач. |
| ``WARNING``  | 30 | Случилось что-то неожиданное, но программа все еще работает. |
|              |    | Также может использоваться для индикации о будущих проблемах. |
| ``INFO``     | 20 | Подтверждение, что все работает как должно. |
| ``DEBUG``    | 10 | Подробная информация для диагностики проблемы. |

---
## [LogRecord attributes](https://docs.python.org/3/library/logging.html#logrecord-attributes)

| Attribute name  | Description                                   |
|-----------------|-----------------------------------------------|
| asctime         | Human-readable time when the                  |
|                 | `LogRecord` was created.  By default   |
|                 | this is of the form '2003-07-08 16:49:45,896' |
| created         | Time when the `LogRecord` was created  |
| filename        | Filename portion of ``pathname``.             |
| funcName        | Name of function containing the logging call. |
| levelname       | Text logging level for the message            |
| levelno         | Numeric logging level for the message         |
| lineno          | Source line number where the logging call was |
|                 | issued (if available).                        |
| message         | The logged message, computed as ``msg % args``  |
|                 | This is set when `Formatter.format` is invoked. |
| module          | Module (name portion of ``filename``).        |
| msecs           | Millisecond portion of the time when the      |
|                 | `LogRecord` was created.               |
| name            | Name of the logger used to log the call.      |
| pathname        | Full pathname of the source file where the    |
|                 | logging call was issued (if available).       |
| process         | Process ID (if available).                    |
| processName     | Process name (if available).                  |
| relativeCreated | Time in milliseconds when the LogRecord was   |
|                 | created, relative to the time the logging     |
|                 | module was loaded.                            |
| thread          | Thread ID (if available).                     |
| threadName      | Thread name (if available).                   |

---
## logging.basicConfig

---
## logging.basicConfig

```python
logging.basicConfig(
    format='{threadName} {name} {levelname}: {message}',
    style="{",
    level=logging.INFO
)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

---
## logging.basicConfig

```python
logging.basicConfig(
    filename='mylog.log',
    format='{threadName} {name} {levelname}: {message}',
    style="{",
    level=logging.DEBUG,
)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

---
## logging.basicConfig

Вызов basicConfig должен происходить перед любыми вызовами logging.debug, logging.info и т.д.

```python
import logging
import yaml
from netmiko import ConnectHandler, NetMikoAuthenticationException


logging.getLogger('paramiko').setLevel(logging.WARNING)
logging.getLogger('netmiko').setLevel(logging.WARNING)

logging.basicConfig(
    format='{threadName} {name} {levelname}: {message}',
    style="{",
    level=logging.INFO
)
```

---
## [Параметры logging.basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig)

* filename  Specifies that a FileHandler be created, using the specified filename, rather than a StreamHandler.
* filemode  Specifies the mode to open the file, if filename is specified (if filemode is unspecified, it defaults to 'a').
* format
* datefmt date/time format
* style ``%``, ``{``, ``$``
* level
* stream stream to initialize the StreamHandler. Incompatible with filename if both are present, a ValueError is raised.
* handlers Incompatible with filename or stream
* force If this keyword is specified as true, any existing handlers attached
  to the root logger are removed and closed, before carrying out the configuration
  as specified by the other arguments.

---
## [Rich logging Handler](https://rich.readthedocs.io/en/latest/reference/logging.html#rich.logging.RichHandler)

```python
import logging
from rich.logging import RichHandler


logging.basicConfig(
    level=logging.DEBUG,
    format="{message}",
    style="{",
    datefmt="%X",
    handlers=[RichHandler(), lfile],
)

log = logging.getLogger("rich")
log.info("Hello, World!")
```


---
## Уровни

| Уровень      |    | Когда используется                          |
|--------------|----|------------------------------------------|
| ``CRITICAL`` | 50 | Серьезная ошибка из-за которой программа не может подолжить работу |
| ``ERROR``    | 40 | Возникла ошибка и из-за нее не получилось выполнить часть задач. |
| ``WARNING``  | 30 | Случилось что-то неожиданное, но программа все еще работает. |
|              |    | Также может использоваться для индикации о будущих проблемах. |
| ``INFO``     | 20 | Подтверждение, что все работает как должно. |
| ``DEBUG``    | 10 | Подробная информация для диагностики проблемы. |


---
## Компоненты модуля logging


* Logger это основной интерфейс для работы с модулем
* Handler отправляет log-сообщения конкретному получателю
* Filter позволяет фильтровать сообщения
* Formatter указывает формат сообщения

Log события передаются между logger, handlers, filters и  formatter в виде экземпляра LogRecord.

```python
import logging

log = logging.getLogger(__name__)
log.setLevel(logging.DEBUG)

fmt = logging.Formatter(
    '{asctime} {name} {levelname} {message}',
    style='{',
    datefmt='%H:%M:%S',
)

console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
console.setFormatter(fmt)

log.addHandler(console)

# messages
log.debug('Сообщение уровня debug %s', 'SOS')
log.info('Сообщение уровня info')
log.warning('Сообщение уровня warning')
```

---
## Компоненты модуля logging

```python
import logging

log = logging.getLogger('My Script')
log.setLevel(logging.DEBUG)

console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
formatter = logging.Formatter(
    '{asctime} {name} {levelname} {message}',
    style='{',
    datefmt='%H:%M:%S',
)
console.setFormatter(formatter)

log.addHandler(console)

## messages
log.debug('Сообщение уровня debug %s', 'SOS')
log.info('Сообщение уровня info')
log.warning('Сообщение уровня warning')
```

---
## Запись в файл и вывод на stderr

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

fmt = logging.Formatter(
    '{asctime} {name} {levelname} {message}',
    style='{',
)

### stderr
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
console.setFormatter(fmt)

logger.addHandler(console)

### File
logfile = logging.FileHandler('logfile3.log')
logfile.setLevel(logging.WARNING)
logfile.setFormatter(fmt)

logger.addHandler(logfile)

## messages
logger.debug('Сообщение уровня debug')
logger.info('Сообщение уровня info')
logger.warning('Сообщение уровня warning')
```


---
## Компоненты модуля logging

[Logger](https://docs.python.org/3.10/library/logging.html#logging.Logger) это основной интерфейс для работы с модулем.

Rich inspect

```python
 <Logger __main__ (WARNING)>
          disabled = False
           filters = []
          handlers = []
             level = 0
           manager = <logging.Manager object at 0xb7155b80>
              name = '__main__'
            parent = <RootLogger root (WARNING)>
         propagate = True
              root = <RootLogger root (WARNING)>
         addFilter = def addFilter(filter): Add the specified filter to this handler.
        addHandler = def addHandler(hdlr): Add the specified handler to this logger.
          setLevel = def setLevel(level):
                     Set the logging level of this logger.  level must be an int or a str.
          critical = def critical(msg, *args, **kwargs):
                     Log 'msg % args' with severity 'CRITICAL'.
             debug = def debug(msg, *args, **kwargs): Log 'msg % args' with severity 'DEBUG'.
             error = def error(msg, *args, **kwargs): Log 'msg % args' with severity 'ERROR'.
         exception = def exception(msg, *args, exc_info=True, **kwargs):
                     Convenience method for logging an ERROR with exception information.
             fatal = def fatal(msg, *args, **kwargs):
                     Log 'msg % args' with severity 'CRITICAL'.
              info = def info(msg, *args, **kwargs): Log 'msg % args' with severity 'INFO'.
               log = def log(level, msg, *args, **kwargs):
                     Log 'msg % args' with the integer severity 'level'.
```

---
## Компоненты модуля logging

[Handler](https://docs.python.org/3.10/library/logging.html#logging.Handler) отправляет log-сообщения конкретному получателю

```python
<StreamHandler <stderr> (NOTSET)>

     filters = []
   formatter = None
       level = 0
        lock = <unlocked _thread.RLock object owner=0 count=0 at 0xb71bb2d8>
        name = None
      stream = <_io.TextIOWrapper name='<stderr>' mode='w' encoding='utf-8'>
   addFilter = def addFilter(filter): Add the specified filter to this handler.
setFormatter = def setFormatter(fmt): Set the formatter for this handler.
    setLevel = def setLevel(level): Set the logging level of this handler.  level must be an int or a str.
```

[Useful Handlers](https://docs.python.org/3.10/howto/logging.html#useful-handlers)

---
## Компоненты модуля logging

Formatter указывает формат сообщения

```python
logging.Formatter.__init__(fmt=None, datefmt=None, style='%')
```

datefmt по умолчанию
```
%Y-%m-%d %H:%M:%S
```

---
## Компоненты модуля logging

* Filter позволяет фильтровать сообщения

---
## Запись в файл и вывод на stderr

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

### stderr
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
formatter = logging.Formatter('{asctime} {name} {levelname} {message}',
                              datefmt='%H:%M:%S', style='{')
console.setFormatter(formatter)

logger.addHandler(console)

### File
logfile = logging.FileHandler('logfile3.log')
logfile.setLevel(logging.WARNING)
formatter = logging.Formatter('{asctime} {name} {levelname} {message}',
                              datefmt='%H:%M:%S', style='{')
logfile.setFormatter(formatter)

logger.addHandler(logfile)

## messages
logger.debug('Сообщение уровня debug')
logger.info('Сообщение уровня info')
logger.warning('Сообщение уровня warning')
```

---
## [Rich Handler](https://github.com/pyneng/pyneng-bonus-lectures/blob/master/examples/10_rich/ex06_rich_logging.py)

```python
from concurrent.futures import ThreadPoolExecutor
from pprint import pprint
from itertools import repeat
import logging

import yaml
from scrapli import Scrapli
from scrapli.exceptions import ScrapliException
from rich.logging import RichHandler


logging.getLogger("scrapli").setLevel(logging.WARNING)
log = logging.getLogger(__name__)
log.setLevel(logging.DEBUG)

### stderr
console = RichHandler(level=logging.DEBUG)
formatter = logging.Formatter(
    "{name} {message}", datefmt="%X", style="{"
)
console.setFormatter(formatter)
log.addHandler(console)

### File
logfile = logging.FileHandler("logfile3.log")
logfile.setLevel(logging.DEBUG)
formatter = logging.Formatter("{asctime} {name} {levelname} {message}", style="{")
logfile.setFormatter(formatter)

log.addHandler(logfile)
```

---
### Logger Filter

[LogRecord attributes](https://docs.python.org/3.10/library/logging.html#logrecord-attributes)

```python
class LevelFilter(logging.Filter):
    def __init__(self, level):
        self.level = level

    def filter(self, record):
        return record.levelno == self.level


log = logging.getLogger(__name__)
log.setLevel(logging.DEBUG)
log.addFilter(LevelFilter(logging.DEBUG))

logfile = logging.FileHandler("logfile3.log")
logfile.setLevel(logging.DEBUG)
formatter = logging.Formatter("{asctime} {name} {levelname} {message}", style="{")
logfile.setFormatter(formatter)

log.addHandler(logfile)
```

---
### Handler Filter

[LogRecord attributes](https://docs.python.org/3.10/library/logging.html#logrecord-attributes)

```python
class LevelFilter(logging.Filter):
    def __init__(self, level):
        self.level = level

    def filter(self, record):
        return record.levelno == self.level


log = logging.getLogger(__name__)
log.setLevel(logging.DEBUG)

logfile = logging.FileHandler("logfile3.log")
logfile.setLevel(logging.DEBUG)
logfile.addFilter(LevelFilter(logging.DEBUG))
logfile.addFilter(MessageFilter("test"))

formatter = logging.Formatter("{asctime} {name} {levelname} {message}", style="{")
logfile.setFormatter(formatter)

log.addHandler(logfile)
```

---
### Filter to add info

[Using Filters to impart contextual information](https://docs.python.org/3/howto/logging-cookbook.html#using-filters-to-impart-contextual-information)


---
## NullHandler

```python
import logging


log = logging.getLogger(__name__)
log.addHandler(logging.NullHandler())


def send_show(device_dict, command):
    ip = device_dict["host"]
    log.info(f"===>  Connection: {ip}")

    try:
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            log.debug(f"<===  Received:   {ip}")
            log.debug(f"Получен вывод команды {command}\n\n{result}")
        return result
    except SSHException as error:
        #log.exception(f"Ошибка {error} на {ip}")
        log.error(f"Ошибка {error} на {ip}")
```


---
### Варианты настройки logging

* Создание loggers, handlers, formatters в коде
* Создание конфиг файла  и чтение файла ``fileConfig()``
* Запись настроек в словаре и чтение настроек  из словаря ``dictConfig()``

---
### Настройка logging через словарь

```yaml
version: 1
formatters:
  simple:
    format: '{asctime} {name} {levelname} {message}'
handlers:
  console:
    class: logging.StreamHandler
    level: DEBUG
    formatter: simple
    stream: ext://sys.stdout
loggers:
  simpleExample:
    level: DEBUG
    handlers: [console]
    propagate: no
root:
  level: DEBUG
  handlers: [console]
```

---
## Альтернативы модулю logging

[loguru](https://github.com/Delgan/loguru)

---
### Модуль python-json-logger

[python-json-logger](https://github.com/madzak/python-json-logger)
