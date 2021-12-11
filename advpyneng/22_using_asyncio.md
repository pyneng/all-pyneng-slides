## Использование asyncio

---
## Использование asyncio

* Цикл событий
* Отмена задач
* Работа с таймаутами

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

    loop = asyncio.get_event_loop()
    result = loop.run_until_complete(coro)
    loop.close()
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

> Deprecated since version 3.10: ``asyncio.get_event_loop()``

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
## Task

```python
class asyncio.Task(coro, *, loop=None, name=None)
```

To cancel a running Task use the cancel() method. Calling it will cause the Task
to throw a CancelledError exception into the wrapped coroutine. If a coroutine is
awaiting on a Future object during cancellation, the Future object will be cancelled.

cancelled() can be used to check if the Task was cancelled. The method returns True if
the wrapped coroutine did not suppress the CancelledError exception and was actually cancelled.

---
### shield

```python
awaitable asyncio.shield(aw)
```

> except that if the coroutine containing it is cancelled, the Task running in
> something() is not cancelled. From the point of view of something(), the cancellation
> did not happen. Although its caller is still cancelled, so the “await” expression still raises a CancelledError.


---
### gather

```python
awaitable asyncio.gather(*aws, return_exceptions=False)
```

If gather() is cancelled, all submitted awaitables (that have not completed yet) are also cancelled.

If any Task or Future from the aws sequence is cancelled, it is treated as if it
raised CancelledError – the gather() call is not cancelled in this case. This is
to prevent the cancellation of one submitted Task/Future to cause other Tasks/Futures to be cancelled.


---
## Работа с таймаутами

---
## Работа с таймаутами

* ``asyncio.wait_for``
* ``async.wait``
* ``asyncio.as_completed``
* [``async-timeout``](https://github.com/aio-libs/async-timeout)

---
## asyncio.wait_for

```python
coroutine asyncio.wait_for(aw, timeout)
```

* If a timeout occurs, it cancels the task and raises asyncio.TimeoutError.
* To avoid the task cancellation, wrap it in shield().
* The function will wait until the future is actually cancelled, so the total wait time may exceed the timeout.
* If an exception happens during cancellation, it is propagated.
* If the wait_for is cancelled, the future aw is also cancelled.


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

---
## asyncio.as_completed


```python
asyncio.as_completed(aws, *, timeout=None)
```

Raises asyncio.TimeoutError if the timeout occurs before all Futures are done.


