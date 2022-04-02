# Python для сетевых инженеров 

---
## Создание базовых скриптов

---
### Создание базовых скриптов

Файл access_template.py:
```python
access_template = ['switchport mode access',
                   'switchport access vlan {}',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

print('\n'.join(access_template).format(5))
```

---
### Создание базовых скриптов

Так выглядит выполнение скрипта:
```python
$ python access_template.py
switchport mode access
switchport access vlan 5
switchport nonegotiate
spanning-tree portfast
spanning-tree bpduguard enable
```

---
### Исполняемый файл

Для того, чтобы файл был исполняемым, и не нужно было каждый раз писать python перед вызовом файла, нужно:
* сделать файл исполняемым (для linux)
* в первой строке файла должна находиться строка ```#!/usr/bin/env python``` или ```#!/usr/bin/env python3```, в зависимости от того, какая версия Python используется по умолчанию

---
### Исполняемый файл

Пример файла access_template_exec.py:
```python
#!/usr/bin/env python3

access_template = ['switchport mode access',
                   'switchport access vlan {}',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

print('\n'.join(access_template).format(5))

```

---
### Исполняемый файл

После этого:
```
chmod +x access_template_exec.py
```

Теперь можно вызывать файл таким образом:
```
$ ./access_template_exec.py
```

---
## Передача аргументов скрипту (argv)

---
### Передача аргументов скрипту

В модуле sys есть очень простой и удобный способ для работы с аргументами - argv.

Файл access_template_argv.py:
```python
from sys import argv

interface, vlan = argv[1:]

access_template = ['switchport mode access',
                   'switchport access vlan {}',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

print('interface {}'.format(interface))
print('\n'.join(access_template).format(vlan))
```

---
### Передача аргументов скрипту

```
$ python access_template_argv.py Gi0/7 4
interface Gi0/7
switchport mode access
switchport access vlan 4
switchport nonegotiate
spanning-tree portfast
spanning-tree bpduguard enable
```

---
### Передача аргументов скрипту

Аргументы, которые были переданы скрипту, подставляются как значения в шаблон.

* argv - это список
* все аргументы находятся в списке в виде строк
* argv содержит не только аргументы, которые передали скрипту, но и название самого скрипта

В данном случае в списке argv находятся такие элементы:
```
['access_template_argv.py', 'Gi0/7', '4']
```

Сначала идет имя самого скрипта, затем аргументы, в том же порядке.

---
### Распаковка

В Python есть возможность за раз присвоить значения нескольким переменным:
```python
In [16]: a = 5
In [17]: b = 6
In [18]: c, d = 5, 6
In [19]: c
Out[19]: 5

In [20]: d
Out[20]: 6
```

---
### Распаковка

Если вместо чисел список, как в случае с argv:
```python
In [21]: arg = ['Gi0/7', '4']
In [22]: interface, vlan = arg

In [23]: interface
Out[23]: 'Gi0/7'

In [24]: vlan
Out[24]: '4'
```

---
## Ввод информации пользователем

---
### Ввод информации пользователем

Для получения информации от пользователя используется функция `input()`:

```python
In [1]: print(input('Твой любимый протокол маршрутизации? '))
Твой любимый протокол маршрутизации? OSPF
OSPF
```

---
### Ввод информации пользователем

В данном случае информация просто тут же выводится пользователю, но, кроме этого, информация, которую ввел пользователь, может быть сохранена в какую-то переменную и может использоваться далее в скрипте.

```python
In [2]: protocol = input('Твой любимый протокол маршрутизации? ')
Твой любимый протокол маршрутизации? OSPF

In [3]: print(protocol)
OSPF
```
---
### Ввод информации пользователем

В скобках обычно пишется какой-то вопрос, который уточняет, какую информацию нужно ввести.

Текст в скобках, в принципе, писать не обязательно.  
И можно сделать такой же вывод с помощью функции **print**:

```python
In [4]: print('Твой любимый протокол маршрутизации?')
Твой любимый протокол маршрутизации?

In [5]: protocol = input()
OSPF

In [6]: print(protocol)
OSPF
```

---
### Ввод информации пользователем

```python
interface = input('Enter interface type and number: ')
vlan = input('Enter VLAN number: ')

access_template = ['switchport mode access',
                   'switchport access vlan {}',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

print('\n' + '-' * 30)
print('interface {}'.format(interface))
print('\n'.join(access_template).format(vlan))
```

---
### Ввод информации пользователем

```
$ python access_template_input.py
Enter interface type and number: Gi0/3
Enter VLAN number: 55

------------------------------
interface Gi0/3
switchport mode access
switchport access vlan 55
switchport nonegotiate
spanning-tree portfast
spanning-tree bpduguard enable
```

