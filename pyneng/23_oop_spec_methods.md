# Python для сетевых инженеров 

---

## Объектно-ориентированное программирование
### Специальные методы

---
### Специальные методы

Специальные методы в Python - это методы, которые отвечают за "стандартные" возможности
объектов и вызываются автоматически при использовани этих возможностей.
Например, выражение ``a + b``, где a и b это числа, преобразуется в такой вызов
``a.__add__(b)``, то есть, специальный метод __add__ отвечает за операцию сложения.
Все специальные методы начинаются и заканчиваются двойным подчеркиванием, поэтому
на английском их часто называют dunder методы, сокращенно от "double underscore".

Специальные методы отвечают за такие возможности как работа в менеджерах контекста,
создание итераторов и итерируемых объектов, операции сложения, умножения и другие.
Добавляя специальные методы в объекты, которые созданы пользователем, мы делаем
их похожими на встроенные объекты.

---
### Подчеркивание в именах

В Python подчеркивание в начале или в конце имени указывает на
специальные имена. Чаще всего это всего лишь договоренность, но иногда
это действительно влияет на поведение объекта.


---
### Одно подчеркивание перед именем

Одно подчеркивание перед именем метода указывает, что метод является 
внутренней особенностью реализации и его не стоит использовать напрямую.

```python
class CiscoSSH:
    def __init__(self, ip, username, password, enable, disable_paging=True):
        self._client = paramiko.SSHClient()
        self._client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

        self._client.connect(
            hostname=ip,
            username=username,
            password=password,
            look_for_keys=False,
            allow_agent=False)

        self._ssh = self._client.invoke_shell()
        self._ssh.send('enable\n')
        self._ssh.send(enable + '\n')
        if disable_paging:
            self._ssh.send('terminal length 0\n')
        time.sleep(1)
        self._ssh.recv(1000)

    def send_show_command(self, command):
        self._ssh.send(command + '\n')
        time.sleep(2)
        result = self._ssh.recv(5000).decode('ascii')
        return result
```

---
### Два подчеркивания перед именем

Два подчеркивания перед именем метода используются не просто как
договоренность. Такие имена трансформируются в формат "имя класса + имя
метода". Это позволяет создавать уникальные методы и атрибуты классов.

Такое преобразование выполняется только в том случае, если в конце
менее двух подчеркиваний или нет подчеркиваний.

```python
In [14]: class Switch(object):
    ...:     __quantity = 0
    ...:
    ...:     def __configure(self):
    ...:         pass
    ...:

In [15]: dir(Switch)
Out[15]:
['_Switch__configure', '_Switch__quantity', ...]
```

---
### Два подчеркивания перед и после имени

Таким образом обозначаются специальные переменные и методы.

* ``__name__`` - эта переменная равна строке ``__main__``, когда скрипт
  запускается напрямую, и равна имени модуля, когда импортируется
* ``__file__`` - эта переменная равна имени скрипта, который был запущен
  напрямую, и равна полному пути к модулю, когда он импортируется

Кроме того, таким образом в Python обозначаются специальные методы. Эти
методы вызываются при использовании функций и операторов Python и
позволяют реализовать определенный функционал.

Как правило, такие методы не нужно вызывать напрямую, но, например, при
создании своего класса может понадобиться описать такой метод, чтобы
объект поддерживал какие-то операции в Python.

---
## Методы __str__, __repr__

---
### Методы __str__, __repr__

Специальные методы __str__ и __repr__ отвечают за строковое представления объекта. При этом используются они в разных местах.

```python
In [1]: from datetime import datetime

In [2]: now = datetime.now()

In [3]: str(now)
Out[3]: '2021-08-22 06:48:39.978094'

In [4]: repr(now)
Out[4]: 'datetime.datetime(2021, 8, 22, 6, 48, 39, 978094)'
```

---
### `__str__`

Метод `__str__` отвечает за строковое отображение информации об объекте. Он вызывается при использовании str и print:

```python
class Switch:
    def __init__(self, hostname, model):
        self.hostname = hostname
        self.model = model

    def __str__(self):
        return 'Switch: {}'.format(self.hostname)

In [5]: sw1 = Switch('sw1', 'Cisco 3850')

In [6]: print(sw1)
Switch: sw1

In [7]: str(sw1)
Out[7]: 'Switch: sw1'
```

---
## Протоколы

---
### Протоколы

Специальные методы отвечают не только за поддержку операций типа сложение,
сравнение, но и за поддержку протоколов. Протокол - это набор методов, которые
должны быть реализованы в объекте, чтобы он поддерживал определенное поведение.
Например, в Python есть такие протоколы: итерации, менеджер контекста,
контейнеры и другие. После создания в объекте определенных методов,
объект будет вести себя как встроенный и использовать интерфейс понятный всем, кто пишет на Python.

---
### Протокол итерации

Итерируемый объект (iterable) - это объект, который способен возвращать элементы по одному.
Для Python это любой объект у которого есть метод ``__iter__`` или метод ``__getitem__``.
Если у объекта есть метод __iter__, итерируемый объект превращается в итератор вызовом ``iter(name)``,
где name - имя итерируемого объекта. Если метода __iter__ нет, Python перебирает элементы используя __getitem__.


Итератор (iterator) - это объект, который возвращает свои элементы по одному за раз.
С точки зрения Python - это любой объект, у которого есть метод __next__. Этот метод возвращает следующий элемент, если он есть, или возвращает исключение StopIteration, когда элементы закончились.
Кроме того, итератор запоминает, на каком объекте он остановился в последнюю итерацию.
Также у каждого итератора присутствует метод __iter__ - то есть, любой итератор является итерируемым объектом. Этот метод возвращает сам итератор.

---
### Протокол последовательности

В самом базовом варианте, протокол последовательности (sequence) включает
два метода: __len__ и __getitem__. В более полном варианте также методы:
__contains__, __iter__, __reversed__, index и count. Если последовательность изменяема,
добавляются еще несколько методов.

```python
class Network:
    def __init__(self, network):
        self.network = network
        subnet = ipaddress.ip_network(self.network)
        self.addresses = [str(ip) for ip in subnet.hosts()]

    def __iter__(self):
        return iter(self.addresses)

    def __len__(self):
        return len(self.addresses)

    def __getitem__(self, index):
        return self.addresses[index]
```

---
### Менеджер контекста

---
### Менеджер контекста

Для использования объекта в менеджере контекста, в классе должны быть определены методы `__enter__` и `__exit__`:

* `__enter__` выполняется в начале блока with и, если метод возвращает значение, оно присваивается в переменную, которая стоит после as.
* `__exit__` гарантированно вызывается после блока with, даже если в блоке возникло исключение.

---
### Менеджер контекста

```python
class CiscoSSH:
    def __init__(self, **device_params):
        self.ssh = netmiko.ConnectHandler(**device_params)
        self.ssh.enable()
        print('CiscoSSH __init__ called')

    def __enter__(self):
        print('CiscoSSH __enter__ called')
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print('CiscoSSH __exit__ called')
        self.ssh.disconnect()
```

---
### Менеджер контекста

```python
In [8]: with CiscoSSH(**DEVICE_PARAMS) as r1:
   ...:     print('Внутри with')
   ...:     print(r1.ssh.send_command('sh ip int br'))
   ...:
CiscoSSH __init__ called
CiscoSSH __enter__ called
Внутри with
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
Ethernet0/2                190.16.200.1    YES NVRAM  up                    up
Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
CiscoSSH __exit__ called
```

---
### Менеджер контекста

Если внутри блока with возникает исключение, оно будет сгенерировано после выполнения метода `__exit__`:
```python
In [10]: with CiscoSSH(**DEVICE_PARAMS) as r1:
    ...:     print('Внутри with')
    ...:     print(r1.ssh.send_command('sh ip int br'))
    ...:     raise ValueError('Ошибка')
    ...:
CiscoSSH __init__ called
CiscoSSH __enter__ called
Внутри with
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
Ethernet0/2                190.16.200.1    YES NVRAM  up                    up
Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
CiscoSSH __exit__ called
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-10-4e8b17370785> in <module>()
      2     print('Внутри with')
      3     print(r1.ssh.send_command('sh ip int br'))
----> 4     raise ValueError('Ошибка')

ValueError: Ошибка
```

---
### Менеджер контекста

Если метод `__exit__` возвращает истинное значение, исключение не генерируется:
```python
class CiscoSSH:
    def __init__(self, **device_params):
        self.ssh = netmiko.ConnectHandler(**device_params)
        self.ssh.enable()
        print('CiscoSSH __init__ called')

    def __enter__(self):
        print('CiscoSSH __enter__ called')
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print('CiscoSSH __exit__ called')
        self.ssh.disconnect()
        return True
```

---
### Менеджер контекста

```python
In [17]: with CiscoSSH(**DEVICE_PARAMS) as r1:
    ...:     print('Внутри with')
    ...:     print(r1.ssh.send_command('sh ip int br'))
    ...:     raise ValueError('Ошибка')
    ...:
CiscoSSH __init__ called
CiscoSSH __enter__ called
Внутри with
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
Ethernet0/2                190.16.200.1    YES NVRAM  up                    up
Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
CiscoSSH __exit__ called
```

