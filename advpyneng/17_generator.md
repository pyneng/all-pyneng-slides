# Генераторы

---
## Итератор и итерируемый объект

---
## Итерируемый объект

Итерируемый объект (iterable) - это объект, который способен возвращать элементы по одному.
Для Python это любой объект у которого есть метод ``__iter__`` (или с нюансами ``__getitem__``).


## Итератор

Итератор (iterator) - это объект, который возвращает свои элементы по одному за раз.
С точки зрения Python - это любой объект, у которого есть метод ``__next__``.
Этот метод возвращает следующий элемент, если он есть, или возвращает исключение StopIteration, когда элементы закончились.

У итератора должен быть метод ``__iter__`` - то есть, любой итератор является итерируемым объектом.
Этот метод возвращает сам итератор.

## Функция iter

Итерируемый объект превращается в итератор вызовом ``iter(name)``, где name - имя итерируемого объекта.

```python
iter(obj)
```

Функция ``iter`` возвращает итератор. Если у obj есть метод ``__iter__``, он вызывается чтобы получить итератор.
Если метода ``__iter__`` нет, функция iter вызывает метод ``__getitem__`` с индексами начиная с 0.


---
## Функция iter - sentinel

```python
import random

def commands():
    return random.choice(["sh clock", "sh ip int br", "ping 10.1.1.1", "sh version", "done"])


In [2]: commands_iter = iter(commands, "done")

In [3]: commands_iter
Out[3]: <callable_iterator at 0xb4602130>

In [4]: for c in commands_iter:
   ...:     print(c)
   ...:
sh version
sh clock
sh version
sh version
sh ip int br
```

```python
In [6]: f = open("config_r1.txt")

In [7]: i = iter(f.readline, "")

In [8]: for line in i:
   ...:     print(line.rstrip()
   ...:
Current configuration : 4052 bytes
!
! Last configuration change at 13:13:40 UTC Tue Mar 1 2016
version 15.2
no service timestamps debug uptime
no service timestamps log uptime
```

---
## Проверка isinstance с итерируемыми объектами

```python
from collections.abc import Iterable

class MyList:
    def __getitem__(self, index):
        print("__getitem__", index)


In [11]: l1 = MyList()
```

isinstance возвращает False

```python
In [12]: isinstance(l1, Iterable)
Out[12]: False
```

при этом iter (и как следствие перебор в цикле) будет работать
```python
In [13]: i = iter(l1)

In [14]: next(i)
__getitem__ 0

In [15]: next(i)
__getitem__ 1
```

---
## Пример создания итератора с помощью класса

```python
class MyRange:
    def __init__(self, start, stop):
        self.start = start
        self.stop = stop
        self._last = start

    def __next__(self):
        value = self._last
        if value == self.stop:
            raise StopIteration
        self._last += 1
        return value

    def __iter__(self):
        return self
```

```python
In [15]: r = MyRange(10, 15)

In [16]: next(r)
Out[16]: 10

In [17]: next(r)
Out[17]: 11

In [18]: next(r)
Out[18]: 12

In [19]: next(r)
Out[19]: 13

In [20]: next(r)
Out[20]: 14

In [21]: next(r)
---------------------------------------------------------------------------
StopIteration                             Traceback (most recent call last)
<ipython-input-21-8ebe59a56b1d> in <module>
----> 1 next(r)

<ipython-input-14-5ce87a9f92ea> in __next__(self)
      8         value = self._last
      9         if value == self.stop:
---> 10             raise StopIteration
     11         self._last += 1
     12         return value

StopIteration:
```

---
## Пример создания итератора с помощью класса

```python
class Cycle:
    def __init__(self, values):
        self.values = values
        self._index = 0

    def __next__(self):
        idx = self._index % len(self.values)
        result = self.values[idx]
        self._index += 1
        return result

    def __iter__(self):
        return self
```

---
## Генераторы

Функция-генератор - функция, которая позволяет легко создавать свои итераторы.
В отличии от обычных функций, функция-генератор не просто возвращает значение
и завершает работу, а возвращает объект генератор (итератор).
Функция-генератор это generator factory.


С точки зрения синтаксиса, функция-генератор выглядит как обычная функция,
но, вместо return, используется оператор ``yield``.
Каждый раз, когда внутри функции встречается yield, генератор приостанавливается и возвращает значение.
При следующем запросе, генератор начинает работать с того же места, где он завершил работу в прошлый раз.
Так как yield не завершает работу генератора, он может использоваться несколько раз.

---
## Python docs glossary

### [generator](https://docs.python.org/3/glossary.html#term-generator)

A function which returns a generator iterator. It looks like a normal function
except that it contains yield expressions for producing a series of values usable
in a for-loop or that can be retrieved one at a time with the next() function.

Usually refers to a generator function, but may refer to a generator iterator
in some contexts. In cases where the intended meaning isn’t clear, using the full terms avoids ambiguity.

### generator iterator

An object created by a generator function.

Each yield temporarily suspends processing, remembering the location execution
state (including local variables and pending try-statements). When the generator
iterator resumes, it picks up where it left off (in contrast to functions which start fresh on every invocation).

---
### Создание генератора с помощью функции генератора

Функция-генератор - это функция, в которой присутствует ключевое слово yield.
При вызове, эта функция возвращает объект генератор. 

```python
import re

def filter_lines(filename, regex):
    with open(filename) as f:
        for line in f:
            if re.search(regex, line):
                yield line.rstrip()


In [6]: filtered = filter_lines('config_r1.txt', '^interface'):

In [7]: for line in filtered:
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
## Создание итератора через класс и функцию-генератор

Класс MyRange возвращает объект, который является итератором:

```python
class MyRange:
    def __init__(self, start, stop):
        self.start = start
        self.stop = stop
        self._last = start

    def __next__(self):
        value = self._last
        if value == self.stop:
            raise StopIteration
        self._last += 1
        return value

    def __iter__(self):
        return self

range1 = MyRange(10, 16)
```

Функция-генератор my_range возвращает объект, который является итератором:

```python
def my_range(start, stop):
    current = start
    while current < stop:
        yield current
        current += 1

gen_range1 = my_range(10, 16)
```

---
## Создание итератора через класс и функцию-генератор

Класс Cycle возвращает объект, который является итератором:

```python
class Cycle:
    def __init__(self, values):
        self.values = values
        self._index = 0

    def __next__(self):
        idx = self._index % len(self.values)
        result = self.values[idx]
        self._index += 1
        return result

    def __iter__(self):
        return self
```

Функция-генератор my_cycle возвращает объект, который является итератором:

```python
def my_cycle(values):
    while True:
        for item in values:
            yield item
```

---
### Генератор это итератор

```python
def generate_nums(number):
    print('Start of generation')
    yield number
    print('Next number')
    yield number + 1
    print('The end')

In [3]: result = generate_nums(100)

In [4]: result
Out[4]: <generator object generate_nums at 0xb5788e9c>

In [5]: next(result)
Start of generation

Out[5]: 100
In [6]: next(result)
Next number

Out[6]: 101

In [7]: next(result)
The end
------------------------------------------------------------
StopIteration              Traceback (most recent call last)
<ipython-input-7-1b214ba10814> in <module>()
----> 1 next(result)

StopIteration:
```

---
## Примеры использования генераторов

```python
import csv

# "status","network","netmask","nexthop","metric","locprf","weight","path","origin"
# "*","1.0.0.0","24","200.219.145.45",NA,NA,0,"28135 18881 3549 15169","i"
# "*>","1.0.0.0","24","200.219.145.23",NA,NA,0,"53242 7738 15169","i"
# "*","1.0.4.0","24","200.219.145.45",NA,NA,0,"28135 18881 3549 1299 7545 56203","i"
# "*>","1.0.4.0","24","200.219.145.23",NA,NA,0,"53242 12956 174 7545 56203","i"


def read_csv(filename):
    with open(filename) as f:
        reader = csv.reader(f)
        for line in reader:
            yield line
```

---
## yield from

---
## yield from

При использовании в генераторе, yield from может помочь упростить использование yield в цикле for:

```python
In [1]: def generate():
   ...:     for i in range(5):
   ...:         yield i
   ...:

In [2]: list(generate())
Out[2]: [0, 1, 2, 3, 4]
```

Аналогичный вариант с yield from:

```python
In [3]: def generate():
   ...:     yield from range(5)
   ...:

In [4]: list(generate())
Out[4]: [0, 1, 2, 3, 4]
```

---
## yield from

```python
def chain_iterables(*iterables):
    for iterable in iterables:
        for item in iterable:
            yield item


In [6]: list(chain_iterables([1, 2, 3], "abc"))
Out[6]: [1, 2, 3, 'a', 'b', 'c']
```

Вариант с yield from

```python
def chain_iterables(*iterables):
    for iterable in iterables:
        yield from iterable


In [8]: list(chain_iterables([1, 2, 3], "abc"))
Out[8]: [1, 2, 3, 'a', 'b', 'c']
```

---
## generator expression (генераторное выражение)


---
## generator expression (генераторное выражение)

Генераторное выражение использует такой же синтаксис, как list comprehensions, но возвращает итератор, а не список.

```python
In [1]: genexpr = (x**2 for x in range(10000))

In [2]: genexpr
Out[2]: <generator object <genexpr> at 0xb571ec8c>

In [3]: next(genexpr)
Out[3]: 0

In [4]: next(genexpr)
Out[4]: 1

In [5]: next(genexpr)
Out[5]: 4
```

---
## Генератор как менеджер контекста

```python
import time


class Timed:
    def __enter__(self):
        self.start = time.time()

    def __exit__(self, exc_type, exc_value, traceback):
        end = time.time()
        print("время выполнения", end - self.start)


In [4]: with Timed():
   ...:     time.sleep(2)
   ...:
время выполнения 2.0024032592773438
```

```python
from contextlib import contextmanager

@contextmanager
def gen_timed():
    start = time.time()
    yield
    end = time.time()
    print("время выполнения", end - start)


In [7]: with gen_timed():
   ...:     time.sleep(2)
   ...:
время выполнения 2.003124713897705
```

---
## Генератор как fixture

```python
@pytest.fixture(scope='module')
def ssh_test_connection(first_router_from_devices_yaml):
    ssh = ConnectHandler(**first_router_from_devices_yaml)
    ssh.enable()
    yield ssh
    ssh.disconnect()
```

```python
@pytest.fixture(scope='module')
def ssh_test_connection(first_router_from_devices_yaml):
    with ConnectHandler(**first_router_from_devices_yaml) as ssh:
        ssh.enable()
        yield ssh
```
