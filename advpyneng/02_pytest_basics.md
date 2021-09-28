# Advanced Python для сетевых инженеров
## Основы pytest

---
### Тестирование кода

Тестирование кода позволяет проверить:

* работает ли код так как нужно
* как ведет себя код в нестандартных ситуациях
* пользовательский интерфейс
* ...

---
### Уровни тестирования

* unit - тестирование отдельных функций/классов
* integration - тестирование взаимодействия разных частей софта друг с другом
* system - тестируется вся система, для web, например, это может быть
  тестирование от логина пользователя до выхода

---
### Виды тестов

[Software testing](https://en.wikipedia.org/wiki/Software_testing)

---
### Основы pytest

pytest - фреймворк для тестирования кода

Альтернативы:

* [unittest](https://pymotw.com/3/unittest/index.html)
* [doctest](https://pymotw.com/3/doctest/index.html)
* nose

Полезные ссылки по другим фреймворкам:

* [Test & code podcast: 2: Pytest vs Unittest vs Nose](https://testandcode.com/2)
* [The Cleaning Hand of Pytest](https://blog.daftcode.pl/the-cleaning-hand-of-pytest-28f434f4b684)

---
### unittest

```python
import unittest

class TestStringMethods(unittest.TestCase):

    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')

    def test_isupper(self):
        self.assertTrue('FOO'.isupper())
        self.assertFalse('Foo'.isupper())

    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):
            s.split(2)


if __name__ == '__main__':
    unittest.main()
```

---
### doctest

```python
def my_function(a, b):
    """Returns a * b.

    Works with numbers:

    >>> my_function(2, 3)
    6

    and strings:

    >>> my_function('a', 3)
    'aaa'
    """
    return a * b
```

---
### pytest

```python
def test_upper():
    assert 'foo'.upper() == 'FOO'


def test_isupper():
    assert 'FOO'.isupper() == True
    assert 'Foo'.isupper() == False


def test_split():
    s = 'hello world'
    assert s.split() == ['hello', 'world']
    # check that s.split fails when the separator is not a string
    with pytest.raises(TypeError):
        s.split(2)
```

---
### pytest

```python
def check_ip(ip):
    try:
        ipaddress.ip_address(ip)
        return True
    except ValueError as err:
        return False
```

Пример базового теста
```python
def test_check_ip():
    assert check_ip('10.1.1.1') == True, 'При правильном IP, функция должна возвращать True'
    assert check_ip('500.1.1.1') == False, 'Если адрес неправильный, функция должна возвращать False'
```

---
### Запуск теста

```
$ pytest check_ip_functions.py
========================= test session starts ==========================
...
check_ip_functions.py .                                          [100%]

======================= 1 passed in 0.02 seconds =======================
```

Тест не проходит
```
$ pytest check_ip_functions.py
========================= test session starts ==========================
...
check_ip_functions.py F                                          [100%]

=============================== FAILURES ===============================
____________________________ test_check_ip _____________________________

    def test_check_ip():
>       assert check_ip('10.1.1.1') == True, 'При правильном IP, функция должна возвращать True'
E       AssertionError: При правильном IP, функция должна возвращать True
E       assert False == True
E        +  where False = check_ip('10.1.1.1')

check_ip_functions.py:14: AssertionError
======================= 1 failed in 0.06 seconds =======================
```

---
### Статус теста

* `.` PASSED
* `F` FAILED
* `s` SKIPPED
* `E` ERROR
* `x` XFAIL - должен был FAIL, но PASSED
* `X` XPASS

---
### Запуск теста

Запуск тестов из конкретного файла:

```
$ pytest test_check_password.py
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 3 items

test_check_password.py ...                                        [100%]

=========================== 3 passed in 0.02s ===========================
```

---
### Запуск теста

Запуск всех тестов:

```
$ pytest
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 9 items

test_check_ip_function.py .                                       [ 11%]
test_check_password.py ...                                        [ 44%]
test_ipv4_network.py .....                                        [100%]

=========================== 9 passed in 0.07s ===========================
```


---
### Запуск теста
Запуск тестов с более подробной информацией:

```

$ pytest -v
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0 -- /home/vagrant/venv/pyneng-py3-7/bin/python3.7
cachedir: .pytest_cache
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 9 items

test_check_ip_function.py::pythontest_check_ip PASSED                   [ 11%]
test_check_password.py::pythontest_password_min_length PASSED           [ 22%]
test_check_password.py::pythontest_password_contains_username PASSED    [ 33%]
test_check_password.py::pythontest_password_default_values PASSED       [ 44%]
test_ipv4_network.py::pythontest_class_created PASSED                   [ 55%]
test_ipv4_network.py::pythontest_attributes_created PASSED              [ 66%]
test_ipv4_network.py::pythontest_methods_created PASSED                 [ 77%]
test_ipv4_network.py::pythontest_return_types PASSED                    [ 88%]
test_ipv4_network.py::pythontest_address_allocation PASSED              [100%]

=========================== 9 passed in 0.08s ===========================
```


---
### Запуск теста

Запуск одного теста

```

$ pytest test_check_password.py::pythontest_password_min_length -v
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0 -- /home/vagrant/venv/pyneng-py3-7/bin/python3.7
cachedir: .pytest_cache
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 1 item

test_check_password.py::pythontest_password_min_length PASSED           [100%]

=========================== 1 passed in 0.01s ===========================
```


---
### Запуск теста

Отображение вывода на stdout:

```
$ pytest test_check_password.py -s
=========================== test session starts ============================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 3 items

test_check_password.py Проверяем пароль
Пароль для пользователя nata прошел все проверки
Пароль содержит имя пользователя
.Пароль для пользователя nata прошел все проверки
Пароль содержит имя пользователя
.Пароль слишком короткий
Пароль содержит имя пользователя
Пароль для пользователя nata прошел все проверки
.

============================ 3 passed in 0.02s =============================
```


---
### Запуск теста

Аналогично с verbose:

```
$ pytest test_check_password.py -v -s
=========================== test session starts ============================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0 -- /home/vagrant/venv/pyneng-py3-7/bin/python3.7
cachedir: .pytest_cache
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 3 items

test_check_password.py::pythontest_password_min_length Проверяем пароль
Пароль для пользователя nata прошел все проверки
Пароль содержит имя пользователя
PASSED
test_check_password.py::pythontest_password_contains_username Пароль для пользователя nata прошел все проверки
Пароль содержит имя пользователя
PASSED
test_check_password.py::pythontest_password_default_values Пароль слишком короткий
Пароль содержит имя пользователя
Пароль для пользователя nata прошел все проверки
PASSED

============================ 3 passed in 0.02s =============================
```


---
### Запуск теста

Вывод когда тесты не проходят

```
$ pytest test_check_password.py
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 3 items

test_check_password.py .F.                                        [100%]

=============================== FAILURES ================================
____________________ test_password_contains_username ____________________

    def test_password_contains_username():
        assert check_passwd('nata', '12345nata', min_length=3, check_username=False)
        assert not check_passwd('nata', '12345nata', min_length=3, check_username=True)
>       assert not check_passwd('nata', '12345NATA', min_length=3, check_username=True), "Если в пароле присутствует имя пользователя в любом регистре, проверка не должна пройти"
E       AssertionError: Если в пароле присутствует имя пользователя в любом регистре, проверка не должна пройти
E       assert not True
E        +  where True = check_passwd('nata', '12345NATA', min_length=3, check_username=True)

test_check_password.py:12: AssertionError
------------------------- Captured stdout call --------------------------
Пароль для пользователя nata прошел все проверки
Пароль содержит имя пользователя
Пароль для пользователя nata прошел все проверки
```


---
### Запуск теста

Короткий вывод traceback:

```
$ pytest test_check_password.py --tb=line
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 3 items

test_check_password.py .F.                                        [100%]

=============================== FAILURES ================================
/home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics/test_check_password.py:12: AssertionError: Если в пароле присутствует имя пользователя в любом регистре, проверка не должна пройти
====================== 1 failed, 2 passed in 0.07s ======================
```


---
### Запуск теста

Остановиться после первого неудачного теста

```

$ pytest test_check_password.py --tb=line -x
========================== test session starts ==========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 3 items

test_check_password.py .F

=============================== FAILURES ================================
/home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics/test_check_password.py:12: AssertionError: Если в пароле присутствует имя пользователя в любом регистре, проверка не должна пройти
====================== 1 failed, 1 passed in 0.06s ======================
```


---
### Запуск теста

Показать какие тесты есть, но не запускать их

```
$ pytest --collect-only
========================== test session starts ===========================
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-3/advpyneng-online-3-sep-dec-2021/examples/01_pytest_basics
collected 9 items
<Module test_check_ip_function.py>
  <Function test_check_ip>
<Module test_check_password.py>
  <Function test_password_min_length>
  <Function test_password_contains_username>
  <Function test_password_default_values>
<Module test_ipv4_network.py>
  <Function test_class_created>
  <Function test_attributes_created>
  <Function test_methods_created>
  <Function test_return_types>
  <Function test_address_allocation>

========================= no tests ran in 0.05s ==========================
```

---
### Структура теста

AAA (Arrange, Act, Assert)

```python
def test_function_return_value(r1_test_connection, first_router_from_devices_yaml):
    """
    Тест проверяет работу функции send_show_command
    first_router_from_devices_yaml - это первое устройство из файла devices.yaml
    r1_test_connection - это сессия SSH с первым устройством из файла devices.yaml
                         Используется для проверки вывода
    """
    correct_return_value = strip_empty_lines(
        r1_test_connection.send_command("sh ip int br")
    )
    return_value = strip_empty_lines(
        task_18_1.send_show_command(first_router_from_devices_yaml, "sh ip int br")
    )
    assert return_value != None, "Функция ничего не возвращает"
    assert (
        type(return_value) == str
    ), f"По заданию функция должна возвращать строку, а возвращает {type(return_value).__name__}"
    assert (
        correct_return_value == return_value
    ), "Функция возвращает неправильное значение"
```

---
### Параметризация теста

```python
@pytest.mark.parametrize("username,password,min_length,result",[
    ('nata', '12345', 3, True),
    ('nata', '12345nata', 3, False)
])
def test_password_min_length(username, password, min_length, result):
    assert result == check_passwd(username, password, min_length=min_length)
```

---
### pytest fixture

Fixtures это функции, которые выполняют что-то до теста и, при необходимости, после.

Два самых распространенных применения fixture:

* для передачи каких-то данных для теста
* setup and teardown

---
### fixture scope

* function (default)
* class
* module
* package
* session

---
### Полезные команды для работы с fixture

```
$ pytest --fixtures
$ pytest --setup-show
```

---
### pytest fixture

```python
import re
import yaml
import pytest
from netmiko import ConnectHandler


@pytest.fixture()
def topology_with_dupl_links():
    topology = {('R1', 'Eth0/0'): ('SW1', 'Eth0/1'),
                ('R2', 'Eth0/0'): ('SW1', 'Eth0/2'),
                ('R2', 'Eth0/1'): ('SW2', 'Eth0/11'),
                ('R3', 'Eth0/0'): ('SW1', 'Eth0/3'),
                ('R3', 'Eth0/1'): ('R4', 'Eth0/0'),
                ('R3', 'Eth0/2'): ('R5', 'Eth0/0'),
                ('SW1', 'Eth0/1'): ('R1', 'Eth0/0'),
                ('SW1', 'Eth0/2'): ('R2', 'Eth0/0'),
                ('SW1', 'Eth0/3'): ('R3', 'Eth0/0')}
    return topology


@pytest.fixture()
def normalized_topology_example():
    normalized_topology = {('R1', 'Eth0/0'): ('SW1', 'Eth0/1'),
                           ('R2', 'Eth0/0'): ('SW1', 'Eth0/2'),
                           ('R2', 'Eth0/1'): ('SW2', 'Eth0/11'),
                           ('R3', 'Eth0/0'): ('SW1', 'Eth0/3'),
                           ('R3', 'Eth0/1'): ('R4', 'Eth0/0'),
                           ('R3', 'Eth0/2'): ('R5', 'Eth0/0')}
    return normalized_topology
```

---
### pytest fixture

```python
import yaml
import pytest
from netmiko import ConnectHandler


@pytest.fixture(scope='module')
def first_router_from_devices_yaml():
    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)
        r1 = devices[0]
        #options = {'timeout': 5, 'fast_cli': True}
        r1.update(options)
    return r1


@pytest.fixture(scope='module')
def first_router_wrong_pass(first_router_from_devices_yaml):
    r1 = first_router_from_devices_yaml.copy()
    r1['password'] = 'wrong'
    return r1


@pytest.fixture(scope='module')
def first_router_wrong_ip(first_router_from_devices_yaml):
    r1 = first_router_from_devices_yaml.copy()
    r1['ip'] = 'unreachable'
    return r1
```

---
### pytest fixture

```python
@pytest.fixture(scope='module')
def r1_test_connection(first_router_from_devices_yaml):
    with ConnectHandler(**first_router_from_devices_yaml) as r1:
        r1.enable()
        yield r1
```

