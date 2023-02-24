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

* передачу функций в качестве аргументов другим функциям
* возвращение функции как результата других функций
* присваивание функций переменным
* сохранение функций в структурах данных

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
## Полезные встроенные функции


---
### callable

```python
In [1]: callable?
Signature: callable(obj, /)
Docstring:
Return whether the object is callable (i.e., some kind of function).

Note that classes are callable, as are instances of classes with a
__call__() method.
Type:      builtin_function_or_method
```


---
### isinstance

```python
In [2]: isinstance?
Signature: isinstance(obj, class_or_tuple, /)
Docstring:
Return whether an object is an instance of a class or of a subclass thereof.

A tuple, as in ``isinstance(x, (A, B, ...))``, may be given as the target to
check against. This is equivalent to ``isinstance(x, A) or isinstance(x, B)
or ...`` etc.
Type:      builtin_function_or_method
```


---
### issubclass

```python
In [3]: issubclass?
Signature: issubclass(cls, class_or_tuple, /)
Docstring:
Return whether 'cls' is derived from another class or is the same class.

A tuple, as in ``issubclass(x, (A, B, ...))``, may be given as the target to
check against. This is equivalent to ``issubclass(x, A) or issubclass(x, B)
or ...``.
Type:      builtin_function_or_method
```


---
### hasattr

```python
In [4]: hasattr?
Signature: hasattr(obj, name, /)
Docstring:
Return whether the object has an attribute with the given name.

This is done by calling getattr(obj, name) and catching AttributeError.
Type:      builtin_function_or_method
```


---
### getattr, settattr, delattr

```python
In [5]: getattr?
Docstring:
getattr(object, name[, default]) -> value

Get a named attribute from an object; getattr(x, 'y') is equivalent to x.y.
When a default argument is given, it is returned when the attribute doesn't
exist; without it, an exception is raised in that case.
Type:      builtin_function_or_method
```

```python
In [6]: setattr?
Signature: setattr(obj, name, value, /)
Docstring:
Sets the named attribute on the given object to the specified value.

setattr(x, 'y', v) is equivalent to ``x.y = v''
Type:      builtin_function_or_method
```

```python
In [7]: delattr?
Signature: delattr(obj, name, /)
Docstring:
Deletes the named attribute from the given object.

delattr(x, 'y') is equivalent to ``del x.y''
Type:      builtin_function_or_method
```

---
### [Функции высшего порядка](https://ru.wikipedia.org/wiki/%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D1%8F_%D0%B2%D1%8B%D1%81%D1%88%D0%B5%D0%B3%D0%BE_%D0%BF%D0%BE%D1%80%D1%8F%D0%B4%D0%BA%D0%B0) (higher-order function)

Функция, принимающая в качестве аргументов другие функции или возвращающая
другую функцию в качестве результата.



---
### Функция map

Функция map применяет функцию к каждому элементу последовательности и
возвращает итератор с результатами.

```python
In [1]: list_of_words = ['one', 'two', 'list', '', 'dict']

In [2]: map(str.upper, list_of_words)
Out[2]: <map at 0xb45eb7ec>

In [3]: list(map(str.upper, list_of_words))
Out[3]: ['ONE', 'TWO', 'LIST', '', 'DICT']
```

---
### Функция map

Конвертация в числа:

```python
In [3]: list_of_str = ['1', '2', '5', '10']

In [4]: list(map(int, list_of_str))
Out[4]: [1, 2, 5, 10]
```

Вместе с map удобно использовать лямбда-выражения:

```python
In [5]: vlans = [100, 110, 150, 200, 201, 202]

In [6]: list(map(lambda x: 'vlan {}'.format(x), vlans))
Out[6]: ['vlan 100', 'vlan 110', 'vlan 150', 'vlan 200', 'vlan 201', 'vlan 202']
```

---
## Функция filter

Функция ``filter`` применяет функцию ко всем элементам последовательности
и возвращает итератор с теми объектами, для которых функция вернула
True.

```python
In [1]: list_of_strings = ['one', 'two', 'list', '', 'dict', '100', '1', '50']

In [2]: filter(str.isdigit, list_of_strings)
Out[2]: <filter at 0xb45eb1cc>

In [3]: list(filter(str.isdigit, list_of_strings))
Out[3]: ['100', '1', '50']
```

---
## Функция filter

Из списка чисел оставить только нечетные:

```python
In [3]: list(filter(lambda x: x % 2 == 1, [10, 111, 102, 213, 314, 515]))
Out[3]: [111, 213, 515]
```

Аналогично, только четные:

```python
In [4]: list(filter(lambda x: x % 2 == 0, [10, 111, 102, 213, 314, 515]))
Out[4]: [10, 102, 314]
```

---
### Анонимная функция (лямбда-выражение)

В Python лямбда-выражение позволяет создавать анонимные функции - функции, которые не привязаны к имени.

В анонимной функции:

* может содержаться только одно выражение
* могут передаваться сколько угодно аргументов

Стандартная функция:

```python
def sum_arg(a, b):
    return a + b
```

Аналогичная анонимная функция, или лямбда-функция:

```python
sum_arg = lambda a, b: a + b
```
