# Генераторы

---
## Итератор и итерируемый объект

Итерируемый объект (iterable) - это объект, который способен возвращать элементы по одному.
Для Python это любой объект у которого есть метод ``__iter__`` или метод ``__getitem__``.
Если у объекта есть метод ``__iter__``, итерируемый объект превращается в итератор вызовом ``iter(name)``,
где name - имя итерируемого объекта. Если метода ``__iter__`` нет, Python перебирает элементы используя ``__getitem__``.


Итератор (iterator) - это объект, который возвращает свои элементы по одному за раз.
С точки зрения Python - это любой объект, у которого есть метод ``__next__``.
Этот метод возвращает следующий элемент, если он есть, или возвращает исключение StopIteration, когда элементы закончились.
Кроме того, итератор запоминает, на каком объекте он остановился в последнюю итерацию.
Также у каждого итератора присутствует метод ``__iter__`` - то есть, любой итератор является итерируемым объектом. Этот метод возвращает сам итератор.


---
## Пример создания итератор через класс

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
## Пример создания итератор через класс

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

Генератор - функция, которая позволяет легко создавать свои итераторы.
В отличии от обычных функций, генератор не просто возвращает значение и завершает работу, а возвращает итератор, который отдает элементы по одному.


Обычная функция завершает работу если:

* встретилось выражение return
* закончился код функции (это срабатывает как выражение ``return None``)
* возникло исключение


С точки зрения синтаксиса, генератор выглядит как обычная функция,
но, вместо return, используется оператор ``yield``.
Каждый раз, когда внутри функции встречается yield, генератор приостанавливается и возвращает значение.
При следующем запросе, генератор начинает работать с того же места, где он завершил работу в прошлый раз.
Так как yield не завершает работу генератора, он может использоваться несколько раз.


---
### Создание генератора

Функция-генератор - это функция, в которой присутствует ключевое слово yield.
При вызове, эта функция возвращает объект генератор. 

```python
import re

def filter_lines(filename, regex):
    with open(filename) as f:
        for line in f:
            if re.search(regex, line):
                yield line.rstrip()


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
## Итератор через класс и аналог как генератор

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

Генератор

```python
def my_range(start, stop):
    current = start
    while current < stop:
        yield current
        current += 1
```

---
## Итератор через класс и аналог как генератор

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

Генератор

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
    yield number+1
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
## Генератор как менеджер контекста

---
## Генератор как fixture


## yield from

