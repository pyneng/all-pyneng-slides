## Основы asyncio

---
## asyncio

asyncio - это библиотека для написания параллельного* кода с использованием синтаксиса async/await.

asyncio используется в качестве основы для нескольких асинхронных фреймворков Python,
которые обеспечивают высокопроизводительные сетевые и веб-серверы, библиотеки подключения
к базам данных, распределенные очереди задач и т. д.


---
## Потоки, процессы, async

В Python есть три основных способа запускать задачи параллельно*:

* процессы
* потоки
* асинхронное программирование

Модули, которые позволяют запустить задачи:

* в процессах: multiprocessing, concurrent.futures
* в потоках: threading, concurrent.futures
* асинхронно: asyncio и масса других вариантов и сторонних фреймворков (Tornado, FastAPI, ...)

---
## CPU-bound vs I/O bound

CPU/IO указывают ограничивающий фактор.

Примеры:

* CPU-bound - обработка картинок/видео
* I/O bound - Telnet подключение к сетевому устройству
* CPU-bound и I/O bound - видео трансляция

---
## Блокирующий/неблокирующий вызов

Блокирующий (blocking) вызов останавливает работу программы, пока не будет получен результат вызова.
Например, ``subprocess.run`` это блокирующий вызов и если вызвать, к примеру,
ping какого-то адреса, то строка с subprocess.run будет блокировать выполнение
скрипта пока не отработает ping.

Неблокирующий (non-blocking) вызов возвращает какой-то объект сразу и, как правило,
предполагается что позже надо будет еще раз вызывать какой-то метод этого
объекта, чтобы получить результат вызова. Например, ``subprocess.Popen``:

```python
import subprocess

def ping_ip_addresses(ip_list):
    reachable = []
    unreachable = []
    result = []
    for ip in ip_list:
        p = subprocess.Popen(
            ["ping", "-c", "3", "-n", ip],
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
        )
        result.append(p)
    for ip, p in zip(ip_list, result):
        returncode = p.wait()
        if returncode == 0:
            reachable.append(ip)
        else:
            unreachable.append(ip)
    return reachable, unreachable
```

---
## Сокеты (sockets)

[Сокет (socket)](https://en.wikipedia.org/wiki/Network_socket) - это программная структура,
которая служит конечной точкой для отправки и получения данных по сети.
Структура и свойства сокета определяются API для сетевой архитектуры.

Сокет может быть клиентским (connected) или серверным (listening).

