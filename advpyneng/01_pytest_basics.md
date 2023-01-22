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
* `x` XFAIL
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
def test_topology_delete_link_mirror(topology_normalized):
    top = Topology(topology_normalized)
    original_len = len(top.topology)
    top.delete_link(("SW1", "Eth0/1"), ("R1", "Eth0/0"))
    assert ("R1", "Eth0/0") not in top.topology
    assert len(top.topology) == original_len - 1


def test_topology_delete_link_not_exists(topology_normalized, capsys):
    top = Topology(topology_normalized)
    original_len = len(top.topology)
    top.delete_link(("SW13", "Eth0/1"), ("R1", "Eth0/0"))
    out = capsys.readouterr().out
    assert len(top.topology) == original_len
    assert "соединения нет" in out
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

Фикстуры (fixtures) это функции, которые выполняют что-то до теста и, при необходимости, после.


> При тестировании электронного оборудования, такого как печатные платы,
> электронные компоненты и микросхемы, fixture представляет
> собой устройство или установку, предназначенную для удержания тестируемого
> устройства на месте и позволяющую тестировать его, подвергая его воздействию
> контролируемых электронных тестовых сигналов.

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
@pytest.fixture()
def r1_test_connection():
    params = {...}
    r1 = ConnectHandler(**params)
    r1.enable()
    yield r1
    r1.disconnect()
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

---
### conftest.py


---
### Fixture может смотреть в тест/модуль, который "вызвал" fixture

conftest.py
```python
@pytest.fixture(scope="module")
def smtp_connection(request):
    server = getattr(request.module, "smtpserver", "smtp.gmail.com")

    smtp_connection = smtplib.SMTP(server, 587, timeout=5)
    yield smtp_connection
    print("finalizing {} ({})".format(smtp_connection, server))
    smtp_connection.close()
```

test_smtp.py
```python
smtpserver = "mail.python.org"  # will be read by smtp fixture


def test_showhelo(smtp_connection):
    assert 0, smtp_connection.helo()
```

---
### factory as fixture

```python
@pytest.fixture
def make_customer_record():
    def _make_customer_record(name):
        return {"name": name, "orders": []}

    return _make_customer_record


def test_customer_records(make_customer_record):
    customer_1 = make_customer_record("Lisa")
    customer_2 = make_customer_record("Mike")
    customer_3 = make_customer_record("Meredith")
```
---
### Параметризация fixture

Параметризация тестов
```python
@pytest.mark.parametrize("ip_address", ["10.1.1.1", "180.1.1.1", "240.1.1.30"])
def test_check_ip_correct(ip_address):
    assert (
        check_ip(ip_address) == True
    ), "При правильном IP, функция должна возвращать True"


@pytest.mark.parametrize("ip_address", ["10.1.1.1", "180.1.1.1", "240.1.1.30"])
def test_ping_ip(ip_address):
    assert (
        ping_ip(ip_address) == True
    ), "Для доступного IP, функция должна возвращать True"
```

---
### Параметризация fixture

Параметризация тестов
```python
@pytest.mark.parametrize("ip_address", ["10.1.1.1", "180.1.1.1", "240.1.1.30"])
def test_check_ip_correct(ip_address):
    assert (
        check_ip(ip_address) == True
    ), "При правильном IP, функция должна возвращать True"
```

Параметризация fixture
```python
@pytest.fixture(params=["10.1.1.1", "180.1.1.1", "240.1.1.30"])
def ip_addr_correct(request):
    return request.param


def test_check_ip_correct(ip_addr_correct):
    assert (
        check_ip(ip_addr_correct) == True
    ), "При правильном IP, функция должна возвращать True"
```

---
### Параметризация fixture

```python
with open("devices.yaml") as f:
    devices_params = yaml.safe_load(f)
ip_list = [d["host"] for d in devices_params]


@pytest.fixture(params=devices_params, scope="session", ids=ip_list)
def ssh_connection(request):
    ssh = ConnectHandler(**request.param)
    ssh.enable()
    yield ssh
    ssh.disconnect()


@pytest.mark.parametrize(
    "ip",
    ["192.168.100.100", "192.168.100.2", "192.168.100.3"],
    ids=["ISP1", "ISP2", "FW"],
)
def test_ping(ssh_connection, ip):
    output = ssh_connection.send_command(f"ping {ip}")
    assert "success rate is 100 percent" in output.lower()
```

---
### Переопределение fixture в conftest.py

```python
tests/
    __init__.py

    conftest.py
        # content of tests/conftest.py
        import pytest

        @pytest.fixture
        def username():
            return 'username'

    test_something.py
        # content of tests/test_something.py
        def test_username(username):
            assert username == 'username'

    subfolder/
        __init__.py

        conftest.py
            # content of tests/subfolder/conftest.py
            import pytest

            @pytest.fixture
            def username(username):
                return 'overridden-' + username

        test_something.py
            # content of tests/subfolder/test_something.py
            def test_username(username):
                assert username == 'overridden-username'
```

---
### Переопределение fixture в тестах

```python
tests/
    __init__.py

    conftest.py
        # content of tests/conftest.py
        import pytest

        @pytest.fixture
        def username():
            return 'username'

    test_something.py
        # content of tests/test_something.py
        import pytest

        @pytest.fixture
        def username(username):
            return 'overridden-' + username

        def test_username(username):
            assert username == 'overridden-username'

    test_something_else.py
        # content of tests/test_something_else.py
        import pytest

        @pytest.fixture
        def username(username):
            return 'overridden-else-' + username

        def test_username(username):
            assert username == 'overridden-else-username'
```

---
### Встроенные fixture

* [capsys](https://docs.pytest.org/en/6.2.x/reference.html#std-fixture-capsys)
* [monkeypatch](https://docs.pytest.org/en/6.2.x/monkeypatch.html)
* [tmp_path](https://docs.pytest.org/en/6.2.x/tmpdir.html)
* [и другие](https://docs.pytest.org/en/6.2.x/fixture.html)

---
### capsys

```python
from netmiko import ConnectHandler
from paramiko.ssh_exception import AuthenticationException


def send_show_command(device, command):
    try:
        with ConnectHandler(**device) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            return result
    except AuthenticationException as error:
        print(error)


def test_function_return_value(capsys, first_router_wrong_pass):
    return_value = send_show_command(first_router_wrong_pass, "sh ip int br")
    correct_stdout = "Authentication fail"
    out, err = capsys.readouterr()
    assert out != "", "Сообщение об ошибке не выведено на stdout"
    assert correct_stdout in out, "Выведено неправильное сообщение об ошибке"
```

---
### monkeypatch

```python
import getpass

def check_passwd(min_length=8, check_username=True):
    username = input('Username: ')
    password = getpass.getpass('Password: ')
    if len(password) < min_length:
        print('Пароль слишком короткий')
        return False
    elif check_username and username in password:
        print('Пароль содержит имя пользователя')
        return False
    else:
        print(f'Пароль для пользователя {username} прошел все проверки')
        return True
```

Тест:
```python
from check_password_function_input import check_passwd


def test_password_min_length():
    assert check_passwd(min_length=3)
```

Вывод
```
$ pytest test_check_password_input.py
======================= test session starts ========================
collected 1 item

test_check_password_input.py F                               [100%]

============================= FAILURES =============================
_____________________ test_password_min_length _____________________

    def test_password_min_length():
>       assert check_passwd(min_length=3)

test_check_password_input.py:5:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _
check_password_function_input.py:2: in check_passwd
    username = input('Username: ')
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

self = <_pytest.capture.DontReadFromInput object at 0xb68f424c>
args = ()

    def read(self, *args):
>       raise IOError("reading from stdin while output is captured")
E       OSError: reading from stdin while output is captured

/home/vagrant/venv/pyneng-py3-7/lib/python3.7/site-packages/_pytest/capture.py:706: OSError
----------------------- Captured stdout call -----------------------
Username:
======================== 1 failed in 0.09s =========================
```

---
### monkeypatch

```python
def test_password_min_length(monkeypatch):
    monkeypatch.setattr('builtins.input', lambda x=None: 'nata')
    assert check_passwd(min_length=3)


@pytest.mark.parametrize("username,password,result",[
    ('nata', '12345', True),
    ('nata', '12345nata', False)
])
def test_password_min_length(monkeypatch, username, password, result):
    monkeypatch.setattr('builtins.input', lambda x=None: username)
    assert result == check_passwd(min_length=3)
```

---
### tmp_path

```python
# content of test_tmp_path.py
CONTENT = "content"


def test_create_file(tmp_path):
    d = tmp_path / "sub"
    d.mkdir()
    p = d / "hello.txt"
    p.write_text(CONTENT)
    assert p.read_text() == CONTENT
    assert len(list(tmp_path.iterdir())) == 1
    assert False
```

---
## [Плагины](https://docs.pytest.org/en/latest/reference/plugin_list.html)

---
### [pytest-lazy-fixture](https://github.com/TvoroG/pytest-lazy-fixture)

```python
import pytest

@pytest.fixture(params=[1, 2])
def one(request):
    return request.param

@pytest.mark.parametrize('arg1,arg2', [
    ('val1', pytest.lazy_fixture('one')),
])
def test_func(arg1, arg2):
    assert arg2 in [1, 2]
```

---
### [pytest-dependency](https://pytest-dependency.readthedocs.io/en/latest/usage.html)

```python
import pytest

@pytest.mark.dependency()
def test_1():
    assert False

@pytest.mark.dependency()
def test_2():
    assert True

@pytest.mark.dependency(depends=["test_2"])
def test_3():
    assert True

@pytest.mark.dependency(depends=["test_1", "test_2"])
def test_4():
    assert True
```

вывод
```
test_plugin_d.py::test_1 FAILED                                  [ 25%]
test_plugin_d.py::test_2 PASSED                                  [ 50%]
test_plugin_d.py::test_3 PASSED                                  [ 75%]
test_plugin_d.py::test_4 SKIPPED (test_4 depends on test_1)      [100%]
```

---
### Пример теста для класса

```python
class Topology:
    def __init__(self, topology_dict):
        self.topology = self._normalize(topology_dict)

    def _normalize(self, topology_dict):
        normalized_topology = {}
        for box, neighbor in topology_dict.items():
            if not neighbor in normalized_topology:
                normalized_topology[box] = neighbor
        return normalized_topology

    def delete_link(self, from_port, to_port):
        if self.topology.get(from_port) == to_port:
            del self.topology[from_port]
        elif self.topology.get(to_port) == from_port:
            del self.topology[to_port]
        else:
            print("Такого соединения нет")

    def __add__(self, other):
        return Topology({**self.topology, **other.topology})

```

---
### Пример теста для класса

```python
def test_topology_normalization(topology_with_dupl_links, normalized_topology_example):
    """Проверка удаления дублей в топологии"""
    top_with_data = Topology(topology_with_dupl_links)
    assert len(top_with_data.topology) == len(normalized_topology_example)


def test_method_delete_link_exists(normalized_topology_example, capsys):
    """Проверка работы метода delete_link"""
    norm_top = Topology(normalized_topology_example)
    delete_link_result = norm_top.delete_link(("R3", "Eth0/0"), ("SW1", "Eth0/3"))
    assert delete_link_result == None, "Метод delete_link не должен ничего возвращать"
    assert ("R3", "Eth0/0") not in norm_top.topology, "Соединение не было удалено"


def test_method_delete_link_mirror(normalized_topology_example, capsys):
    """Проверка работы метода delete_link - проверка удаления зеркального линка"""
    norm_top = Topology(normalized_topology_example)
    norm_top.delete_link(("R5", "Eth0/0"), ("R3", "Eth0/2"))
    assert ("R3", "Eth0/2") not in norm_top.topology, "Соединение не было удалено"


def test_method_delete_link_not_exists(normalized_topology_example, capsys):
    """Проверка работы метода delete_link - проверка удаления несуществующего линка"""
    norm_top = Topology(normalized_topology_example)
    norm_top.delete_link(("R8", "Eth0/2"), ("R9", "Eth0/1"))
    out, err = capsys.readouterr()
    link_msg = "Такого соединения нет"
    assert (
        link_msg in out
    ), "При удалении несуществующего соединения, не было выведено сообщение 'Такого соединения нет'"
```

---
### Пример теста для класса

```python
def test_method__add__(normalized_topology_example):
    """Проверка наличия метода __add__ и его работы"""
    top1 = Topology(normalized_topology_example)
    top1_size_before_add = len(top1.topology)
    top2 = Topology(
        {("R1", "Eth0/4"): ("R7", "Eth0/0"), ("R1", "Eth0/6"): ("R9", "Eth0/0")}
    )
    top2_size_before_add = len(top2.topology)

    top3 = top1 + top2
    assert isinstance(
        top3, Topology
    ), "Метод __add__ должен возвращать новый экземпляр класса Topology"
    assert len(top3.topology) == 8
    assert (
        len(top1.topology) == top1_size_before_add
    ), "После сложения изменился размер первой топологии. Метод __add__ не должен менять исходные топологии"
    assert (
        len(top2.topology) == top2_size_before_add
    ), "После сложения изменился размер второй топологии. Метод __add__ не должен менять исходные топологии"
```

---
### [pytest.ini](https://docs.pytest.org/en/6.2.x/reference.html#ini-options-ref)

```
[pytest]
minversion = 6.0
addopts = -ra -q
testpaths =
    tests
    integration
```
