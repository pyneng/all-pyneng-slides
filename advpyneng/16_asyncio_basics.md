## Основы asyncio

---
## asyncio

asyncio - это библиотека для написания параллельного кода с использованием синтаксиса async/await.

asyncio используется в качестве основы для нескольких асинхронных фреймворков Python,
которые обеспечивают высокопроизводительные сетевые и веб-серверы, библиотеки подключения
к базам данных, распределенные очереди задач и т. д.


---
## Потоки, процессы, async

В Python есть три основных способа запускать задачи "параллельно":

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

---
### Multitasking

* Preemptive multitasking - ОС решает, когда надо сделать переключение
* Cooperative multitasking - переключения указаны в коде

---
## Основы asyncio

---
## Основы asyncio

asyncio - это библиотека для написания параллельного кода с использованием синтаксиса async/await.

asyncio используется в качестве основы для нескольких асинхронных фреймворков Python,
которые обеспечивают высокопроизводительные сетевые и веб-серверы, библиотеки подключения
к базам данных, распределенные очереди задач и т. д.

Отличия asyncio от многопоточной работы:

* при работе с потоками, планировщик может прервать работу потока в любой момент,
  не всегда это "удобный" момент, поэтому надо явно указывать, что в какие-то моменты
  прерывать поток нельзя
* при работе с asyncio, мы явно указываем в каком месте надо сделать переключение (await)
* при использовании asyncio работа идет в одном потоке
* в потоках будет работать почти любой из распространенных модулей
* для работы с asyncio, как правило, надо будет искать альтернативные модули

---
## Терминология

* цикл событий (event loop) - менеджер управляющий различными задачами и сопрограммами
* сопрограмма (coroutine) в asyncio - специальный тип функций, которые создаются
  со словом async перед определением.
* future - это объект, который представляет отложенное вычисление.
* задача (task) - отвечает за управление работой сопрограммы, запрашивает статус
  сопрограммы. Является подклассом Future.

---
## Терминология

---
## Терминология
## Awaitables

Awaitables (ожидаемые объекты) - это объекты, которые можно использовать в выражении 
вместе с ``await``. Три базовых типа awaitables:

* сопрограммы (coroutines)
* задачи (tasks)
* future


---
## Терминология
## Coroutine (сопрограмма)

Как и с генераторами, различают:

* функцию сопрограмму - функция, которая создается с помощью ``async def`` (асинхронная функция)
* объект сопрограмму - объект, который возвращается при вызове функции сопрограммы

Функции-сопрограммы возвращают объекты сопрограммы, которые запускаются 
менеджером (циклом событий). Сопрограмма может периодически прерывать выполнение
и отдавать управление менеджеру, но при этом она не теряет состояние.

## Task (задача)

Объекты класса Task используются для запуска сопрограмм в цикле событий и для отслеживания
их состояния. Как только сопрограмма "обернута" в Task, например, с помощью функции
``asyncio.create_task``, сопрограмма автоматически запущена для выполнения.

## asyncio.Future

Future - это специальный низкоуровневый объект, который представляет отложенное 
вычисление асинхронных операций. Чаще всего, при работе с модулем asyncio, нет 
необходимости создавать Future напрямую, но некоторые функции могут возвращать Future.
Task является подклассом Future.
Менеджер может следить за future и ожидать их завершения.

---
## asyncio API

Модуль asyncio можно разделить на две части: высокоуровневый интерфейс для пользователей,
которые пишут программы и низкоуровневый интерфейс для авторов модулей, библиотек
и фреймворков на основе asyncio.

## [High-level API Index](https://docs.python.org/3.10/library/asyncio-api-index.html)
### Tasks

|           |                                                     |
|-----------|-----------------------------------------------------|
| ``run()`` | Create event loop, run a coroutine, close the loop. |
| ``create_task()`` | Start an asyncio Task. |
| ``await sleep()`` | Sleep for a number of seconds. |
| ``await gather()`` | Schedule and wait for things concurrently. |
| ``await wait_for()`` | Run with a timeout. |
| ``await shield()`` | Shield from cancellation. |
| ``await wait()`` | Monitor for completion. |
| ``current_task()`` | Return the current Task. |
| ``all_tasks()`` | Return all tasks for an event loop. |
| ``Task`` | Task object. |
| ``to_thread()`` | Asynchronously run a function in a separate OS thread. |
| ``run_coroutine_threadsafe()`` | Schedule a coroutine from another OS thread. |
| ``for in as_completed()`` | Monitor for completion with a for loop. |


---
## Сопрограммы и задачи

Создание сопрограммы (coroutine):

```python
import asyncio

async def main():
    print(f'Start {datetime.now()}')
    await asyncio.sleep(3)
    print(f'End   {datetime.now()}')


In [6]: coro = main()

In [7]: coro
Out[7]: <coroutine object main at 0xb449fdac>
```

---
## Сопрограммы и задачи

Как и с генераторами, различают:

* функцию сопрограмму - функция, которая создается с помощью ``async def``
* объект сопрограмму - объект, который возвращается при вызове функции сопрограммы

Создать сопрограмму недостаточно для того чтобы она запускалась
параллельно с другими сопрограммами - для управления сопрограммами нужен
менеджер - event loop. Также по умолчанию в сопрограмме код выполняется последовательно
и надо явно указывать в каких местах можно переключаться - await.


---
## Сопрограммы и задачи

Запустить сопрограмму можно несколькими способами:

* asyncio.run
* await
* asyncio.create_task

---
## asyncio.run

Функция asyncio.run запускает сопрограмму и возвращает результат:

```python
asyncio.run(coro, *, debug=False)
```

Функция asyncio.run всегда создает новый цикл событий и закрывает его в конце.
В идеале, функция asyncio.run должна вызываться в программе только один раз и использоваться
как основная точка входа.
Эту функцию нельзя вызвать, когда в том же потоке запущен другой цикл событий.

---
## asyncio.run

Запуск с помощью asyncio.run:

```python
In [8]: asyncio.run(coro)
Start 2019-10-30 06:36:03.396389
End   2019-10-30 06:36:06.399606

In [9]: asyncio.run(main())
Start 2019-10-30 06:46:22.162731
End   2019-10-30 06:46:25.166902
```

---
## await

Второй вариант запуска сопрограммы - ожидание ее результата в другой сопрограмме
с помощью ``await``.

Сопрограмма delay_print выводит указанное сообщение с задержкой:

```python
from datetime import datetime

async def delay_print(delay, task_name):
    print(f'>>> start {task_name}')
    await asyncio.sleep(delay)
    print(f'<<< end   {task_name}')
```

---
## await

Для запуска сопрограммы delay_print, ее результат ожидается в сопрограмме main:

```python
async def main():
    print(f'Start {datetime.now()}')
    await delay_print(4, 'task1')
    await delay_print(2, 'task2')
    print(f'End   {datetime.now()}')

In [5]: asyncio.run(main())
Start 2021-03-16 09:54:42.163949
>>> start task1
<<< end   task1
>>> start task2
<<< end   task2
End   2021-03-16 09:54:48.172434
```

---
## asyncio.create_task

Еще один вариант запуска сопрограммы - это создание задачи (task).
Обернуть сопрограмму в задачу и запланировать ее выполнение можно с помощью функции
asyncio.create_task. Она возвращает объект Task, который можно ожидать с await, как
и сопрограммы.

```python
asyncio.create_task(coro, *, name=None)
```

Функция asyncio.create_task позволяет запускать сопрограммы одновременно, так как
создание задачи означает для цикла, что надо запустить эту сопрограмму при первой
возможности.

---
## asyncio.create_task

Пример создания задач:

```python
async def delay_print(delay, task_name):
    print(f'>>> start {task_name}')
    await asyncio.sleep(delay)
    print(f'<<< end   {task_name}')


async def main():
    print(f'Start {datetime.now()}')
    task1 = asyncio.create_task(delay_print(4, 'task1'))
    task2 = asyncio.create_task(delay_print(2, 'task2'))

    await asyncio.sleep(1)
    print("Все задачи запущены")

    await task1
    await task2
    print(f'End   {datetime.now()}')


In [8]: asyncio.run(main())
Start 2021-03-16 09:58:10.817222
>>> start task1
>>> start task2
Все задачи запущены
<<< end   task2
<<< end   task1
End   2021-03-16 09:58:14.821104
```


---
## Запуск нескольких awaitables

---
## Запуск нескольких awaitables

* asyncio.gather
* asyncio.as_completed
* asyncio.wait

---
## asyncio.gather

---
## asyncio.gather

Функция gather запускает на выполнение awaitable объекты, которые перечислены в
последовательности ``aws``:

```python
asyncio.gather(*aws, return_exceptions=False)
```

Если какие-то из объектов являются сопрограммами, они автоматически оборачиваются в задачи
и планируются на выполнение уже как объекты Task.

---
## asyncio.gather

```python
async def connect_ssh(ip, command):
    print(f'Подключаюсь к {ip}')
    await asyncio.sleep(ip)
    print(f'Отправляю команду {command} на устройство {ip}')
    await asyncio.sleep(1)
    return f"{command} {ip}"


async def send_command_to_devices(ip_list, command):
    coroutines = map(connect_ssh, ip_list, repeat(command))
    result = await asyncio.gather(*coroutines)
    return result
```

---
## asyncio.gather

Если все объекты отработали корректно, asyncio.gather вернет список со значениями,
которые вернули объекты. Порядок значений в списке соответствует порядку объектов:

```python
In [2]: ip_list = [5, 2, 3, 7]

In [3]: result = asyncio.run(send_command_to_devices(ip_list, 'test'))
Подключаюсь к 5
Подключаюсь к 2
Подключаюсь к 3
Подключаюсь к 7
Отправляю команду test на устройство 2
Отправляю команду test на устройство 3
Отправляю команду test на устройство 5
Отправляю команду test на устройство 7

In [4]: result
Out[4]: ['test 5', 'test 2', 'test 3', 'test 7']
```


---
## asyncio.as_completed

---
## asyncio.as_completed

Функция as_completed запускает на выполнение awaitable объекты, которые перечислены
в последовательности aws:

```python
asyncio.as_completed(aws, *, timeout=None)
```

Возвращает итератор с сопрограмами, в порядке получения результата от сопрограмм.
Функция генерирует исключение asyncio.TimeoutError, если за timeout отработали не
все сопрограмы.

---
## asyncio.as_completed

```python
async def delay_print(task_name):
    delay = round(random.random() * 10, 2)
    print(f'>>> start {task_name} sleep {delay}')
    await asyncio.sleep(delay)
    print(f'<<< end   {task_name}')
    return task_name


async def main():
    coroutines = [delay_print(f"task {i}") for i in range(1, 6)]
    for cor in asyncio.as_completed(coroutines):
        cor_result = await cor
        print(f"DONE {cor_result}")
```

---
## asyncio.as_completed

Результаты возвращаются в порядке отрабатывания сопрограм, а не в порядке их запуска:

```python

In [27]: asyncio.run(main())
>>> start task 2 sleep 8.93
>>> start task 1 sleep 0.03
>>> start task 4 sleep 8.33
>>> start task 5 sleep 3.43
>>> start task 3 sleep 5.09
<<< end   task 1
DONE task 1
<<< end   task 5
DONE task 5
<<< end   task 3
DONE task 3
<<< end   task 4
DONE task 4
<<< end   task 2
DONE task 2
```
