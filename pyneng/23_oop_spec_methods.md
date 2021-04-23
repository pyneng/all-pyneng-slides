# Python для сетевых инженеров 

---

## Объектно-ориентированное программирование


---
### Специальные методы

---
### `__str__`

---
### `__str__`

Метод `__str__` отвечает за строковое отображение информации об объекте. Он вызывается при использовании str и print:

```python
class Switch:
    def __init__(self, hostname, model):
        self.hostname = hostname
        self.model = model

    def __str__(self):
        return 'Swicth: {}'.format(self.hostname)

In [5]: sw1 = Switch('sw1', 'Cisco 3850')

In [6]: print(sw1)
Swicth: sw1

In [7]: str(sw1)
Out[7]: 'Swicth: sw1'
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

---
### Метод `__getattr__`


---
### Метод `__getattr__`

Метод `__getattr__` вызывается только для не существующих атрибутов:
```python
In [1]: class CiscoSSH:
   ...:     def __init__(self, **device_params):
   ...:         self._device_params = device_params
   ...:         self.ssh = netmiko.ConnectHandler(**device_params)
   ...:         self.ssh.enable()
   ...:

In [2]: r1 = CiscoSSH(**DEVICE_PARAMS)

In [3]: r1.test
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-20-d2cff4242cf5> in <module>()
----> 1 r1.test

AttributeError: 'CiscoSSH' object has no attribute 'test'
```

---
### Метод `__getattr__`

Этот метод позволяет динамически создавать атрибуты, при первом вызове:
```python
class CiscoSSH:
    def __init__(self, **device_params):
        self.ssh = netmiko.ConnectHandler(**device_params)
        self.ssh.enable()

    def __getattr__(self, attr):
        if attr == 'sh_version':
            print('Создаю атрибут sh_version')
            output = self.ssh.send_command('sh version')
            self.sh_version = re.search('Version (.*?),', output).group(1)
            return self.sh_version

In [8]: r1 = CiscoSSH(**DEVICE_PARAMS)

In [9]: r1.sh_version
Создаю атрибут sh_version
Out[9]: '15.2(2.3)T'

In [10]: r1.sh_version
Out[10]: '15.2(2.3)T'
```

---
### Метод `__getattr__`

При таком варианте метода `__getattr__` не генерируется исключение при доступе к несуществующим атрибутам. Чтобы вернуть этот функционал, добавлена генерация исключения:
```python
class CiscoSSH:
    def __init__(self, **device_params):
        self.ssh = netmiko.ConnectHandler(**device_params)
        self.ssh.enable()

    def __getattr__(self, attr):
        if attr == 'sh_version':
            print('Создаю атрибут sh_version')
            output = self.ssh.send_command('sh version')
            self.sh_version = re.search('Version (.*?),', output).group(1)
            return self.sh_version
        else:
            raise AttributeError("'CiscoSSH' object has no attribute '{}'".format(attr))
```

---
### Метод `__getattr__`

```python
In [3]: r1 = CiscoSSH(**DEVICE_PARAMS)

In [4]: r1.sh_version
Создаю атрибут sh_version
Out[4]: '15.2(2.3)T'

In [5]: r1.sh_clock
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-55-0e09b642cfdd> in <module>()
----> 1 r1.sh_clock

<ipython-input-52-e5278c4462df> in __getattr__(self, attr)
     11             return self.sh_version
     12         else:
---> 13             raise AttributeError("'CiscoSSH' object has no attribute '{}'".format(attr))

AttributeError: 'CiscoSSH' object has no attribute 'sh_clock'
```


