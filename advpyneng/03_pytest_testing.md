# Advanced Python для сетевых инженеров
## Тестирование кода и сети с помощью pytest

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
        send_show_command(first_router_from_devices_yaml, "sh ip int br")
    )
    assert (
        correct_return_value == return_value
    ), "Функция возвращает неправильное значение"
```

---
### Параметризация теста

```python
@pytest.mark.parametrize(
    ("user", "passwd", "min_len", "result"),
    [
        ("user1", "123456", 4, True),
        ("user1", "123456", 8, False),
        ("user1", "123456", 6, True),
    ],
)
def test_min_len_param(user, passwd, min_len, result):
    assert check_passwd(user, passwd, min_length=min_len) == result
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
### pytest fixture

```python
@pytest.fixture(scope='module')
def first_router_from_devices_yaml():
    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)
        r1 = devices[0]
        #options = {'timeout': 5, 'fast_cli': True}
        r1.update(options)
    return r1


@pytest.fixture(scope='module')
def r1_test_connection(first_router_from_devices_yaml):
    with ConnectHandler(**first_router_from_devices_yaml) as r1:
        r1.enable()
        yield r1
```

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

```python
with open("devices.yaml") as f:
    devices_params = yaml.safe_load(f)
ip_list = [d["host"] for d in devices_params]


@pytest.fixture(params=devices_params, scope="session", ids=ip_list)
def ssh_connection(request):
    with ConnectHandler(**request.param) as ssh:
        ssh.enable()
        yield ssh


def test_ospf_enabled(ssh_connection):
    output = ssh_connection.send_command("sh ip ospf")
    assert "routing process" in output.lower()


def test_loopback(ssh_connection):
    output = ssh_connection.send_command("sh ip int br | i up +up")
    assert "Loopback0" in output
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
platform linux -- Python 3.7.3, pytest-5.2.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/vagrant/repos/advanced-pyneng-1/advpyneng-online-oct-nov-2019/examples/14_pytest_basics
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
    monkeypatch.setattr('getpass.getpass', lambda x=None: '12345')
    assert check_passwd(min_length=3)


@pytest.mark.parametrize("username,password,result",[
    ('nata', '12345', True),
    ('nata', '12345nata', False)
])
def test_password_min_length(monkeypatch,
                             username, password, result):
    monkeypatch.setattr('builtins.input', lambda x=None: username)
    monkeypatch.setattr('getpass.getpass', lambda x=None: password)
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
