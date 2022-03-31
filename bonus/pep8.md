# Python для сетевых инженеров 

---
## PEP 8 - руководство по написанию кода

---

## Расположение кода

---
## Табы или пробелы

* В качестве отступов рекомендуется использовать пробелы.
* Один уровень отступа - 4 пробела
* Python 3 запрещает одновременное использование Tab и пробелов в отступах.

> Как правило, в редакторах и IDE есть настройка, которая позволяет указать, что при нажатии клавиши Tab будет устанавливаться 4 пробела.


---
### Отступы. Да

Вертикальное выравнивание по открывающейся скобке:
```python
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
```

Выравнивание с учетом остальных строк.
Тут параметры функции сдвинуты относительно print,
чтобы визуально разделить их:
```python
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

Висячие отступы (hanging indents) - все строки с отступом, кроме первой.
Однако, надо учитывать ситуацию выше
и отделять строки дополнительным отступом, если необходимо:
```python
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

---
### Отступы. Да

При использовании висячих отступов, можно использовать не только 4 пробела
```python
foo = long_function_name(
  var_one, var_two,
  var_three, var_four)
```


---
### Отступы. Нет

Запрещено писать аргументы в первой строке, если не используется вертикальное выравнивание:
```python
foo = long_function_name(var_one, var_two,
    var_three, var_four)
```

В этом случае, надо добавить отступы перед параметрами функции,
чтобы они отличались от следующих строк:
```python
def long_function_name(
    var_one, var_two, var_three,
    var_four):
    print(var_one)
```

---
### Отступы и перенос условия на разные строки

Иногда условие в выражении if слишком длинное, чтобы писать его в одной строке.
В таком случае, возможны такие варианты переноса выражения:

Часть выражения находится на том же уровне, что и следующие команды:
```python
if (this_is_one_thing
    and that_is_another_thing):
    do_something()
```

В варианте выше сложнее отличить часть условия от команд внутри условия.
Улучшить ситуацию можно сделав дополнительный отступ в условии:
```python
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

---
### Отступы и перенос условия на разные строки

Иногда, создание дополнительной переменной перед условием, также может помочь сократить условие
```python
ignore_line = '!' in line or ignore_command(line, ignore)

if line and not ignore_line:
    do_something()
```

Python позволяет разбивать код до или после операторов, однако рекомендуется разбивать код до операторов.
Такой вариант обычно легче воспринимается
```python
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

---
### Отступы и перенос условия на разные строки

```python
income = (gross_wages +
          taxable_interest +
          (dividends - qualified_dividends) -
          ira_deduction -
          student_loan_interest)
```

---
### Закрывающиеся скобки

Первый вариант закрытия скобок:
```python
port_security_template = [
    'switchport port-security maximum 2',
    'switchport port-security violation restrict',
    'switchport port-security',
    ]
```

Второй вариант закрытия скобок:
```python
port_security_template = [
    'switchport port-security maximum 2',
    'switchport port-security violation restrict',
    'switchport port-security',
]
```

---
## Максимальная длина строки

* Максимальная длина строки кода - 79 символов.
* Максимальная длина комментариев/docstring - 72 символа.

Длинные строки предпочтительней разбивать на несколько, используя круглые скобки:
```python
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

Многие выражения в скобках можно переносить на разные строки:
```python
r1 = {'IOS': '15.4',
      'IP': '10.255.0.1',
      'hostname': 'london_r1',
      'location': '21 New Globe Walk',
      'model': '4451',
      'vendor': 'Cisco'}

lower_r1 = {str.lower(key): value
            for key, value in r1.items()}
```

---
## Максимальная длина строки

Некоторые выражения нельзя разбить на несколько строк. В этом случае, можно использовать обратный слеш:
```python
with open('/path/to/some/file/you/want/to/read') as file_1, \
     open('/path/to/some/file/being/written', 'w') as file_2:
    file_2.write(file_1.read())
```

Аналогичная ситуация будет с выражением assert:
```python
assert return_value == correct_return_value,\
       "Функция возвращает неправильное значение"
```

---
## Пустые строки

Функции и классы должны быть отделены друг от друга двумя пустыми строками:
```python
def sum_numbers(a, b):
    return a + b


def divide_numbers(a, b):
    return a/b


class Robot:

    def __init__(self, name):
        self.name = name
```

---
## Пустые строки

Внутри класса метода должны быть отделены друг от друга одной пустой строкой:
```python
class Robot:

    def __init__(self, name):
        self.name = name

    def hello(self):
        return f'My name is {self.name}'

    def say(self, message):
        return f'message'
```

---
## Кодировка исходного файла

Для кода должна использоваться кодировка UTF-8.

Соглашение по именам в стандартной библиотеке:

* все идентификаторы должны состоять только из ASCII символов
* индентификаторы должны сосоять из английских слов/сокращений
* комментарии должны состоять из ASCII символов

---
### Импорт

Импорт модулей всегда должен находиться в начале файла, сразу после docstring/комментариев модуля.
```python
# -*- coding: utf-8 -*-
"""Utilities for writing code that runs on Python 2 and 3"""

import functools
import itertools
import operator
import sys
```

---
### Импорт

Импорт модулей должен выполняться в таком порядке
1. Модули стандартной библиотеки
2. Модули сторонних библиотек
3. Модули текущего проекта  

Каждая группа должна быть разделена пустой строкой.

```python
import sys
import os

import tabulate
import jinja

import my_super_script
```

---
### Импорт. Правильно

Импорт каждого модуля, как правило, должен находиться на отдельной строке:
```python
import os
import sys
```

При этом, можно импортировать несколько объектов из модуля в одной строке:
```python
from subprocess import run, PIPE
```

---
### Импорт. Неправильно

```python
import os, sys
```

---
## Пробелы в выражениях

Следует избегать использования пробелов в таких ситуациях:

* сразу после открытых скобок
* пробелы между последней запятой и закрывающейся скобкой
* перед запятой, двоеточием и точкой с запятой
* перед открывающейся скобкой, за которой следуют аргументы функции, индекс или срез 
* более одного пробела вокруг оператора присваивания


---
### Пробелы сразу после открытых скобок

Правильно:
```python
spam(ham[1], {eggs: 2})
```

Неправильно:
```python
spam( ham[ 1 ], { eggs: 2 } )
```


---
### Пробелы между последней запятой и закрывающейся скобкой

Правильно:
```python
foo = (0,)
```

Неправильно:
```python
foo = (0, )
```

---
### Пробелы перед запятой, двоеточием и точкой с запятой

Правильно:
```python
if x == 4: print x, y; x, y = y, x
```

Неправильно:
```python
if x == 4 : print x , y ; x , y = y , x
```

---
### Пробелы в срезах

Правильно:
```python
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
ham[lower:upper], ham[lower:upper:], ham[lower::step]
ham[lower+offset : upper+offset]
ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
ham[lower + offset : upper + offset]
```

Неправильно:
```python
ham[lower + offset:upper + offset]
ham[1: 9], ham[1 :9], ham[1:9 :3]
ham[lower : : upper]
ham[ : upper]
```

---
### Перед открывающейся скобкой, за которой следуют аргументы функции, индекс или срез 

Правильно:
```python
spam(1)
dct['key'] = lst[index]
```

Неправильно:
```python
spam (1)
dct ['key'] = lst [index]
```

---
### Более одного пробела вокруг оператора присваивания

Правильно:
```python
x = 1
y = 2
long_variable = 3
```

Неправильно:
```python
x             = 1
y             = 2
long_variable = 3
```

---
### Знак равенства в ключевом аргументе

Не используйте пробелы вокруг знака равенства, если он используется в ключевом аргументе или в параметре со значением по умолчанию.

Правильно:
```python
def complex(real, imag=0.0):
    return magic(r=real, i=imag)
```

Неправильно:
```python
def complex(real, imag = 0.0):
    return magic(r = real, i = imag)
```


---
### Пробелы в конце строки

Не ставьте пробелы в конце строки!

Например, пробел после обратного слеша приводит к ошибке
```python
In [3]: return_value = 4
   ...: correct_return_value = 3
   ...: assert return_value == correct_return_value,\
   ...:        "Функция возвращает неправильное значение"
  File "<ipython-input-3-42fca39fd65f>", line 3
    assert return_value == correct_return_value,\
                                                  ^
SyntaxError: unexpected character after line continuation character
```

---
### Пробелы в аннотации функций

Правильно:
```python
def munge(input: AnyStr):
    ...

def munge() -> AnyStr:
    ...
```

Неправильно:
```python
def munge(input:AnyStr):
    ...

def munge()->PosInt:
    ...
```


---
### Пробелы в аннотации функций

Правильно:
```python
def munge(sep: AnyStr = None):
    ...

def munge(input: AnyStr, sep: AnyStr = None, limit=1000):
    ...
```

Неправильно:
```python
def munge(input: AnyStr=None):
    ...

def munge(input: AnyStr, limit = 1000):
    ...
```

---
## Составные конструкции

---
### Составные конструкции

В целом, лучше не писать несколько выражений в одной строке.

Правильно:
```python
if foo == 'blah':
    do_blah_thing()
do_one()
do_two()
do_three()
```

Неправильно:
```python
if foo == 'blah': do_blah_thing()
do_one(); do_two(); do_three()
```


---
### Составные конструкции

Небольшие выражения if/while/for можно писать в одной строке, но если в блоке несколько команд, не пишите выражение в одну строку.

Лучше не писать так:
```python 
if foo == 'blah': do_blah_thing()
for x in lst: total += x
while t < 10: t = delay()
```

И точно не стоит писать так:
```python
if foo == 'blah': do_blah_thing()
else: do_non_blah_thing()

try: something()
finally: cleanup()

do_one(); do_two(); do_three(long, argument,
                             list, like, this)

if foo == 'blah': one(); two(); three()
```

---
## Комментарии


---
### Комментарии

* Комментарий, который противоречит коду большее зло, чем отсутствие комментария
* Всегда изменяйте комментарии вместе с кодом
* Не меняйте регистр переменной в комментарии, даже если имя переменной это первое слово в предложении
* Комментарии должны быть на английском (если только вы не уверены, что ваш код никогда не будут читать люди, которые не говорят на вашем языке)


---
### Комментарии перед блоком кода

Обычно комментарии относятся к блоку кода, который следует за ними и должны быть на том же уровне отступа, что и код следующий за ними.

Если комментарий длинный и в пояснении нужно выделить параграфы, они разделяются между собой пустой строкой с #.
```python
    # Check for device with no password configured
    if re.search(r"assword required, but none set", output):
        self.remote_conn.close()
        msg = "Login failed - Password required, but none set: {}".format(
            self.host
        )
        raise NetMikoAuthenticationException(msg)

    # Check if proper data received
    if re.search(pri_prompt_terminator, output, flags=re.M) or re.search(
        alt_prompt_terminator, output, flags=re.M
    ):
        return return_msg
```

---
### Комментарии в одной строке с кодом

Такие коментарии желательно использовать редко.
Как правило, они нормально воспринимаются, только когда комментарий небольшой.

После кода надо поставить хотя бы два пробела, затем #, пробел и комментарий
```python
x = x + 1   # Compensate for border
```

