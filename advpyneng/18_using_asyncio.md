## Использование asyncio

---
## Использование asyncio

* создание классов
* асинхронный менеджер контекста
* асинхронная итерация
* асинхронные генераторы
* list/dict/set comprehensions
* декораторы для сопрограмм
* asyncio subprocess
* semaphore
* запуск синхронного кода в потоках


---
## Создание классов с asyncio

---
## Создание классов с asyncio

В целом асинхронные классы пишутся так же, как синхронные, но есть несколько
отличий:

* особенности создания экземпляров, так как ``__init__`` не может быть сопрограммой
* некоторые специальные методы имеют отдельные версии для асинхронных классов,
  например, ``__aenter__``, ``__aexit__``, ``__anext__``, ``__aiter__``
* методы класса могут быть сопрограммами или обычными функциями


---
## Создание экземпляра

---
## Создание экземпляра

В синхронном варианте, при создании класса который подключается
по SSH, само подключение обычно будет выполняться в методе ``__init__``
(напрямую или через вызов другого метода).
В асинхронных классах так сделать не получится, так как для выполнения
подключения в ``__init__``, надо сделать init сопрограммой. Так как init
должен возвращать None, если сделать init сопрограммой, возникнет ошибка:

```python
class ConnectSSH:
    async def __init__(self):
        await asyncio.sleep(0.1)


In [5]: r1 = ConnectSSH()
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-5-ceb9b33fd87d> in <module>
----> 1 r1 = ConnectSSH()

TypeError: __init__() should return None, not 'coroutine'
```

---
## Создание экземпляра

Пожалуй, самый распространенный вариант решения проблемы - создание отдельного
метода для подключения (вместе с добавлением асинхронного менеджера контекста).

```python
class ConnectAsyncSSH:
    def __init__(self, host, username, password, enable_password, connection_timeout=5):
        self.host = host
        self.username = username
        self.password = password
        self.enable_password = enable_password
        self.connection_timeout = connection_timeout

    async def connect(self):
        self._ssh = asyncssh.connect(
            host=self.host,
            username=self.username,
            password=self.password,
            encryption_algs="+aes128-cbc,aes256-cbc",
            timeout=self.connection_timeout,
        )
        self.writer, self.reader, _ = await self._ssh.open_session(
            term_type="Dumb", term_size=(200, 24)
        )
        await self.reader.readuntil(">")
        self.writer.write("enable\n")
        await self.reader.readuntil("Password")
        self.writer.write(f"{self.enable_password}\n")
        await self.reader.readuntil("#")
        self.writer.write("terminal length 0\n")
        await self.reader.readuntil("#")

    async def get_prompt(self):
        self.writer.write("\n")
        output = await self.reader.readuntil("#")
        return output
```

---
## Создание экземпляра

В этом случае для подключения надо сначала создать экземпляр класса, а потом
вызвать метод connect где выполняется само подключение:

```python
async def main():
    r1 = {
        "host": "192.168.100.1",
        "username": "cisco",
        "password": "cisco",
        "enable_password": "cisco",
    }
    ssh = ConnectAsyncSSH(**r1)
    await ssh.connect()
    print(await ssh.get_prompt())


if __name__ == "__main__":
    asyncio.run(main())
```

Как правило, вместе с таким вариантом используется асинхронный менеджер контекста.
В этом случае, метод connect вызывается в методе ``__aenter__`` (аналог ``__enter__``).

---
## classmethod

Еще один распространенный вариант - использование classmethod для создания экземпляра.

```python
from pprint import pprint
import asyncio
import asyncssh


class ConnectAsyncSSH:
    @classmethod
    async def connect(cls, host, username, password, enable_password, connection_timeout=5):
        self = cls()

        self.host = host
        self.username = username
        self.password = password
        self.enable_password = enable_password
        self.connection_timeout = connection_timeout
        self._ssh = await asyncio.wait_for(
            asyncssh.connect(
                host=self.host,
                username=self.username,
                password=self.password,
                encryption_algs="+aes128-cbc,aes256-cbc",
            ),
            timeout=self.connection_timeout,
        )
        self.writer, self.reader, _ = await self._ssh.open_session(
            term_type="Dumb", term_size=(200, 24)
        )
        await self.reader.readuntil(">")
        self.writer.write("enable\n")
        await self.reader.readuntil("Password")
        self.writer.write(f"{self.enable_password}\n")
        await self.reader.readuntil("#")
        self.writer.write("terminal length 0\n")
        await self.reader.readuntil("#")
        return self

    async def get_prompt(self):
        self.writer.write("\n")
        output = await self.reader.readuntil("#")
        return output
```

---
## classmethod

Создание экземпяра выглядит так:

```python
async def main():
    r1 = {
        "host": "192.168.100.1",
        "username": "cisco",
        "password": "cisco",
        "enable_password": "cisco",
    }
    ssh = await ConnectAsyncSSH.connect(**r1)
    print(await ssh.get_prompt())


if __name__ == "__main__":
    asyncio.run(main())
```

---
## Асинхронный менеджер контекста

---
## Асинхронный менеджер контекста

В целом, смысл работы асинхронного менеджера контекста остается таким же, как
и в синхронном. Только так как методы для подключения к устройству являются
сопрограммами, их надо правильно ожидать (await) и соответственно специальные
методы для создания асинхронного менеджера контекста, тоже должны быть сопрограммами.
Поэтому для асинхронного менеджера контекста созданы отдельные специальные методы
``__aenter__`` и ``__aexit__`` и соответственно чтобы они вызывались, надо
использовать асинхронный менеджер контекста ``async with``, а не обычный ``with``.

> ``async with`` можно использовать только внутри асинхронной функции.

---
## Асинхронный менеджер контекста

```python
class ConnectAsyncSSH:
    def __init__(self, host, username, password, enable_password, connection_timeout=5):
        self.host = host
        self.username = username
        self.password = password
        self.enable_password = enable_password
        self.connection_timeout = connection_timeout

    async def connect(self):
        self._ssh = await asyncio.wait_for(
            asyncssh.connect(
                host=self.host,
                username=self.username,
                password=self.password,
                encryption_algs="+aes128-cbc,aes256-cbc",
            ),
            timeout=self.connection_timeout,
        )
        self.writer, self.reader, _ = await self._ssh.open_session(
            term_type="Dumb", term_size=(200, 24)
        )
        await self.reader.readuntil(">")
        self.writer.write("enable\n")
        await self.reader.readuntil("Password")
        self.writer.write(f"{self.enable_password}\n")
        await self.reader.readuntil("#")
        self.writer.write("terminal length 0\n")
        await self.reader.readuntil("#")

    async def get_prompt(self):
        self.writer.write("\n")
        output = await self.reader.readuntil("#")
        return output

    def close(self):
        self._ssh.close()

    async def __aenter__(self):
        await self.connect()
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        self.close()
```

---
## Асинхронный менеджер контекста

Пример использования:

```python
async def main():
    r1 = {
        "host": "192.168.100.1",
        "username": "cisco",
        "password": "cisco",
        "enable_password": "cisco",
    }
    async with ConnectAsyncSSH(**r1) as ssh:
        print(await ssh.get_prompt())


if __name__ == "__main__":
    asyncio.run(main())
```

---
## Асинхронная итерация

---
## Асинхронная итерация

При работе с асинхронным кодом, могут использоваться как обычные итераторы
и итерируемые объекты, так и асинхронные. Асинхронные итераторы и итерируемые
объекты в целом работают так же, как и синхронные.

Для асинхронных итераторов и итерируемых объектов созданы отдельные методы
``__aiter__`` и ``__anext__``, плюс для перебора асинхронных итерируемых объектов
есть цикл ``async for``.

> ``async for`` можно использовать только внутри асинхронной функции.

---
## Асинхронная итерация

Асинхронный итерируемый объект (asynchronous iterable) - это объект, который можно
использовать в ``async for``. В асинхронном итерируемом объекте должен быть метод
``__aiter__``, который возвращает асинхронный итератор.

Асинхронный итератор (asynchronous iterator) - это объект, у которого есть методы
``__anext__`` и ``__aiter__``. Метод ``__anext__`` должен возвращать awaitable объект.
При завершении итерации генерируется исключение ``StopAsyncIteration``.

---
## Асинхронная итерация

```python
class CheckConnection:
    def __init__(self, device_list):
        self.device_list = device_list
        self._current_device = 0

    async def _scan_device(self, device):
        ip = device["host"]
        try:
            async with timeout(5): # для asynctelnet
                async with AsyncScrapli(**device) as conn:
                    prompt = await conn.get_prompt()
                return True, prompt
        except (ScrapliException, asyncio.exceptions.TimeoutError) as error:
            return False, f"{error} {ip}"

    async def __anext__(self):
        if self._current_device >= len(self.device_list):
            raise StopAsyncIteration
        device_params = self.device_list[self._current_device]
        scan_results = await self._scan_device(device_params)
        self._current_device += 1
        return scan_results

    def __aiter__(self):
        return self
```

> Метод ``__anext__`` сопрограмма, а ``__aiter__`` нет.

---
## Асинхронная итерация

Для перебора асинхронного итератора надо использовать ``async for`` и соответственно
перебор надо делать в сопрограмме:

```python
async def ssh_scan(devices):
    check = CheckConnection(devices)
    async for status, msg in check:
        if status:
            print(f"{datetime.now()} Подключение успешно: {msg}")
        else:
            print(f"{datetime.now()} Не удалось подключиться: {msg}")


if __name__ == "__main__":
    with open("devices_asyncssh.yaml") as f:
        devices = yaml.safe_load(f)
    asyncio.run(ssh_scan(devices))
```

Вызов скрипта:

```
$ python ex05_async_iterator_ssh.py
2021-04-19 11:25:40.775 Подключение успешно: R1#
2021-04-19 11:25:41.334 Подключение успешно: R2#
2021-04-19 11:25:43.638 Не удалось подключиться: all authentication methods failed 192.168.100.3
2021-04-19 11:25:48.647 Не удалось подключиться: timed out opening connection to device 192.168.100.11
```

---
## Асинхронная итерация

Итератор CheckConnection проверяет устройства последовательно, но вместе с этим
итератором, параллельно можно запускать что-то еще, например, паралелльно проверять
подключение telnet (файл ex06_async_iterator_telnet_ssh.py):

```python
async def scan(devices, protocol):
    check = CheckConnection(devices)
    async for status, msg in check:
        if status:
            print(f"{datetime.now()} {protocol}. Подключение успешно: {msg}")
        else:
            print(f"{datetime.now()} {protocol}. Не удалось подключиться: {msg}")


async def main():
    with open("devices_asyncssh.yaml") as f:
        devices_ssh = yaml.safe_load(f)
    with open("devices_asynctelnet.yaml") as f:
        devices_telnet = yaml.safe_load(f)
    await asyncio.gather(scan(devices_ssh, "SSH"), scan(devices_telnet, "Telnet"))


if __name__ == "__main__":
    asyncio.run(main())
```

Вызов скрипта:

```
$ python ex06_async_iterator_telnet_ssh.py
2021-04-19 11:30:14.820195 SSH. Подключение успешно: R1#
2021-04-19 11:30:14.983307 Telnet. Подключение успешно: R1#
2021-04-19 11:30:15.296011 SSH. Подключение успешно: R2#
2021-04-19 11:30:15.449338 Telnet. Подключение успешно: R2#
2021-04-19 11:30:17.599259 SSH. Не удалось подключиться: all authentication methods failed 192.168.100.3
2021-04-19 11:30:19.775107 Telnet. Не удалось подключиться: username/login prompt seen more than once, assuming auth failed 192.168.100.3
2021-04-19 11:30:22.603411 SSH. Не удалось подключиться:  192.168.100.11
2021-04-19 11:30:24.777987 Telnet. Не удалось подключиться:  192.168.100.11
```

---
## Асинхронные генераторы

---
## Асинхронные генераторы

Доступны с Python 3.6

```python
async def check_connection(devices_list):
    for device in devices_list:
        ip = device["host"]
        transport = device.get("transport")
        try:
            async with timeout(5):  # для asynctelnet
                async with AsyncScrapli(**device) as conn:
                    prompt = await conn.get_prompt()
                yield True, f"{ip=} {prompt=} {transport=}"
        except (ScrapliException, asyncio.exceptions.TimeoutError) as error:
            yield False, f"{ip=} {error=} {transport=}"


async def scan(devices):
    check = check_connection(devices)
    async for status, msg in check:
        if status:
            logging.info(f"Подключение успешно {msg}")
        else:
            logging.warning(f"Не удалось подключиться {msg}")


async def scan_all(telnet_list, ssh_list):
    await asyncio.gather(scan(telnet_list), scan(ssh_list))
```

---
## list/dict/set comprehensions

---
## list/dict/set comprehensions

В list/dict/set comprehensions можно использовать await и, при переборе
асинхронного итератора - ``async for``.

```python
async def send_show(device, command):
    print(f">>> connect to {device['host']}")
    try:
        async with AsyncScrapli(**device) as conn:
            result = await conn.send_command(command)
            return result.result
    except ScrapliException as error:
        print(error, device["host"])


async def send_command_to_devices(devices, commands):
    tasks = [asyncio.create_task(send_show(device, commands)) for device in devices]
    result = [await task for task in tasks]
    return result


if __name__ == "__main__":
    with open("devices_async.yaml") as f:
        devices = yaml.safe_load(f)
    result = asyncio.run(send_command_to_devices(devices, "sh clock"))
    pprint(result, width=120)
```

---
## list/dict/set comprehensions

Также в list comprehensions может использоваться ``async for``, при переборе
асинхронного итератора.

```python
class CheckConnection:
    def __init__(self, device_list):
        self.device_list = device_list
        self._current_device = 0

    async def _scan_device(self, device):
        ip = device["host"]
        try:
            async with AsyncScrapli(**device) as conn:
                prompt = await conn.get_prompt()
            return True, prompt
        except (ScrapliException, asyncio.exceptions.TimeoutError) as error:
            return False, f"{error} {ip}"

    async def __anext__(self):
        if self._current_device >= len(self.device_list):
            raise StopAsyncIteration
        device_params = self.device_list[self._current_device]
        scan_results = await self._scan_device(device_params)
        self._current_device += 1
        return scan_results

    def __aiter__(self):
        return self


async def ssh_scan(devices):
    check = CheckConnection(devices)
    results = [out async for out in check]
    return results


if __name__ == "__main__":
    with open("devices_asyncssh.yaml") as f:
        devices = yaml.safe_load(f)
    result = asyncio.run(ssh_scan(devices))
    pprint(result)
    for status, msg in result:
        if status:
            print(f"{datetime.now()} SSH. Подключение успешно: {msg}")
        else:
            print(f"{datetime.now()} SSH. Не удалось подключиться: {msg}")
```


---
## list/dict/set comprehensions

В примере выше в list comp используется именно ``async for`` потому что выполняется
перебор асинхронного итератора:

```python
results = [out async for out in check]
```

---
## Декораторы для сопрограмм

---
## Декораторы для сопрограмм

Декораторы для сопрограмм в целом создаются так же, как и
декораторы для обычных функций.
Основное отличие в том, что если декоратор должен подменять функцию
(это делается в большинстве декораторов функций), надо чтобы декоратор подменял
сопрограмму сопрограммой.

---
## Декораторы для сопрограмм

Пример декоратора timecode, который замеряет время выполнения сопрограммы:

```python
def timecode(function):
    @wraps(function)
    async def wrapper(*args, **kwargs):
        start_time = datetime.now()
        result = await function(*args, **kwargs)
        print(">>> Функция выполнялась:", datetime.now() - start_time)
        return result
    return wrapper


@timecode
async def send_show(device, command):
    try:
        async with AsyncScrapli(**device) as conn:
            result = await conn.send_command(command)
            await asyncio.sleep(2)
            return result.result
    except ScrapliException as error:
        print(error, device["host"])
```

---
## Декораторы для сопрограмм

Для того чтобы замерить время выполнения send_show, надо дождаться выполнения
сопрограммы - сделать ``await``, а ``await`` можно писать только внутри асинхронной функции,
соответственно функция wrapper должна быть сопрограммой.

Аналогичный обычный декоратор:

```python
def timecode(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        start_time = datetime.now()
        result = function(*args, **kwargs)
        print('>>> Функция выполнялась:', datetime.now() - start_time)
        return result
    return wrapper
```

> Измерение времени выполнения сопрограммы неоднозначное занятие, так как оно
> зависит не только от самой сопрограммы, но и от того что еще работает в цикле событий.

---
## Встроенные декораторы

Многие встроенные декораторы работают с функциями/методами, которые являются
сопрограммами: functools.wraps, classmethod, staticmethod.

Сопрограммы можно декорировать и property, но по смыслу property обычно
применяется к чему-то, что выполняется быстро, поэтому стоит, как минимум,
подумать о том надо ли делать property метод, который выполняет операции ввода-вывода.

---
## Встроенные декораторы

Пример использования classmethod:

```python
class ConnectAsyncSSH:

    @classmethod
    async def connect(cls, host, username, password, enable_password, connection_timeout=5):
        self = cls()

        self.host = host
        self.username = username
        self.password = password
        self.enable_password = enable_password
        self.connection_timeout = connection_timeout
        self._ssh = await asyncio.wait_for(
            asyncssh.connect(
                host=self.host,
                username=self.username,
                password=self.password,
                encryption_algs="+aes128-cbc,aes256-cbc",
            ),
            timeout=self.connection_timeout,
        )

    async def get_prompt(self):
        self.writer.write("\n")
        output = await self.reader.readuntil("#")
        return output
```

---
## Встроенные декораторы

Пример использования staticmethod:

```python
class PingIP:
    def __init__(self, ip_list):
        self.ip_list = ip_list

    @staticmethod
    async def _ping(ip):
        reply = await asyncio.create_subprocess_shell(
            f"ping -c 3 -n {ip}",
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE,
        )
        stdout, stderr = await reply.communicate()
        ip_is_reachable = reply.returncode == 0
        return ip, ip_is_reachable

    async def scan(self):
        ping_ok = []
        ping_not_ok = []
        coroutines = [self._ping(ip) for ip in self.ip_list]
        result = await asyncio.gather(*coroutines)
        for ip, status in result:
            if status:
                ping_ok.append(ip)
            else:
                ping_not_ok.append(ip)
        return ping_ok, ping_not_ok
```

---
## asyncio subprocess

---
## asyncio subprocess

```python
coroutine asyncio.create_subprocess_exec(program, *args, stdin=None, stdout=None, stderr=None, limit=None, **kwds)
coroutine asyncio.create_subprocess_shell(cmd, stdin=None, stdout=None, stderr=None, limit=None, **kwds)
```

> [loop.subprocess_exec](https://docs.python.org/3/library/asyncio-eventloop.html#asyncio.loop.subprocess_exec)

---
## asyncio subprocess

```python
async def ping(ip):
    reply = await asyncio.create_subprocess_shell(
        f"ping -c 3 -n {ip}",
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE,
    )

    stdout, stderr = await reply.communicate()

    ip_is_reachable = reply.returncode == 0
    return ip_is_reachable


async def ping_ip_list(ip_list):
    coroutines = [ping(ip) for ip in ip_list]
    result = await asyncio.gather(*coroutines)
    return result
```

---
## Запуск синхронного кода в потоках

---
## Запуск синхронного кода в потоках

Сопрограмма ``asyncio.to_thread`` позволяет запустить блокирующую операцию
в потоке. 

```python
asyncio.to_thread(func, /, *args, **kwargs)
```

```python
def blocking_io():
    time.sleep(1)


async def main():
    await asyncio.gather(
        asyncio.to_thread(blocking_io),
        asyncio.sleep(1),
    )


asyncio.run(main())
```

---
## Запуск синхронного кода в потоках

Эта сопрограмма появилась в Python 3.9, до этого использовалась ``loop.run_in_executor``.
При этом to_thread это по сути `обертка вокруг loop.run_in_executor
с использованием ThreadPoolExecutor по умолчанию, с максимальным количеством потоков
по умолчанию.

Начиная с версии Python 3.8 значение по умолчанию для max_workers высчитывается
так ``min(32, os.cpu_count() + 4)``.

---
## Semaphore

---
## Semaphore

```python
async def connect(ip, semaphore):
    async with semaphore:
        print(f"Подключаюсь к {ip}")
        await asyncio.sleep(1)
        print(f"Ответ от {ip}")


async def main():
    sem = asyncio.Semaphore(20)
    coroutines = [connect(i, sem) for i in range(500)]
    await asyncio.gather(*coroutines)


asyncio.run(main())
```

---
## Semaphore

```python
async def send_show(device, show):
    print(f'>>> Подключаюсь к {device["host"]}')
    try:
        async with AsyncScrapli(**device) as ssh:
            reply = await ssh.send_command(show)
            output = reply.result
            print(f'<<< Получен результат от {device["host"]}')
        return output
    except ScrapliException as error:
        print(error, device["host"])


async def connect_ssh_with_semaphore(semaphore, function, *args, **kwargs):
    async with semaphore:
        return await function(*args, **kwargs)


async def send_command_to_devices(devices, commands, max_workers=50):
    semaphore = asyncio.Semaphore(max_workers)
    coroutines = [
        connect_ssh_with_semaphore(semaphore, send_show, device, commands)
        for device in devices
    ]
    result = await asyncio.gather(*coroutines)
    return result


if __name__ == "__main__":
    with open("devices_async.yaml") as f:
        devices = yaml.safe_load(f)
    result = asyncio.run(send_command_to_devices(devices, "sh ip int br"))
    pprint(result, width=120)
```

