## Python package

---
## Python package

* Import Package
* Distribution Package

---
### Import Package

Модуль Python, который может содержать другие модули или рекурсивно другие пакеты.

Import package чаще всего называют одним словом package, но в этом руководстве
будет использоваться расширенный термин, когда требуется больше ясности, чтобы
избежать путаницы с "Distribution Package", который также обычно называют
Package.

### Distribution Package

Версионный архивный файл, содержащий пакеты Python, модули и другие файлы
ресурсов, которые используются для распространения пакета. Архивный файл — это
то, что конечный пользователь загрузит из Интернета и установит.

---
### Развитие кода

```python
import re
import logging
from concurrent.futures import ThreadPoolExecutor
from itertools import repeat
from pprint import pprint

import yaml
from scrapli import Scrapli
from scrapli.exceptions import ScrapliException
from rich.console import Console
from rich.table import Table
from rich import print as rprint
from rich.logging import RichHandler


logging.getLogger("scrapli").setLevel(logging.WARNING)
logging.getLogger("paramiko").setLevel(logging.CRITICAL)

logging.basicConfig(
    format="%(name)s: %(message)s",
    level=logging.INFO,
    datefmt="[%X]",
    handlers=[RichHandler()],
)


def get_show_output(params, command):


def get_show_from_devices_threads(devices, command, max_threads=10):


def parse_device_all_ports_info(output):


def collect_ports_info(devices, command="sh ip int br"):



if __name__ == "__main__":
    with open("devices.yaml") as f:
        devices = yaml.safe_load(f)
    port_info = collect_ports_info(devices)
    draw_pretty_tables_for_port_info(port_info, stats=True)
```

---
### Развитие кода

collect_ports_status.py
```python
def get_show_output(params, command):

def get_show_from_devices_threads(devices, command, max_threads=10):

def parse_device_all_ports_info(output):

def collect_ports_info(devices, command="sh ip int br"):


if __name__ == "__main__":
    with open("devices.yaml") as f:
        devices = yaml.safe_load(f)
    port_info = collect_ports_info(devices)
    draw_pretty_tables_for_port_info(port_info, stats=True)
```

write_rib_to_db.py
```python
def get_show_output(params, command):

def get_show_from_devices_threads(devices, command, max_threads=10):

def parse_rib(devices):

def write_rib_to_db(rib_info):


if __name__ == "__main__":
    with open("devices.yaml") as f:
        devices = yaml.safe_load(f)
    rib_info = parse_rib(devices)
    write_rib_to_db(rib_info)
```

---
### Развитие кода

my_common_funcs.py
```python
def get_show_output(params, command):

def get_show_from_devices_threads(devices, command, max_threads=10):
```

collect_ports_status.py
```python
def parse_device_all_ports_info(output):

def collect_ports_info(devices, command="sh ip int br"):


if __name__ == "__main__":
    with open("devices.yaml") as f:
        devices = yaml.safe_load(f)
    port_info = collect_ports_info(devices)
    draw_pretty_tables_for_port_info(port_info, stats=True)
```

write_rib_to_db.py
```python
def parse_rib(devices):

def write_rib_to_db(rib_info):


if __name__ == "__main__":
    with open("devices.yaml") as f:
        devices = yaml.safe_load(f)
    rib_info = parse_rib(devices)
    write_rib_to_db(rib_info)
```

---
## Import Package

```
project1_basics/
├── connect.py
├── exceptions.py
├── __init__.py
└── utils.py
```

---
## Import Package

```
project1_basics/
├── connect.py
├── exceptions.py
├── __init__.py
└── utils.py
```

connect.py
```python
print("Import connect.py")

def connect_ssh(ip):
    print("Connect SSH to {}".format(ip))

def connect_telnet(ip):
    print("Connect Telnet to {}".format(ip))
```

exceptions.py
```python
class MyException(Exception):
    pass
```

utils.py
```python
def some_func():
    pass
```

__init__.py
```python
from project1_basics.connect import *
```


---
## Import Package

```
project2_package/
├── configs
│   ├── cisco.py
│   └── __init__.py
├── connect.py
├── __init__.py
└── parse
    ├── cisco.py
    ├── __init__.py
    └── juniper.py
```



---
## Import Package

```
project3_quiz_test/
├── AUTHORS.rst
├── CONTRIBUTING.rst
├── docs
│   ├── authors.rst
│   ├── conf.py
│   ├── contributing.rst
│   ├── history.rst
│   ├── index.rst
│   ├── installation.rst
│   ├── make.bat
│   ├── Makefile
│   ├── readme.rst
│   └── usage.rst
├── HISTORY.rst
├── LICENSE
├── Makefile
├── MANIFEST.in
├── pyneng_quiz_test
│   ├── cli.py
│   ├── __init__.py
│   └── pyneng_quiz_test.py
├── README.rst
├── requirements_dev.txt
├── setup.cfg
├── setup.py
├── tests
│   ├── __init__.py
│   └── test_pyneng_quiz_test.py
└── tox.ini
```



---
## Import Package

```
scrapli/
├── codecov.yaml
├── docs
├── examples
├── LICENSE
├── Makefile
├── MANIFEST.in
├── mkdocs.yml
├── noxfile.py
├── pyproject.toml
├── README.md
├── requirements-asyncssh.txt
├── requirements-community.txt
├── requirements-dev.txt
├── requirements-docs.txt
├── requirements-genie.txt
├── requirements-paramiko.txt
├── requirements-ssh2.txt
├── requirements-textfsm.txt
├── requirements-ttp.txt
├── requirements.txt
├── scrapli
├── scrapli.svg
├── setup.cfg
└── tests
```



---
## Import Package

```
scrapli/
├── codecov.yaml
├── docs
├── examples
├── LICENSE
├── Makefile
├── MANIFEST.in
├── mkdocs.yml
├── noxfile.py
├── pyproject.toml
├── README.md
├── requirements.txt
├── scrapli
│   ├── channel
│   ├── decorators.py
│   ├── driver
│   ├── exceptions.py
│   ├── factory.py
│   ├── helper.py
│   ├── __init__.py
│   ├── logging.py
│   ├── py.typed
│   ├── response.py
│   ├── settings.py
│   ├── ssh_config.py
│   └── transport
```

---
## Import Package

```
scrapli/
├── scrapli
│   ├── channel
│   │   ├── async_channel.py
│   │   ├── base_channel.py
│   │   ├── __init__.py
│   │   └── sync_channel.py
│   ├── decorators.py
│   ├── driver
│   │   ├── base
│   │   │   ├── async_driver.py
│   │   │   ├── base_driver.py
│   │   │   ├── __init__.py
│   │   │   └── sync_driver.py
│   │   ├── core
│   │   │   ├── arista_eos
│   │   │   ├── cisco_iosxe
│   │   │   ├── cisco_iosxr
│   │   │   ├── cisco_nxos
│   │   │   ├── __init__.py
│   │   │   └── juniper_junos
│   │   ├── generic
│   │   │   ├── async_driver.py
│   │   │   ├── base_driver.py
│   │   │   ├── __init__.py
│   │   │   └── sync_driver.py
│   │   ├── __init__.py
│   │   └── network
│   │       ├── async_driver.py
│   │       ├── base_driver.py
│   │       ├── __init__.py
│   │       └── sync_driver.py
│   ├── exceptions.py
│   ├── factory.py
│   ├── helper.py
│   ├── __init__.py
│   ├── logging.py

```
