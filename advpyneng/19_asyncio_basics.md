## Основы asyncio


---
## Основы asyncio

В Python есть три основных способа запускать задачи "параллельно":

* процессы
* потоки
* асинхронное программирование

> Полноценно параллельная работа будет только при использовании процессов,
> по всех остальных случая речь идет о попеременном выполнении (concurrent).

---
## Основы asyncio

Модули, которые позволяют запустить задачи:

* в процессах: multiprocessing, concurrent.futures
* в потоках: threading, concurrent.futures
* асинхронно: asyncio и масса других вариантов и сторонних фреймворков

---
## Основы asyncio

Часто можно встретить фразу, что асинхронный код проще чем многопоточный. Тут имеется
в виду, что в асинхронном коде очень четко видно те места, где будет переключение, а
весь остальной код будет выполняться последовательно без прерывания. В то время как
многопоточный код может быть прерван в любом месте и часто это приводит к проблемам.

При этом разобраться с тем как работает asyncio совсем не просто. К тому же, в большинстве
случаев не получится использовать уже известные модули, например, netmiko, а вместо
них надо будет использовать асинхронный аналог.

---
## Основы asyncio

Самые большие преимущества при использовании asyncio можно получить, например,
в веб. Тут подключений действительно может быть тысячи и с asynio не надо будет
создавать поток для каждого подключения. Однако для работы с сетевым оборудованием
asyncio тоже может быть полезен и во многих случаях, особенно когда подключений много,
работать быстрее многопоточного варианта.

При этом конечно надо учитывать, что почти все модули, которые работали в многопоточном
варианте, не будут работать с asyncio, так как они будут блокировать работу и не будут
отдавать управление циклу событий.


---
## Основы asyncio

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
## Блокирующий/неблокирующий вызов

Блокирующий вызов останавливает работу программы, пока не будет получен результат вызова.
Например, ``subprocess.run`` это блокирующий вызов и если вызвать, к примеру,
ping какого-то адреса, то строка с subprocess.run будет блокировать выполнение
скрипта пока не отработает ping.

Неблокирующий вызов возвращает какой-то объект сразу и, как правило,
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

* функцию сопрограмму - функция, которая создается с помощью ``async def``
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
asyncio.create_task(coro)
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
## Примеры использования await и create_task

Что именно использовать await или create_task для запуска сопрограмм, зависит от
ситуации. Некоторые действия внутри функции должны выполняться последовательно,
некоторые могут выполняться параллельно.

```python
async def get_command_output(device_ip, show_command):
    print(f">>> Start get_command_output {device_ip}")
    await asyncio.sleep(1)
    print(f"<<< End  get_command_output  {device_ip}")


async def parse_command_output(output):
    print(">>> Start parse_command_output")
    await asyncio.sleep(1)
    print("<<< End parse_command_output")
```

Внутри функции, которая должна получить вывод команды и распарсить его,
эти функции надо вызывать последовательно, не параллельно, поэтому используется await:

```python
async def get_and_parse_show_command(device, command):
    print(f"### Start get_and_parse_show_command {device}")
    output = await get_command_output(device, command)
    parsed_data = await parse_command_output(output)
    print(f"### End   get_and_parse_show_command {device}")
```

---
## Примеры использования await и create_task

При этом саму функцию get_and_parse_show_command можно запускать параллельно
для разных устройств, поэтому используем create_task.

```python
async def main():
    print(f'Start {datetime.now()}')
    tasks = [asyncio.create_task(get_and_parse_show_command(ip, "sh ip int br"))
             for ip in ["10.1.1.1", "10.2.2.2", "10.3.3.3"]]
    results = [await t for t in tasks]
    print(f'End {datetime.now()}')

In [15]: asyncio.run(main())
Start 2021-03-16 10:29:24.280408
### Start get_and_parse_show_command 10.1.1.1
>>> Start get_command_output 10.1.1.1
### Start get_and_parse_show_command 10.2.2.2
>>> Start get_command_output 10.2.2.2
### Start get_and_parse_show_command 10.3.3.3
>>> Start get_command_output 10.3.3.3
<<< End  get_command_output  10.1.1.1
>>> Start parse_command_output
<<< End  get_command_output  10.2.2.2
>>> Start parse_command_output
<<< End  get_command_output  10.3.3.3
>>> Start parse_command_output
<<< End parse_command_output
### End   get_and_parse_show_command 10.1.1.1
<<< End parse_command_output
### End   get_and_parse_show_command 10.2.2.2
<<< End parse_command_output
### End   get_and_parse_show_command 10.3.3.3
End 2021-03-16 10:29:26.286659
```

---
## Примеры использования await и create_task

Еще один пример работы с await и create_task, но теперь функции выполняют много действий и
после каждого выполняется asyncio.sleep:

```python
async def print_number(task_name):
    print(f">>> Start {task_name}")
    for _ in range(10):
        print(42)
        await asyncio.sleep(0.5)
    print(f"<<< End   {task_name}")


async def print_text(task_name):
    print(f">>> Start {task_name}")
    for _ in range(10):
        print("hello")
        await asyncio.sleep(0.9)
    print(f"<<< End   {task_name}")


async def main():
    print(f'Start {datetime.now()}')
    task1 = asyncio.create_task(print_number('task1'))
    task2 = asyncio.create_task(print_text('task2'))

    await task1
    await task2
    print(f'End   {datetime.now()}')


In [11]: asyncio.run(main())
Start 2021-03-16 15:35:31.668084
>>> Start task1
42
>>> Start task2
hello
42
hello
42
42
hello
42
42
hello
42
42
hello
42
hello
42
<<< End   task1
hello
hello
hello
hello
<<< End   task2
End   2021-03-16 15:35:40.684632
```

---
## Примеры использования await и create_task

Также очень важно понимать, что сопрограммы не являются асинхронными сами по себе
и если не отдавать в сопрограмме управление, то она будет блокирующей и ничего не будет
выполняться, пока она не выполнится до конца.
Например, если изменить asyncio.sleep на time.sleep в функции print_number,
то сначала выведутся print из print_number и только потом print_text:

```python
import time

async def print_number(task_name):
    print(f">>> Start {task_name}")
    for _ in range(10):
        print(42)
        time.sleep(0.2)
    print(f"<<< End   {task_name}")


In [16]: asyncio.run(main())
Start 2021-03-16 15:43:31.593333
>>> Start task1
42
42
42
42
42
42
42
42
42
42
<<< End   task1
>>> Start task2
hello
hello
hello
hello
hello
hello
hello
hello
hello
hello
<<< End   task2
End   2021-03-16 15:43:42.614749
```

