## Функции

---
## Аргументы переменной длины

---
### Позиционные аргументы переменной длины

Параметр, который принимает позиционные аргументы переменной длины, создается добавлением перед именем параметра звездочки.
Имя параметра может быть любым, но, по договоренности, чаще всего, используют имя ```*args```

```python
def sum_arg(a, *args):
    print(a, args)
    return a + sum(args)

```

```python
In [2]: sum_arg(1, 10, 20, 30)
1 (10, 20, 30)
Out[2]: 61

In [3]: sum_arg(1, 10)
1 (10,)
Out[3]: 11

In [4]: sum_arg(1)
1 ()
Out[4]: 1
```

---
### Позиционные аргументы переменной длины

```python
def sum_arg(*args):
    print(args)
    return sum(args)

In [6]: sum_arg(1, 10, 20, 30)
(1, 10, 20, 30)
Out[6]: 61

In [7]: sum_arg()
()
Out[7]: 0
```

---
### Ключевые аргументы переменной длины

Параметр, который принимает ключевые аргументы переменной длины, создается добавлением перед именем параметра двух звездочек.
Имя параметра может быть любым, но, по договоренности, чаще всего, используют имя ```**kwargs``` (от keyword arguments).


```python
def sum_arg(a, **kwargs):
    print(a, kwargs)
    return a + sum(kwargs.values())

```

```python
In [9]: sum_arg(a=10, b=10, c=20, d=30)
10 {'c': 20, 'b': 10, 'd': 30}
Out[9]: 70

In [10]: sum_arg(b=10, c=20, d=30, a=10)
10 {'c': 20, 'b': 10, 'd': 30}
Out[10]: 70
```

---
### Ключевые аргументы переменной длины

Нельзя указывать позиционный аргумент после ключевого:
```python
In [11]: sum_arg(10, b=10, c=20, d=30)
10 {'c': 20, 'b': 10, 'd': 30}
Out[11]: 70

In [12]: sum_arg(b=10, c=20, d=30, 10)
  File "<ipython-input-14-71c121dc2cf7>", line 1
    sum_arg(b=10,c=20,d=30,10)
                          ^
SyntaxError: positional argument follows keyword argument
```

---
## Распаковка аргументов

---
### Распаковка позиционных аргументов

```python
In [4]: items = [1, 2, 3]

In [5]: print('One: {}, Two: {}, Three: {}'.format(*items))
One: 1, Two: 2, Three: 3
```

---
### Распаковка позиционных аргументов

Функция config_interface (файл func_args_unpacking.py): 
```python
def config_interface(intf_name, ip_address, cidr_mask):
    ip_addr = 'ip address {} {}'

    mask_bits = int(cidr_mask.split('/')[-1])
    bin_mask = '1' * mask_bits + '0' * (32 - mask_bits)
    dec_mask = [str(int(bin_mask[i:i+8], 2)) for i in range(0, 25, 8)]
    mask = '.'.join(dec_mask)

    intf_cfg = [
        f"interface {intf_name}", "no shutdown", f"ip address {ip_address} {mask}"
    ]
    return intf_cfg

```

---
### Распаковка позиционных аргументов

```python
In [1]: config_interface('Fa0/1', '10.0.1.1', '/25')
Out[1]: ['interface Fa0/1', 'no shutdown', 'ip address 10.0.1.1 255.255.255.128']
```

```python
interfaces_info = [['Fa0/1', '10.0.1.1', '/24'],
                   ['Fa0/2', '10.0.2.1', '/24'],
                   ['Fa0/3', '10.0.3.1', '/24'],
                   ['Fa0/4', '10.0.4.1', '/24'],
                   ['Lo0', '10.0.0.1', '/32']]

In [8]: for info in interfaces_info:
  ....:     print(config_interface(*info))
  ....:
['interface Fa0/1', 'no shutdown', 'ip address 10.0.1.1 255.255.255.0']
['interface Fa0/2', 'no shutdown', 'ip address 10.0.2.1 255.255.255.0']
['interface Fa0/3', 'no shutdown', 'ip address 10.0.3.1 255.255.255.0']
['interface Fa0/4', 'no shutdown', 'ip address 10.0.4.1 255.255.255.0']
['interface Lo0', 'no shutdown', 'ip address 10.0.0.1 255.255.255.255']
```

---
### Распаковка ключевых аргументов

---
### Распаковка ключевых аргументов

Аналогичным образом можно распаковывать словарь, чтобы передать его как ключевые аргументы.

Функция config_to_list (файл func_args_unpacking.py):
```python
def config_to_list(cfg_file, delete_excl=True,
                   delete_empty=True, strip_end=True):
    result = []
    with open(cfg_file) as f:
        for line in f:
            if strip_end:
                line = line.rstrip()
            if delete_empty and not line:
                pass
            elif delete_excl and line.startswith('!'):
                pass
            else:
                result.append(line)
    return result
```

---
### Распаковка ключевых аргументов

Пример использования:
```python
In [9]: config_to_list('r1.txt')
Out[9]:
['service timestamps debug datetime msec localtime show-timezone year',
 'service timestamps log datetime msec localtime show-timezone year',
 'service password-encryption',
 'service sequence-numbers',
 'no ip domain lookup',
 'ip ssh version 2']
```

---
### Распаковка ключевых аргументов

Список словарей ```cfg```, в которых указано имя файла и все аргументы:
```python
cfg = [dict(cfg_file='r1.txt', delete_excl=True, delete_empty=True, strip_end=True),
       dict(cfg_file='r2.txt', delete_excl=False, delete_empty=True, strip_end=True),
       dict(cfg_file='r3.txt', delete_excl=True, delete_empty=False, strip_end=True),
       dict(cfg_file='r4.txt', delete_excl=True, delete_empty=True, strip_end=False)]

In [12]: for d in cfg:
    ...:     print(config_to_list(**d))
    ...:
['service timestamps debug datetime msec localtime show-timezone year', 'service timestamps log datetime msec localtime show-timezone year', 'service password-encryption', 'service sequence-numbers', 'no ip domain lookup', 'ip ssh version 2']
['!', 'service timestamps debug datetime msec localtime show-timezone year', 'service timestamps log datetime msec localtime show-timezone year', 'service password-encryption', 'service sequence-numbers', '!', 'no ip domain lookup', '!', 'ip ssh version 2', '!']
['service timestamps debug datetime msec localtime show-timezone year', 'service timestamps log datetime msec localtime show-timezone year', 'service password-encryption', 'service sequence-numbers', '', '', '', 'ip ssh version 2', '']
['service timestamps debug datetime msec localtime show-timezone year\n', 'service timestamps log datetime msec localtime show-timezone year\n', 'service password-encryption\n', 'service sequence-numbers\n', 'no ip domain lookup\n', 'ip ssh version 2\n']
```

---
### Пример использования ключевых аргументов переменной длины и распаковки аргументов

```python
def config_to_list(
    cfg_file, delete_excl=True, delete_empty=True, strip_end=True
):
    result = []
    with open(cfg_file) as f:
        for line in f:
            if strip_end:
                line = line.rstrip()
            if delete_empty and not line:
                continue
            if delete_excl and line.startswith('!'):
                continue
            result.append(line)
    return result


def clear_cfg_and_write_to_file(cfg, to_file, **kwargs):
    cfg_as_list = config_to_list(cfg, **kwargs)
    with open(to_file, 'w') as f:
        f.write('\n'.join(cfg_as_list))
```


---
## звездочка

```python
a, *rest = [1, 2, 3, 4, 5]

def func1(*args, **kwargs):
    result = func2(*args, **kwargs)
    return result

items = [100, 200, *rest]
dict1 = {1: 100, 2: 200}
dict2 = {**dict1, 3: 300}

template.format(*items)
```


---
### Функции - объекты первого класса (first-class object)

В Python все функции являются объектами первого класса. Это означает, что Python поддерживает:

* создание функций в runtime
* передачу функций в качестве аргументов другим функциям
* возвращение функции как результата других функций
* присваивание функций переменным, сохранение функций в структурах данных

---
### [Функции высшего порядка](https://ru.wikipedia.org/wiki/%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D1%8F_%D0%B2%D1%8B%D1%81%D1%88%D0%B5%D0%B3%D0%BE_%D0%BF%D0%BE%D1%80%D1%8F%D0%B4%D0%BA%D0%B0)

Функция, принимающая в качестве аргументов другие функции или возвращающая
другую функцию в качестве результата.

---
### Передача функций в качестве аргументов другим функциям

```python
In [1]: list(map(str, [1, 2, 3]))
Out[1]: ['1', '2', '3']
```

---
### Передача функций в качестве аргументов другим функциям
Функция delay ожидает как аргумент задержку в секундах, другую функцию и ее аргументы:

```python
import time

def delay(seconds, func, *args, **kwargs):
    print(f'Delay {seconds} seconds...')
    time.sleep(seconds)
    return func(*args, **kwargs)
```

Вызов
```python
In [4]: def summ(a, b):
   ...:     return a + b
   ...:

In [5]: delay(5, summ, 1, 4)
Delay 5 seconds...
Out[5]: 5
```

---
### Сохранение функций в структурах данных

```python
In [8]: functions = [delay, summ]

In [9]: functions
Out[9]:
[<function __main__.delay(seconds, func, *args, **kwargs)>,
 <function __main__.summ(a, b)>]
```

---
### Присваивание функций переменным

```python
In [10]: delay_execution = delay

In [11]: delay_execution
Out[11]: <function __main__.delay(seconds, func, *args, **kwargs)>

In [12]: delay_execution(5, summ, 1, 4)
    Delay 5 seconds...
Out[12]: 5
```

---
## Замыкание (Closure)

Замыкание (closure) — функция, которая находится внутри другой функции
и ссылается на переменные объявленные в теле внешней функции (свободные переменные).

Внутренняя функция создается каждый раз во время выполнения внешней.
Каждый раз при вызове внешней функции происходит создание нового 
экземпляра внутренней функции, с новыми ссылками на переменные внешней функции.

Ссылки на переменные внешней функции действительны внутри 
вложенной функции до тех пор, пока работает вложенная функция, даже если внешняя 
функция закончила работу, и переменные вышли из области видимости.

---
### Замыкание (Closure)

```python
def multiply(num1):
    var = 10
    def inner(num2):
        return num1 * num2
    return inner
```

Использование созданной функции:

```python
In [2]: mult_by_9 = multiply(9)
```

---
### Замыкание (Closure)

Переменная mult_by_9 ссылается на внутреннюю функцию inner и при этом внутренняя функция
помнит значение num1 = 9 и поэтому все числа будут умножаться на 9:

```python
In [3]: mult_by_9
Out[3]: <function __main__.multiply.<locals>.inner(num2)>

In [4]: mult_by_9.__closure__
Out[4]: (<cell at 0xb0bd5f2c: int object at 0x836bf60>,)

In [5]: mult_by_9.__closure__[0].cell_contents
Out[5]: 9

In [8]: mult_by_9(10)
Out[8]: 90

In [9]: mult_by_9(2)
Out[9]: 18
```

