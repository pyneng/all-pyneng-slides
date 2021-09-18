# Advanced Python для сетевых инженеров

---
### О курсе

* Лекции проводятся в субботу и/или воскресенье
* Онлайн лекции проходят в [FCC](https://freeconferencecall.com)
* После онлайн лекции запись выкладывается на YouTube и Google Drive
* Всё общение на курсе в Slack

---
### Какие темы рассматриваются в курсе

* Тестирование кода с помощью pytest
* Основы аннотации типов
* Автоматическое форматирование кода с Black
* Создание интерфейса CLI с click
* Модуль logging
* Декораторы
* ООП: основы, декораторы методов, наследование, ABC, mixin, descriptor, namedtuple, dataclass
* Генераторы
* Asyncio

---
### Основы pytest

* Основы pytest
* Тестирование кода с помощью pytest
* Основы тестирования оборудования с помощью pytest

---
### Основы pytest

```
$ pytest
============== test session starts ==============
platform linux -- Python 3.8.0, pytest-6.2.1, py-1.9.0, pluggy-0.13.1
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-tasks/exercises/08_deco
rators, configfile: pytest.ini
plugins: metadata-1.10.0, json-report-1.2.4, html-2.1.1, clarity-0.3.0a0
collected 24 items

test_task_8_1.py F.                  [  8%]
test_task_8_2.py ..                  [ 16%]
test_task_8_3.py .....               [ 37%]
test_task_8_4.py ...                 [ 50%]
test_task_8_4a.py ..F                [ 62%]
test_task_8_5.py ...                 [ 75%]
test_task_8_5a.py .FF                [ 87%]
test_task_8_6.py ...                 [100%]
```

--- 
### Аннотация типов

* Синтаксис
* Основы mypy
* Примеры использования аннотации типов

--- 
### Аннотация типов

```python
def check_passwd(
    username: str, password: str, min_length: int = 8, check_username: bool = True,
) -> bool:
    if len(password) < min_length:
        return False
    elif check_username and username in password:
        return False
    else:
        return True
```

--- 
### Аннотация типов

```python
from typing import List, Dict


def send_show(device_dict: Dict[str, str], command: str) -> str:
    with ConnectHandler(**device_dict) as ssh:
        ssh.enable()
        result: str = ssh.send_command(command)
    return result


def send_command_to_devices(
    devices: List[Dict[str, str]], command: str, max_workers: int = 3
) -> Dict[str, str]:
    data = {}
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        result = executor.map(send_show, devices, repeat(command))
        for device, output in zip(devices, result):
            data[device["host"]] = output
    return data
```

---
### Автоматическое форматирование кода с Black

До

```python
from seven_dwwarfs import Grumpy, Happy, Sleepy, Bashful, Sneezy, Dopey, Doc, Section, TestLine
x = { 'a':37,'b':42, 'c':927}

x = 123456789.123456789E123456789

if very_long_variable_name is not None and \
 very_long_variable_name.field > 0 or \
 very_long_variable_name.is_debug:
 z = 'hello '+'world'
else:
 world = 'world'
 a = 'hello {}'.format(world)
 f = rf'hello {world}'
```

После
```python
from seven_dwwarfs import (
    Grumpy,
    Happy,
    Sleepy,
    Bashful,
    Sneezy,
    Dopey,
    Doc,
    Section,
    TestLine,
)

x = {"a": 37, "b": 42, "c": 927}

x = 123456789.123456789e123456789

if (
    very_long_variable_name is not None
    and very_long_variable_name.field > 0
    or very_long_variable_name.is_debug
):
    z = "hello " + "world"
else:
    world = "world"
    a = "hello {}".format(world)
    f = rf"hello {world}"
```

---
### Автоматическое форматирование кода с Black

До
```python
def send_show_command(device_dict: Dict[str, str],
                      command: str, verbose: bool) -> Union[str, None]:
    try:
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            command_output = ssh.send_command(command)
        return command_output



    except (NetMikoAuthenticationException, NetMikoTimeoutException) as error:
        print(error)
        return None
```

После
```python
def send_show_command(
    device_dict: Dict[str, str], command: str, verbose: bool
) -> Union[str, None]:
    try:
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            command_output = ssh.send_command(command)
        return command_output

    except (NetMikoAuthenticationException, NetMikoTimeoutException) as error:
        print(error)
        return None
```

---
### Создание интерфейса CLI с click

* Терминология в CLI интерфейсах
* Основные принципы создания CLI интерфейса с click
* Дополнительные возможности click: запрос информации, вывод в цвете, очистка экрана, индикаторы выполнения


---
### Создание интерфейса CLI с click

```
$ python example_05_pomodoro_timer.py --help
Usage: example_05_pomodoro_timer.py [OPTIONS]

Options:
  -r, --pomodoros_to_run INTEGER  [default: 5]
  -w, --work_minutes INTEGER      [default: 25]
  -s, --short_break INTEGER       [default: 5]
  -l, --long_break INTEGER        [default: 30]
  -p, --set_size INTEGER          [default: 4]
  --help                          Show this message and exit.
```

---
### Создание интерфейса CLI с click

```
$ python example_06_connect_to_devices.py --help
Usage: example_06_connect_to_devices.py [OPTIONS] COMMAND [IP_LIST]...

Options:
  -u, --username TEXT
  -p, --password TEXT
  -s, --secret TEXT
  --help               Show this message and exit.


$ python example_06_connect_to_devices.py "sh clock" 192.168.100.1 192.168.100.2 192.168.100.3
Username: cisco
Password:
Secret:
Connecting to devices  [####################################]  100%
========192.168.100.1=========
*13:06:19.223 UTC Wed Mar 4 2020
========192.168.100.2=========
*13:06:25.326 UTC Wed Mar 4 2020
========192.168.100.3=========
*13:06:31.424 UTC Wed Mar 4 2020
```

---
### Создание интерфейса CLI с click

```python
@click.command()
@click.argument("command")
@click.argument("ip-list", nargs=-1)
@click.option("--username", "-u", envvar="NET_USER", prompt=True)
@click.option("--password", "-p", envvar="NET_PASSWORD", prompt=True, hide_input=True)
@click.option("--secret", "-s", envvar="NET_SECRET", prompt=True, hide_input=True)
def main(command, ip_list, username, password, secret):
    device_params = {
        "device_type": "cisco_ios",
        "username": username,
        "password": password,
        "secret": secret,
    }

    device_list = [{**device_params, "host": ip} for ip in ip_list]
    result_dict = send_command_to_cisco_devices(device_list, command)
    for ip, output in result_dict.items():
        print(ip.center(30, "="))
        print(output)

```

---
### Модуль logging

* Базовая настройка модуля
* Контроль логов сторонних модулей
* Компоненты модуля logging
* Варианты конфигурации модуля
* Rich Handler

---
### Модуль logging

```
$ python netmiko_threads_logging_stdout.py
ThreadPoolExecutor-0_0 root INFO 05:54:36: ===> Connection: 192.168.100.1
ThreadPoolExecutor-0_1 root INFO 05:54:36: ===> Connection: 192.168.100.2
ThreadPoolExecutor-0_1 root INFO 05:54:37: <=== Received:   192.168.100.2
ThreadPoolExecutor-0_0 root INFO 05:54:37: <=== Received:   192.168.100.1
ThreadPoolExecutor-0_1 root INFO 05:54:39: ===> Connection: 192.168.100.3
ThreadPoolExecutor-0_1 root INFO 05:54:40: <=== Received:   192.168.100.3
```

---
### Модуль logging

```
$ python ex06_rich_logging.py
05:55:38 INFO     __main__ - >>> Connecting to 192.168.100.1                ex06_rich_logging.py:38
         INFO     __main__ - >>> Connecting to 192.168.100.2                ex06_rich_logging.py:38
         INFO     __main__ - >>> Connecting to 192.168.100.22               ex06_rich_logging.py:38
         INFO     __main__ - >>> Connecting to 192.168.100.3                ex06_rich_logging.py:38
         INFO     __main__ - >>> Connecting to 192.168.100.1                ex06_rich_logging.py:38
         INFO     __main__ - >>> Connecting to 192.168.100.2                ex06_rich_logging.py:38
05:55:40 INFO     __main__ - <<< Received output from 192.168.100.1         ex06_rich_logging.py:44
         INFO     __main__ - <<< Received output from 192.168.100.2         ex06_rich_logging.py:44
05:55:41 INFO     __main__ - <<< Received output from 192.168.100.3         ex06_rich_logging.py:44
05:55:43 CRITICAL __main__ - Error connecting to 192.168.100.22             ex06_rich_logging.py:48
05:55:44 CRITICAL __main__ - Error connecting to 192.168.100.1              ex06_rich_logging.py:48
05:55:45 CRITICAL __main__ - Error connecting to 192.168.100.2              ex06_rich_logging.py:48
```

---
### Декораторы

* Closure (замыкание)
* Декораторы
* Декораторы с аргументами

---
### Декораторы

```python
def all_args_str(func):
    @wraps(func)
    def wrapper(*args):
        if not all(isinstance(arg, str) for arg in args):
            raise ValueError('Все аргументы должны быть строками')
        return func(*args)
    return wrapper


@all_args_str
def to_upper(*args):
    result = [s.upper() for s in args]
    return result


In [6]: to_upper('a', 'b')
Out[6]: ['A', 'B']

In [7]: to_upper(1, 'b')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
...
ValueError: Все аргументы должны быть строками
```

---
### Объектно-ориентированное программирование

* Обзор основ
* Декораторы методов: classmethod, staticmethod, property
* Наследование
* ABC, Mixin, descriptor
* Namedtuple, dataclass

---
### Модуль collections

* defaultdict
* Counter
* ChainMap
* UserDict, UserList, UserString

---
### Генераторы

* Итераторы
* Генераторы
* Модуль itertools

---
### Генераторы

```python
def filter_lines(filename, regex):
    with open(filename) as f:
        for line in f:
            if re.search(regex, line):
                yield line.rstrip()
```

Пример использования
```python
In [7]: for line in filter_lines('config_r1.txt', '^interface'):
   ...:     print(line)
   ...:
interface Loopback0
interface Tunnel0
interface Ethernet0/0
interface Ethernet0/1
interface Ethernet0/2
interface Ethernet0/3
interface Ethernet0/3.100
interface Ethernet1/0
```

---
### Asyncio

* Asyncio. Основы
* Модули async: asyncssh, scrapli, aiofiles, async-timeout
* Создание классов с async методами
* Использование asyncio: декораторы, генераторы, subprocess, semaphore
* Работа с циклом событий

---
### Asyncio

```python
import asyncio
from scrapli import AsyncScrapli
from scrapli.exceptions import ScrapliException


async def send_show(device, command):
    try:
        async with AsyncScrapli(**device) as conn:
            result = await conn.send_command(command)
            return result.result
    except ScrapliException as error:
        print(error, device["host"])


if __name__ == "__main__":
    output = asyncio.run(send_show(r1, "show ip int br"))
    print(output)
```

---
### Книга/методичка

* [Advanced Python для сетевых инженеров](https://advpyneng.readthedocs.io/ru/latest/contents.html)
* [Основы asyncio для сетевых инженеров](https://asyncpyneng.readthedocs.io/ru/latest/index.html)

---
## Инструменты курса

* Slack - общение, вопросы
* Git, GitHub - хранение заданий, проверка заданий
* apyneng - скрипт для проверки заданий тестами и сдачи заданий на проверку
* IDE/редактор

---

## Git, Github

---

## Проверка заданий тестами

```
$ apyneng
================================== test session starts ==================================
platform linux -- Python 3.8.0, pytest-6.2.1, py-1.9.0, pluggy-0.13.1 -- /home/vagrant/ve
nv/pyneng-py3-8-0/bin/python3.8
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-tasks/exercises/08_deco
rators, configfile: pytest.ini
collected 24 items

test_task_8_1.py::test_func_created FAILED                         [  4%]
test_task_8_1.py::test_timecode PASSED                             [  8%]
test_task_8_2.py::test_func_created PASSED                         [ 12%]
test_task_8_2.py::test_all_args_str PASSED                         [ 16%]
test_task_8_3.py::test_func_created PASSED                         [ 20%]
test_task_8_3.py::test_add_verbose PASSED                          [ 25%]
test_task_8_3.py::test_add_verbose_args PASSED                     [ 29%]
test_task_8_3.py::test_add_verbose_kwargs PASSED                   [ 33%]
test_task_8_3.py::test_add_verbose_args_kwargs PASSED              [ 37%]
test_task_8_4.py::test_func_created PASSED                         [ 41%]
test_task_8_4.py::test_retry_success PASSED                        [ 45%]
test_task_8_4.py::test_retry_failed PASSED                         [ 50%]
test_task_8_4a.py::test_func_created PASSED                        [ 54%]
test_task_8_4a.py::test_retry_success PASSED                       [ 58%]
test_task_8_4a.py::test_retry_failed FAILED                        [ 62%]
test_task_8_5.py::test_func_created PASSED                         [ 66%]
test_task_8_5.py::test_count_calls_basic PASSED                    [ 70%]
test_task_8_5.py::test_count_calls_repeat PASSED                   [ 75%]
test_task_8_5a.py::test_func_created PASSED                        [ 79%]
test_task_8_5a.py::test_attr_total_calls_created FAILED            [ 83%]
test_task_8_5a.py::test_count_calls_basic FAILED                   [ 87%]
test_task_8_6.py::test_func_created PASSED                         [ 91%]
test_task_8_6.py::test_total_order_exception PASSED                [ 95%]
test_task_8_6.py::test_total_order_methods PASSED                  [100%]
```
---

## Бонусные лекции

* [Модуль Rich - создание красивых приложений в CLI](https://www.youtube.com/playlist?list=PLah0HUih_ZRkzS7TouDvcgK79WiYZSgpk)
* [Основы pdb](https://natenka.github.io/pyneng/pdb-basics/)
* [Создание CLI с помощью Typer](https://www.youtube.com/playlist?list=PLah0HUih_ZRnp0HI2TxbQ549Ty-P8soUV)
