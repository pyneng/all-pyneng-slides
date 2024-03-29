## asyncio

---
## Терминология
## Coroutine (сопрограмма)

Как и с генераторами, различают:

* функцию сопрограмму - функция, которая создается с помощью ``async def`` (асинхронная функция)
* объект сопрограмму - объект, который возвращается при вызове функции сопрограммы

Функции-сопрограммы возвращают объекты сопрограммы, которые запускаются 
менеджером (циклом событий). Сопрограмма может периодически прерывать выполнение
и отдавать управление менеджеру, но при этом она не теряет состояние.

Создать сопрограмму недостаточно для того чтобы она запускалась
параллельно с другими сопрограммами - для управления сопрограммами нужен
менеджер - event loop. Также по умолчанию в сопрограмме код выполняется последовательно
и надо явно указывать в каких местах можно переключаться - await.

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
## [High-level API Index](https://docs.python.org/3/library/asyncio-api-index.html)
### Tasks

|           |                                                     |
|-----------|-----------------------------------------------------|
| ``run()`` | Create event loop, run a coroutine, close the loop. |
| ``Task`` | Task object. |
| ``create_task()`` | Start an asyncio Task. |
| ``current_task()`` | Return the current Task. |
| ``all_tasks()`` | Return all tasks for an event loop. |
| ``await sleep()`` | Sleep for a number of seconds. |
| ``await gather()`` | Schedule and wait for things concurrently. |
| ``await wait_for()`` | Run with a timeout. |
| ``await shield()`` | Shield from cancellation. |
| ``await wait()`` | Monitor for completion. |
| ``to_thread()`` | Asynchronously run a function in a separate OS thread. |
| ``run_coroutine_threadsafe()`` | Schedule a coroutine from another OS thread. |
| ``for in as_completed()`` | Monitor for completion with a for loop. |

New in Python 3.11

|           |                                                     |
|-----------|-----------------------------------------------------|
| ``Runner`` | A context manager that simplifies multiple async function calls. |
| ``TaskGroup()`` | A context manager that holds a group of tasks. |
| ``timeout()`` | Run with a timeout. Useful in cases when wait_for is not suitable. |

---
## Использование asyncio

* Цикл событий
* Отмена задач
* Работа с таймаутами

---
### asyncio.Task

```python

In [5]: inspect(asyncio.Task, methods=True)
╭──────────────────────────────────────── <class '_asyncio.Task'> ────────────────────────────────────────╮
│ class Task(coro, *, loop=None, name=None, context=None):                                                │
│                                                                                                         │
│ A coroutine wrapped in a Future.                                                                        │
│                                                                                                         │
│    add_done_callback = def add_done_callback(...) Add a callback to be run when the future becomes      │
│                        done.                                                                            │
│               cancel = def cancel(self, /, msg=None): Request that this task cancel itself.             │
│            cancelled = def cancelled(self, /): Return True if the future was cancelled.                 │
│           cancelling = def cancelling(self, /): Return the count of the task's cancellation requests.   │
│                 done = def done(self, /): Return True if the future is done.                            │
│            exception = def exception(self, /): Return the exception that was set on this future.        │
│             get_coro = def get_coro(self, /):                                                           │
│             get_loop = def get_loop(self, /): Return the event loop the Future is bound to.             │
│             get_name = def get_name(self, /):                                                           │
│            get_stack = def get_stack(self, /, *, limit=None): Return the list of stack frames for this  │
│                        task's coroutine.                                                                │
│          print_stack = def print_stack(self, /, *, limit=None, file=None): Print the stack or traceback │
│                        for this task's coroutine.                                                       │
│ remove_done_callback = def remove_done_callback(self, fn, /): Remove all instances of a callback from   │
│                        the "call when done" list.                                                       │
│               result = def result(self, /): Return the result this future represents.                   │
│        set_exception = def set_exception(self, exception, /): Mark the future done and set an           │
│                        exception.                                                                       │
│             set_name = def set_name(self, value, /):                                                    │
│           set_result = def set_result(self, result, /): Mark the future done and set its result.        │
│             uncancel = def uncancel(self, /): Decrement the task's count of cancellation requests.      │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

---
### asyncio.Task

```python

In [5]: inspect(asyncio.Task, methods=True)
╭──────────────────────────────────────── <class '_asyncio.Task'> ────────────────────────────────────────╮
│ class Task(coro, *, loop=None, name=None, context=None):                                                │
│                                                                                                         │
│ A coroutine wrapped in a Future.                                                                        │
│                                                                                                         │
│                 done = def done(self, /): Return True if the future is done.                            │
│            exception = def exception(self, /): Return the exception that was set on this future.        │
│               result = def result(self, /): Return the result this future represents.                   │
│               cancel = def cancel(self, /, msg=None): Request that this task cancel itself.             │
│            cancelled = def cancelled(self, /): Return True if the future was cancelled.                 │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

---
## Event Loop

---
### asyncio.run

```python
async def send_show(device, command):
    async with AsyncScrapli(**device) as conn:
        reply = await conn.send_command(command)
        parsed = reply.textfsm_parse_output()
    return parsed


async def run_all(devices, command):
    coro = [send_show(dev, command) for dev in devices]
    result = await asyncio.gather(*coro, return_exceptions=True)
    return result


if __name__ == "__main__":
    output = asyncio.run(run_all(devices, "show ip int br"))
    pprint(output)
```

---
### [asyncio.run](https://github.com/python/cpython/blob/3.10/Lib/asyncio/runners.py)

```python
def run(main, *, debug=None):
    if events._get_running_loop() is not None:
        raise RuntimeError(
            "asyncio.run() cannot be called from a running event loop")

    if not coroutines.iscoroutine(main):
        raise ValueError("a coroutine was expected, got {!r}".format(main))

    loop = events.new_event_loop()
    try:
        events.set_event_loop(loop)
        if debug is not None:
            loop.set_debug(debug)
        return loop.run_until_complete(main)
    finally:
        try:
            _cancel_all_tasks(loop)
            loop.run_until_complete(loop.shutdown_asyncgens())
            loop.run_until_complete(loop.shutdown_default_executor())
        finally:
            events.set_event_loop(None)
            loop.close()
```

---
### asyncio.run

```python
def run(main, *, debug=None):
    loop = events.new_event_loop()
    try:
        events.set_event_loop(loop)
        return loop.run_until_complete(main)
    finally:
        try:
            _cancel_all_tasks(loop)
            loop.run_until_complete(loop.shutdown_asyncgens())
            loop.run_until_complete(loop.shutdown_default_executor())
        finally:
            events.set_event_loop(None)
            loop.close()


def _cancel_all_tasks(loop):
    to_cancel = tasks.all_tasks(loop)
    if not to_cancel:
        return

    for task in to_cancel:
        task.cancel()

    loop.run_until_complete(tasks.gather(*to_cancel, return_exceptions=True))

    for task in to_cancel:
        if task.cancelled():
            continue
        if task.exception() is not None:
            loop.call_exception_handler({
                'message': 'unhandled exception during asyncio.run() shutdown',
                'exception': task.exception(),
                'task': task,
            })
```

---
## Работа с циклом событий


---
## Работа с циклом событий

```python
async def send_show(device, command):
    try:
        async with AsyncScrapli(**device) as conn:
            reply = await conn.send_command(command)
        return reply.result
    except ScrapliException as error:
        print(error)


async def run_all(devices, command):
    coro = [send_show(dev, command) for dev in devices]
    result = await asyncio.gather(*coro, return_exceptions=True)
    return result


if __name__ == "__main__":
    coro = run_all(devices, "sh clock")

    loop = asyncio.new_event_loop()
    result = loop.run_until_complete(coro)
    loop.close()
```

---
## Работа с циклом событий

```python
async def main(devices):
    loop = asyncio.get_running_loop()
    with ThreadPoolExecutor(max_workers=10) as executor:
        coroutines = [
            loop.run_in_executor(executor, netmiko_connect, dev) for dev in devices
        ]
        results = await asyncio.gather(*coroutines)
    return results


if __name__ == "__main__":
    output = asyncio.run(main(devices))
    pprint(output)
```

---
## Работа с циклом событий

Когда может понадобится:

* старая версия Python: нет какой-то высокоуровневой функции, например, ``asyncio.to_thread`` или asyncio.run
* нужен контроль executor
* нужно более тонко управлять отменой задач/прерыванием работы скрипта
* нужны низкоуровневые методы

---
### Работа с циклом событий
### Получение event loop

* ``asyncio.get_running_loop()`` - функция для получения текущего цикла событий
* ``asyncio.set_event_loop()`` - установить цикл событий как текущий
* ``asyncio.new_event_loop()`` - создать новый цикл событий


---
## Работа с циклом событий

* ``loop.run_until_complete``
* ``loop.run_forever``
* ``loop.stop``
* ``loop.close``
* ``loop.is_running()``
* ``loop.is_closed()``
* ``loop.shutdown_asyncgens``


---
## Работа с циклом событий

Thread/Process Pool:

* ``await loop.run_in_executor()``
* ``loop.set_default_executor()``

Tasks/Futures:

* ``loop.create_future()``
* ``loop.create_task()``
* ``loop.set_task_factory()``
* ``loop.get_task_factory()``

---
## Отмена задач


---
## Отмена задач

Зачем

* остановить бесконечную задачу (спиннер, сервер, опрос чего-то)
* позволяет управлять тем, что случается в случае прерывания скрипта или какой-то задачи

---
## Отмена задач

---
### shield

```python
awaitable asyncio.shield(aw)
```

---
### Запуск сопрограмм и отмена задач

---
### Запуск сопрограмм и отмена задач

* ``gather``
* ``as_completed``
* ``wait``
* ``wait_for``
* ``timeout`` (для Python < 3.11 [``async-timeout``](https://github.com/aio-libs/async-timeout))
* ``TaskGroup`` (New in Python 3.11)

---
### gather

```python
awaitable asyncio.gather(*aws, return_exceptions=False)
```

Если return_exceptions=False (по умолчанию), первое сгенерированное исключение
немедленно распространяется на задачу ``await gather``. Другие awaitables
объекты в последовательности aws не будут отменены и продолжат выполняться.

Если return_exceptions=True, исключения обрабатываются так же, как и успешные
результаты, и объединяются в списке результатов.

Если gather отменяется, все awaitables (которые еще не завершены) также отменяются.

Если какая-либо Task/Future из последовательности aws отменяется, она
обрабатывается так, как будто вызвала CancelledError — в этом случае вызов
gather не отменяется. Это сделано для того, чтобы отмена одной отправленной
Task/Future не привела к отмене других Task/Future.

```python
async def send_command_to_devices(devices, command):
    coroutines = [send_show(device, command) for device in devices]
    result = await asyncio.gather(*coroutines)
    return result
```

---
## asyncio.as_completed


```python
asyncio.as_completed(aws, *, timeout=None)
```

* если случился timeout, задачи не отменяются
* если какая-то задача отработала с исключением, остальные не отменяются

```python
async def send_command_to_devices(devices, command):
    coroutines = [connect_ssh(dev, command) for dev in devices]
    results = []
    for coro in asyncio.as_completed(coroutines):
        res = await coro
        results.append(res)
    return results
```

---
## asyncio.wait

```python
coroutine asyncio.wait(aws, *, timeout=None, return_when=ALL_COMPLETED)

done, pending = await asyncio.wait(aws)
```

* wait НЕ гененирует asyncio.TimeoutError
* wait НЕ отменяет задачи, если случился таймаут

> Deprecated since version 3.8, will be removed in version 3.11: Passing coroutine objects to wait() directly is deprecated.


* FIRST_COMPLETED The function will return when any future finishes or is cancelled.
* FIRST_EXCEPTION The function will return when any future finishes by raising an exception. If no future raises an exception then it is equivalent to ALL_COMPLETED.
* ALL_COMPLETED The function will return when all futures finish or are cancelled.

```python
async def run_all(devices, command):
    tasks = [asyncio.create_task(send_show(dev, command)) for dev in devices]
    done_set, pending_set = await asyncio.wait(
        tasks, timeout=5, return_when=asyncio.ALL_COMPLETED
    )
    [t.cancel() for t in pending_set]
    results = [await t for t in tasks if t not in pending_set]
    return results
```


---
## asyncio.wait_for

```python
coroutine asyncio.wait_for(aw, timeout)
```

* если происходит timeout, wait_for отменяет задачу и генерирует TimeoutError
* чтобы избежать отмены задачи, оберните ее в shield
* wait_for будет ждать до тех пор, пока задача не будет фактически отменена, поэтому общее время ожидания может превысить timeout
* если wait_for отменяется, awaitable также отменяется

---
## asyncio.timeout

```python
coroutine asyncio.timeout(delay)
```


```python
async def main():
    async with asyncio.timeout(10):
        await long_running_task()
```
---
## asyncio.timeout

Если для выполнения long_running_task требуется более 10 секунд, context manager отменит текущую задачу и обработает полученную ошибку
asyncio.CancelledError, преобразуя ее в ошибку asyncio.TimeoutError, которую
можно перехватить и обработать (только за пределами with).

```python
async def main():
    try:
        async with asyncio.timeout(10):
            await long_running_task()
    except TimeoutError:
        print("The long operation timed out, but we've handled it.")

    print("This statement will run regardless.")
```

---
## asyncio.TaskGroup

```python
async def main():
    async with asyncio.TaskGroup() as group:
        task1 = group.create_task(some_coro(...))
        task2 = group.create_task(another_coro(...))
    print("Both tasks have completed now.")
```

При первом исключении, отличным от asyncio.CancelledError, оставшиеся задачи в группе отменяются.

---
## asyncio.TaskGroup
```python
async def get_show_from_devices(devices, command):
    async with asyncio.TaskGroup() as group:
        tasks = [group.create_task(send_show(dev, command)) for dev in devices]
    results = [t.result() for t in tasks]
    return results
```

