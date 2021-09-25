# Advanced Python для сетевых инженеров
## Тестирование кода и сети с помощью pytest

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


def test_ospf_enabled(ssh_connection):
    output = ssh_connection.send_command("sh ip ospf")
    assert "routing process" in output.lower()


def test_loopback(ssh_connection):
    output = ssh_connection.send_command("sh ip int br | i up +up")
    assert "Loopback0" in output


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
