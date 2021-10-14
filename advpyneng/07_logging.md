# Logging


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
* force - If this keyword  is specified as true, any existing handlers attached
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

---
##

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
### Filter

[LogRecord attributes](https://docs.python.org/3.10/library/logging.html#logrecord-attributes)

```python
class LevelFilter(logging.Filter):
    def __init__(self, level):
        self.level = level

    def filter(self, record):
        return record.levelno == self.level


logfile = logging.FileHandler("logfile3.log")
logfile.setLevel(logging.DEBUG)
logfile.addFilter(LevelFilter(logging.DEBUG))

formatter = logging.Formatter("{asctime} - {name} - {levelname} - {message}", style="{")
logfile.setFormatter(formatter)
```


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
## Альтернативы модулю logging

[loguru](https://github.com/Delgan/loguru)
