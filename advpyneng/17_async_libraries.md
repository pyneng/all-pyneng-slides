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

Запустить сопрограмму можно несколькими способами:

* asyncio.run
* await
* asyncio.create_task

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
## Запуск нескольких awaitables

* asyncio.gather
* asyncio.as_completed
* asyncio.wait

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

---
## scrapli

---
## scrapli

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
