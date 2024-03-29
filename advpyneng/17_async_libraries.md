## Асинхронные модули

---
## asyncssh

---
## asyncssh


Модуль asynssh это реализация SSHv2 клиента и сервера. Модуль написан
с использованием asyncio и требует Python 3.6+.

---
## asyncssh

```python
async def send_show(host, username, password, enable_password, command):
    ssh = await asyncssh.connect(
        host=host,
        username=username,
        password=password,
    )

    writer, reader, stderr = await ssh.open_session(term_type="Dumb")
    await reader.readuntil(">")
    writer.write("enable\n")
    await reader.readuntil("Password")
    writer.write(f"{enable_password}\n")
    await reader.readuntil([">", "#"])
    writer.write("terminal length 0\n")
    await reader.readuntil("#")

    writer.write(f"{command}\n")
    output = await reader.readuntil("#")
    ssh.close()
    return output


if __name__ == "__main__":
    r1 = {
        'host': '192.168.100.1',
        'username': 'cisco',
        'password': 'cisco',
        'enable_password': 'cisco',
    }
    result = asyncio.run(send_show(**r1, command="sh ip int br"))
    print(result)
```

## Модуль scrapli
---

### Модуль scrapli

Scrapli это модуль, который
позволяет подключаться к сетевому оборудованию используя Telnet, SSH или NETCONF.

Также как и netmiko, scrapli может использовать paramiko или telnetlib
(и другие модули) для самого подключения, но при этом предоставляет одинаковый
интерфейс работы для разных типов подключения и разного оборудования.

---

### Основные составляющие части scrapli

* transport - это конкретный способ подключения к оборудованию
* channel - следующий уровень над транспортом, который отвечает за отправку команд,
  получение вывода и другими взаимодействиями с оборудованием
* driver - это интерфейс, который предоставляется пользователю для работы со scrapli.
  Тут есть как специфические драйверы типа ``IOSXEDriver``, который понимает
  как взаимодействовать с оборудованием конкретного типа, так и базовый
  драйвер Driver, который предоставляет минимальный интерфейс для работы через SSH/Telnet.


---

### Доступные варианты транспорта

* system - используется встроенный SSH клиент, подразумевается использование клиента на Linux/MacOS
* paramiko - модуль paramiko
* ssh2 - используется модуль ssh2-python
* telnet - будет использоваться telnetlib
* asyncssh - модуль asyncssh
* asynctelnet - telnet клиент написанный с использованием asyncio

---
### Поддерживаемые платформы

* Cisco IOS-XE
* Cisco NX-OS
* Juniper JunOS
* Cisco IOS-XR
* Arista EOS

Кроме этих платформ, есть также [платформы](https://scrapli.github.io/scrapli_community/user_guide/project_details/)
[scrapli community](https://github.com/scrapli/scrapli_community).

---

### Создание подключения

В scrapli глобально есть два варианта подключения: используя общий класс Scrapli/AsyncScrapli,
который выбирает нужный driver по параметру platform или конкретный driver,
например, IOSXEDriver/AsyncIOSXEDriver. При этом параметры передаются те же самые и конкретному
драйверу и Scrapli/AsyncScrapli.

---

### Параметры подключения

* host - IP-адрес или имя хоста
* auth_username - имя пользователя
* auth_password - пароль
* auth_secondary - пароль на enable
* auth_strict_key - контролирует проверку SSH ключей сервера, а именно разрешать
  ли подключаться к серверам ключ которых не сохранен в ssh/known_hosts.
  False - разрешить подключение (по умолчанию значение True)
* platform - нужно указывать при использовании Scrapli
* transport - какой транспорт использовать
* transport_options - опции для конкретного транспорта

---
### Подключение

Процесс подключения немного отличается в зависимости от того используется
менеджер контекста или нет. При подключении без менеджера контекста, сначала надо
передать параметры драйверу или Scrapli, а затем вызвать метод open:

```python

from scrapli import AsyncScrapli

r1 = {
   "host": "192.168.100.1",
   "auth_username": "cisco",
   "auth_password": "cisco",
   "auth_secondary": "cisco",
   "auth_strict_key": False,
   "platform": "cisco_iosxe",
   "transport": "asyncssh",
}

In [2]: ssh = AsyncScrapli(**r1)

In [5]: await ssh.open()

In [6]: await ssh.get_prompt()
Out[6]: 'R1#'

In [5]: ssh.close()
```



---
### Подключение с менеджером контекста

```python
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

### Отправка команд

В scrapli есть несколько методов для отправки команд:

* ``send_command`` - отправить одну show команду
* ``send_commands`` - отправить список show команд
* ``send_commands_from_file`` - отправить show команды из файла
* ``send_config`` - отправить одну команду в конфигурационном режиме
* ``send_configs`` - отправить список команд в конфигурационном режиме
* ``send_configs_from_file`` - отправить команды из файла в конфигурационном режиме
* ``send_interactive``

Все эти методы возвращают объект Response, а не вывод команды в виде строки.

---
### Объект Response

Метод send_command и другие методы для отправки команд на оборудование
возвращают объект Response (не вывод команды).
Response позволяет получить не только вывод команды, но и такие вещи как
время работы команды, выполнилась команда с ошибками или без, структурированный
вывод с помощью textfsm и так далее.

```python
In [15]: reply = await ssh.send_command("sh clock")

In [16]: reply
Out[16]: Response(host='192.168.139.1',channel_input='sh clock',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command'])

In [17]: print(reply)
Response <Success: True>
```

---
### Объект Response

Получить вывод команды можно обратившись к атрибуту result:

```python
In [17]: reply.result
Out[17]: '*17:31:54.232 UTC Wed Mar 31 2021'
```

Атрибут raw_result содержит байтовую строку с полным выводом:

```python
In [18]: reply.raw_result
Out[18]: b'\n*17:31:54.232 UTC Wed Mar 31 2021\nR1#'
```

---

### elapsed_time

Для команд, которые выполняются дольше обычных show, может быть необходимо
знать время выполнения команды:

```python
In [18]: r = ssh.send_command("ping 10.1.1.1")

In [19]: r.result
Out[19]: 'Type escape sequence to abort.\nSending 5, 100-byte ICMP Echos to 10.1.1.1, timeout is 2 seconds:\n.....\nSuccess rate is 0 percent (0/5)'

In [20]: r.elapsed_time
Out[20]: 10.047594

In [21]: r.start_time
Out[21]: datetime.datetime(2021, 4, 1, 7, 10, 56, 63697)

In [22]: r.finish_time
Out[22]: datetime.datetime(2021, 4, 1, 7, 11, 6, 111291)
```

---
### Метод send_command

Метод ``send_command`` позволяет отправить одну команду на устройство.

```python
In [14]: reply = await ssh.send_command("sh clock")
```

---
### Метод send_command

Параметры метода (все эти параметры надо передавать как ключевые):

* ``strip_prompt`` - удалить приглашение из вывода. По умолчанию удаляется
* ``failed_when_contains`` - если вывод содержит указанную строку или одну из
  строк в списке, будет считаться, что команда выполнилась с ошибкой
* ``timeout_ops`` - максимальное время на выполнение команды, по умолчанию
  равно 30 секунд для IOSXEDriver

---
### Метод send_command

```python
In [15]: reply = await ssh.send_command("sh clock")

In [16]: reply
Out[16]: Response(host='192.168.139.1',channel_input='sh clock',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command'])
```

---
### Метод send_command. Параметр timeout_ops

Параметр timeout_ops указывает сколько ждать выполнения команды:

```python
In [18]: await ssh.send_command("ping 8.8.8.8", timeout_ops=20)
Out[18]: Response(host='192.168.139.1',channel_input='ping 8.8.8.8',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command'])
```

Если команда не выполнилась за указанное время, сгенерируется исключение
ScrapliTimeout (вывод сокращен):

```python
In [20]: await ssh.send_command("ping 8.8.8.8", timeout_ops=2)
---------------------------------------------------------------------------
CancelledError                            Traceback (most recent call last)
File /usr/local/lib/python3.11/asyncio/tasks.py:490, in wait_for(fut, timeout)
ScrapliTimeout: timed out sending input to device
```

---
### TextFSM

Кроме получения обычного вывода команды, scrapli также позволяет получить
структурированный вывод, например, с помощью метода textfsm_parse_output:

```python
In [21]: reply = await ssh.send_command("sh ip int br")

In [22]: reply.textfsm_parse_output()
Out[22]:
In [27]: pprint(reply.textfsm_parse_output())
[{'intf': 'FastEthernet0/0', 'ipaddr': '192.168.139.1', 'proto': 'up', 'status': 'up'},
 {'intf': 'FastEthernet0/1', 'ipaddr': 'unassigned', 'proto': 'down', 'status': 'administratively down'},
 {'intf': 'Loopback11', 'ipaddr': '11.1.1.1', 'proto': 'up', 'status': 'up'}]
```

---
### Обнаружение ошибок

Методы для отправки команд автоматически проверяют вывод на наличие ошибок.
Для каждого вендора/типа оборудования это свои ошибки, плюс можно самостоятельно
указать наличие каких строк в выводе будет считаться ошибкой.
По умолчанию для IOSXEDriver ошибками будут считаться такие строки:

```python
In [21]: ssh.failed_when_contains
Out[21]:
['% Ambiguous command',
 '% Incomplete command',
 '% Invalid input detected',
 '% Unknown command']
```

---
### Обнаружение ошибок

Атрибут failed у объекта Response возвращает True, если команда отработала с
ошибкой и False, если без ошибки:

```python
In [23]: reply = await ssh.send_command("sh clck")

In [24]: reply.result
Out[24]: "        ^\n% Invalid input detected at '^' marker."

In [26]: reply.failed
Out[26]: True
```

---
### Метод send_config

Метод ``send_config`` позволяет отправить одну команду конфигурационного режима.

```python
In [33]: r = await ssh.send_config("username user1 password password1")
```

---
### Метод send_config

По умолчанию, при использовании
send_config, в атрибуте result будет пустая строка (если не было ошибки при
выполнении команды):

```python
In [34]: r.result
Out[34]: ''
```

Параметр ``strip_prompt=False``:

```python
In [37]: r = await ssh.send_config("username user1 password password1", strip_prompt=False)

In [38]: r.result
Out[38]: 'R1(config)#'
```

---
### Методы send_commands, send_configs

Методы send_commands, send_configs отличаются от send_command, send_config тем,
что могут отправлять несколько команд.
Кроме того, эти методы возвращают не Response, а MultiResponse, который можно
в целом воспринимать как список Response, по одному для каждой команды.

```python
In [44]: reply = await ssh.send_commands(["sh clock", "sh ip int br"])

In [39]: reply
Out[39]:
MultiResponse([Response(host='192.168.139.1',channel_input='sh clock',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command']),
               Response(host='192.168.139.1',channel_input='sh ip int br',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command'])])

In [46]: for r in reply:
    ...:     print(r)
    ...:     print(r.result)
    ...:
Response <Success: True>
*12:11:36.779 UTC Mon Apr 24 2023
Response <Success: True>
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            192.168.139.1   YES NVRAM  up                    up
FastEthernet0/1            unassigned      YES NVRAM  administratively down down
Loopback11                 11.1.1.1        YES manual up                    up

In [47]: reply.result
Out[47]: 'sh clock\n*08:38:20.115 UTC Thu Apr 1 2021sh ip int br\nInterface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up\nEthernet0/2                unassigned      YES NVRAM  up                    up\nEthernet0/3                192.168.130.1   YES NVRAM  up                    up'

In [43]: reply[0]
Out[43]: Response(host='192.168.139.1',channel_input='sh clock',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command'])

In [44]: reply[1]
Out[44]: Response(host='192.168.139.1',channel_input='sh ip int br',textfsm_platform='cisco_iosxe',genie_platform='iosxe',failed_when_contains=['% Ambiguous command', '% Incomplete command', '% Invalid input detected', '% Unknown command'])

In [50]: reply[0].result
Out[50]: '*08:38:20.115 UTC Thu Apr 1 2021'
```

---
### stop_on_failed

Если указать ``stop_on_failed=True``, после возникновения
ошибки в какой-то команде, следующие команды не будут выполняться:

```python
In [59]: reply = await ssh.send_commands(["ping 192.168.100.2", "sh clck", "sh ip int br"], stop_on_failed=True)

In [60]: reply
Out[60]: MultiResponse <Success: False; Response Elements: 2>

In [61]: reply.result
Out[61]: "ping 192.168.100.2\nType escape sequence to abort.\nSending 5, 100-byte ICMP Echos to 192.168.100.2, timeout is 2 seconds:\n!!!!!\nSuccess rate is 100 percent (5/5), round-trip min/avg/max = 1/2/6 mssh clck\n        ^\n% Invalid input detected at '^' marker."

In [62]: for r in reply:
    ...:     print(r)
    ...:     print(r.result)
    ...:
Response <Success: True>
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.100.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/6 ms
Response <Success: False>
        ^
% Invalid input detected at '^' marker.
```

---
## Подключение telnet

---
### Подключение telnet

Для подключения к оборудовани по Telnet надо указать transport равным
telnet и обязательно указать параметр port равным 23 (или тому порту который
используется у вас для подключения по Telnet):

```python
from scrapli.driver.core import IOSXEDriver
from scrapli.exceptions import ScrapliException
import socket

r1 = {
    "host": "192.168.100.1",
    "auth_username": "cisco",
    "auth_password": "cisco2",
    "auth_secondary": "cisco",
    "auth_strict_key": False,
    "transport": "telnet",
    "port": 23,  # обязательно указывать при подключении telnet
}


def send_show(device, show_command):
    try:
        with IOSXEDriver(**r1) as ssh:
            reply = ssh.send_command(show_command)
            return reply.result
    except socket.timeout as error:
        print(error)
    except ScrapliException as error:
        print(error, device["host"])


if __name__ == "__main__":
    output = send_show(r1, "sh ip int br")
    print(output)
```

