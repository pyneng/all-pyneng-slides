## Создание интерфейса командной строки с помощью Typer

---

### Typer

Typer это модуль, который позволяет создавать интерфейс командной строки с помощью аннотации типов.

```python
def main(ip_addresses: List[str], count: int):
    """
    Ping IP_ADDRESS
    """
    for ip in ip_addresses:
        status = ping_ip(ip, count)
        if status:
            print(f"IP-адрес {ip:15} пингуется")
        else:
            print(f"IP-адрес {ip:15} не пингуется")


if __name__ == "__main__":
    typer.run(main)
```

---

### Typer

```
$ python example_typer_01_ping_ip_list.py 8.8.8.8 8.8.4.4
IP-адрес 8.8.8.8         пингуется
IP-адрес 8.8.4.4         пингуется

$ python example_typer_01_ping_ip_list.py --help
Usage: example_typer_01_ping_ip_list.py [OPTIONS] IP_ADDRESSES...

  Ping IP_ADDRESS

Arguments:
  IP_ADDRESSES...  [required]

Options:
  --count INTEGER                 [default: 3]
  --help                          Show this message and exit.
```

---

### Основы аннотации типов

Аннотация типов - это дополнительное описание в классах, функциях, переменных,
которое указывает какой тип данных должен быть в этом месте.

```python
def ping_ip(ip: str) -> bool:
    command = ["ping", "-c", "1", ip]
    reply = subprocess.run(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    if reply.returncode == 0:
        return True
    else:
        return False
```

---

### Основы аннотации типов

При этом указанные типы не проверяются и не форсируются самим Python. То есть, при выполнении кода,
несоответствие реального типа данных тому, что написано в аннотациях, не вызывает ошибок или
предупреждений. Для проверки типов данных используются отдельные модули, например, mypy.
Mypy выполняет статический анализ кода - проверяет соответствие типов данных без выполнения кода.

> Аннотация типов добавлялась постепенно в Python 3.x. Начиная с версии Python 3.0 была доступна
> аннотация функций, а в Python 3.6 была добавлена аннотация для переменных.

---

### Основы аннотации типов

Преимущества:

* при создании объектов сразу описаны типы данных
* можно проверять правильность указанных типов с помощью отдельных модулей
* IDE могут делать подсказки, указывать на ошибки на основании аннотации типов

Нюансы:

* как и с тестами, надо потратить время на написание аннотаций (хотя есть софт, который может в этом помочь)
* на данный момент, надо делать довольно большое количество импортов
* желательно использовать Python 3.6+ чтобы были доступны все возможности, в идеале, последнюю версию Python.

---

### Основы аннотации типов

Пример аннотации функции со значениями по умолчанию:

```python
def check_passwd(username: str, password: str,
                 min_length: int = 8, check_username: bool = True) -> bool:
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

---

### Typer


В typer описание интерфейса командной строки (CLI) построено на аннотации типов.

```python
def main(ip_address: str):
    """
    Ping IP_ADDRESS
    """
    status = ping_ip(ip_address, count=3)
    if status:
        print(f"IP-адрес {ip_address:15} пингуется")
    else:
        print(f"IP-адрес {ip_address:15} не пингуется")


if __name__ == "__main__":
    typer.run(main)
```

---

### Параметры

Typer поддерживает два вида параметров CLI: опции и аргументы.

Добавление аргумента:

```python
def main(ip_address: str, count: int):
    """
    Ping IP_ADDRESS
    """
    status = ping_ip(ip_address, count)
    if status:
        print(f"IP-адрес {ip_address:15} пингуется")
    else:
        print(f"IP-адрес {ip_address:15} не пингуется")


if __name__ == "__main__":
    typer.run(main)
```

Аргумент ip_address обязательный, принимает только одно значение и все что передается
аргументу, воспринимается как строка.

Аргумент count обязательный, принимает только одно значение и все что передается
аргументу, попадает в функцию как число и при попытке передать не число, возникнет ошибка.

---

### Аргументы

```
$ python ex00_ping_ip.py --help
Usage: ex00_ping_ip.py [OPTIONS] IP_ADDRESS COUNT

  Ping IP_ADDRESS

Arguments:
  IP_ADDRESS  [required]
  COUNT       [required]

Options:
  --help                          Show this message and exit.
```

---

### Опции

Самый простой способ создать опцию в typer - добавить значение по умолчанию:

```python
def main(ip_address: str, count: int = 3):
    """
    Ping IP_ADDRESS
    """
    status = ping_ip(ip_address, count)
    if status:
        print(f"IP-адрес {ip_address:15} пингуется")
    else:
        print(f"IP-адрес {ip_address:15} не пингуется")
```

---

### Опции

```
$ python ex01_ping_ip_count_option.py --help
Usage: ex01_ping_ip_count_option.py [OPTIONS] IP_ADDRESS

  Ping IP_ADDRESS

Arguments:
  IP_ADDRESS  [required]

Options:
  --count INTEGER                 [default: 3]
  --help                          Show this message and exit.
```

```
$ python ex01_ping_ip_count_option.py 8.8.8.8
IP-адрес 8.8.8.8         пингуется

$ python ex01_ping_ip_count_option.py 8.8.8.8 --count 1
IP-адрес 8.8.8.8         пингуется

$ python ex01_ping_ip_count_option.py --count 1 8.8.8.8
IP-адрес 8.8.8.8         пингуется
```
