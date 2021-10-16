# Logging

---
## Logging

```
$ python ex08_logging_api_stderr_file_netmiko.py
2021-10-15 14:21:12,990 - __main__ - DEBUG - START
2021-10-15 14:21:12,991 - __main__ - INFO - ===>  Connection: 192.168.100.1
2021-10-15 14:21:12,995 - __main__ - INFO - ===>  Connection: 192.168.100.2
2021-10-15 14:21:13,946 - __main__ - DEBUG - <===  Received:   192.168.100.2
2021-10-15 14:21:13,946 - __main__ - DEBUG - Получен вывод команды sh ip int br с 192.168.100.2
2021-10-15 14:21:15,716 - __main__ - ERROR - Ошибка Authentication to device failed.

Common causes of this problem are:
1. Invalid username and password
2. Incorrect SSH-key file
3. Connecting to the wrong device

Device settings: cisco_ios 192.168.100.1:22


Authentication failed. на 192.168.100.1
2021-10-15 14:21:15,716 - __main__ - INFO - ===>  Connection: 192.168.100.3
2021-10-15 14:21:16,541 - __main__ - DEBUG - <===  Received:   192.168.100.3
2021-10-15 14:21:16,541 - __main__ - DEBUG - Получен вывод команды sh ip int br с 192.168.100.3
```

---
## Уровни

| Уровень      | Когда используется                          |
|--------------|---------------------------------------------|
| ``DEBUG``    | Подробная информация для диагностики проблемы. |
| ``INFO``     | Подтверждение, что все работает как должно. |
| ``WARNING``  | Случилось что-то неожиданное, но программа все еще работает. |
|              | Также может использоваться для индикации о будущих проблемах. |
| ``ERROR``    | Возникла ошибка и из-за нее не получилось выполнить часть задач. |
| ``CRITICAL`` | Серьезная ошибка из-за которой программа не может подолжить работу |

---
## logging.basicConfig

```python
import logging

logging.basicConfig(filename='mylog.log', level=logging.DEBUG)

logging.debug('Сообщение уровня debug')
logging.info('Сообщение уровня info')
logging.warning('Сообщение уровня warning')
```

---
## logging.basicConfig

Вызов basicConfig должен происходить перед любыми вызовами logging.debug, logging.info и т.д.

basicConfig задуман как одноразовое простое средство настройки, только первый вызов 
что-то сделает: последующие вызовы фактически не выполняются.

---
## logging.basicConfig

```python
import logging
import yaml
from netmiko import ConnectHandler, NetMikoAuthenticationException


logging.getLogger('paramiko').setLevel(logging.WARNING)
logging.getLogger('netmiko').setLevel(logging.WARNING)

logging.basicConfig(
    format='%(threadName)s %(name)s %(levelname)s: %(message)s',
    level=logging.INFO
)
```

---
## [Параметры logging.basicConfig](https://docs.python.org/3.9/library/logging.html#logging.basicConfig)

* filename  Specifies that a FileHandler be created, using the specified filename, rather than a StreamHandler.
* filemode  Specifies the mode to open the file, if filename is specified (if filemode is unspecified, it defaults to 'a').
* format
* datefmt - date/time format
* style - ``%``, ``{``, ``$``
* level
* stream - stream to initialize the StreamHandler. Incompatible with filename - if both are present, a ValueError is raised.
* handlers - Incompatible with filename or stream
* force - If this keyword is specified as true, any existing handlers attached
  to the root logger are removed and closed, before carrying out the configuration
  as specified by the other arguments.

---
## [Rich logging Handler](https://rich.readthedocs.io/en/latest/reference/logging.html#rich.logging.RichHandler)

```python
import logging
from rich.logging import RichHandler

FORMAT = "%(message)s"
logging.basicConfig(
    level="NOTSET", format=FORMAT, datefmt="[%X]", handlers=[RichHandler()]
)

log = logging.getLogger("rich")
log.info("Hello, World!")
```


---
## Компоненты модуля logging


* Logger - это основной интерфейс для работы с модулем
* Handler - отправляет log-сообщения конкретному получателю
* Filter - позволяет фильтровать сообщения
* Formatter - указывает формат сообщения

Log события передаются между logger, handlers, filters и  formatter в виде экземпляра LogRecord.

---
## Компоненты модуля logging

```python
import logging

log = logging.getLogger('My Script')
log.setLevel(logging.DEBUG)

console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s',
                              datefmt='%H:%M:%S')
console.setFormatter(formatter)

log.addHandler(console)

## messages
log.debug('Сообщение уровня debug %s', 'SOS')
log.info('Сообщение уровня info')
log.warning('Сообщение уровня warning')
```

---
## Компоненты модуля logging

[Logger](https://docs.python.org/3.10/library/logging.html#logging.Logger) - это основной интерфейс для работы с модулем.

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

[Handler](https://docs.python.org/3.10/library/logging.html#logging.Handler) - отправляет log-сообщения конкретному получателю

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

Formatter - указывает формат сообщения

```python
logging.Formatter.__init__(fmt=None, datefmt=None, style='%')
```

datefmt по умолчанию
```
%Y-%m-%d %H:%M:%S
```

---
## Компоненты модуля logging

* Filter - позволяет фильтровать сообщения

---
## Запись в файл и вывод на stderr

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

### stderr
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
formatter = logging.Formatter('{asctime} - {name} - {levelname} - {message}',
                              datefmt='%H:%M:%S', style='{')
console.setFormatter(formatter)

logger.addHandler(console)

### File
logfile = logging.FileHandler('logfile3.log')
logfile.setLevel(logging.WARNING)
formatter = logging.Formatter('{asctime} - {name} - {levelname} - {message}',
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
    "{name} - {message}", datefmt="%X", style="{"
)
console.setFormatter(formatter)
log.addHandler(console)

### File
logfile = logging.FileHandler("logfile3.log")
logfile.setLevel(logging.DEBUG)
formatter = logging.Formatter("{asctime} - {name} - {levelname} - {message}", style="{")
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
formatter = logging.Formatter("{asctime} - {name} - {levelname} - {message}", style="{")
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

formatter = logging.Formatter("{asctime} - {name} - {levelname} - {message}", style="{")
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
    format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
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
