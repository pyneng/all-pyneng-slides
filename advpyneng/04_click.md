# Создание CLI с помощью Click

---
## click

Click это модуль, который позволяет создавать интерфейс командной строки. Примеры того, что позволяет делать модуль:

* создавать аргументы и опции, с которыми может вызываться скрипт
* указывать типы аргументов, значения по умолчанию
* отображать сообщения с подсказками по использованию скрипта

---
## click

```
$ python ping_ip_click.py --help
Usage: ping_ip_click.py [OPTIONS] IP_ADDRESS

Options:
  -c, --count INTEGER  [default: 3]
  --help               Show this message and exit.
```

---
### Альтернативы click

* [argparse](https://docs.python.org/3/library/argparse.html)
* [typer](https://github.com/tiangolo/typer)
* [docopt](https://github.com/docopt/docopt)

---
### argparse

```python
def main():
    parser = argparse.ArgumentParser(description="Ping script")

    parser.add_argument("host", help="IP or name to ping")
    parser.add_argument(
        "-c",
        dest="count",
        default=2,
        type=int,
        help="Number of packets",
    )
    args = parser.parse_args()

    reply = ping_ip(args.host, args.count)
    if reply:
        print(f"IP address {ip_address} is reachable")
    else:
        print(f"IP address {ip_address} is unreachable")


if __name__ == "__main__":
    main()
```

---
### click

```python
@click.command()
@click.argument("ip_address")
@click.option("--count", "-c", default=2, type=int, help="Number of packets")
def cli(ip_address, count):
    """
    Ping IP_ADDRESS
    """
    reply = ping_ip(ip_address, count)
    if reply:
        print(f"IP address {ip_address} is reachable")
    else:
        print(f"IP address {ip_address} is unreachable")


if __name__ == "__main__":
    cli()
```

---
### typer

```python
def cli(ip_address: str, count: int = 3):
    """
    Ping IP_ADDRESS
    """
    reply = ping_ip(ip_address, count)
    if reply:
        print(f"IP address {ip_address} is reachable")
    else:
        print(f"IP address {ip_address} is unreachable")


if __name__ == "__main__":
    typer.run(cli)
```


---
### docopt

```python
"""
Ping IP_ADDRESS

Usage:
  example_01_ping_function.py -c <count> IP_ADDRESS

Options:
  -c, --count INTEGER  Number of packets
  --help               Show this message and exit.
"""
from docopt import docopt


if __name__ == '__main__':
    arguments = docopt(__doc__)
    print(arguments)
```

---
### click

В click описание интерфейса командной строки (CLI) построено на декораторах:

* аргументы создаются с помощью декоратора click.argument
* опции с помощью декоратора click.option

---
### click

```python
@click.command()
@click.argument("ip_address")
@click.option("--count", "-c", default=2, type=int, help="Number of packets")
def cli(ip_address, count):
    """
    Ping IP_ADDRESS
    """
    reply = ping_ip(ip_address, count)
    if reply:
        print(f"IP address {ip_address} is reachable")
    else:
        print(f"IP address {ip_address} is unreachable")


if __name__ == "__main__":
    cli()
```

---
## Параметры

Click поддерживает два вида параметров: опции и аргументы. В click аргументы имеют больше ограничений.

Возможности доступные только в опциях:

* запрос ввода значения опции у пользователя
* опции могут использоваться как флаги
* значения опций можно считывать из переменных окружения м автоматическим префиксом
* опциям можно писать help

Возможности доступные только в аргументах: передача любого количества значений


---
## Типы параметров

По умолчанию тип параметра будет строкой str, но его можно задавать явно или получать косвенно с помощью значения по умолчанию.

Доступные типы (большинство типов показаны в примерах опций):

* базовые типы: str, int, float, bool
* click.File - специальный тип, который автоматически открывает и закрывает файл. Возвращает открытый файл
* click.Path - тип для проверки пути, файл это или каталог и подобного. Возвращает строку, не открытый файл
* click.Choice - набор допустимых значений
* click.IntRange - диапазон числовых значений
* click.DateTime - преобразует строку с датой в объект datetime

Также можно создавать свои типы данных

---
## Аргументы

Аргументы создаются с помощью декоратора @click.argument:

```python
@click.argument(name, type=None, required=True, default=None, nargs=None)
```

---
## Аргументы

В самом простом случае, достаточно указать только имя аргумента:

```python
@click.command()
@click.argument("name")
def cli(name):
    """Print NAME"""
    print(name)
```

Так будет выглядеть help скрипта:
```
$ python basics_01.py --help
Usage: basics_01.py [OPTIONS] NAME

  Print NAME

Options:
  --help  Show this message and exit.
```

---
## Переменное количество аргументов

Параметр nargs позволяет контролировать какое количество аргументов можно передать.
По умолчанию, значение 1. Значение -1 - это специальное значение обозначающее,
что аргументов может быть сколько угодно.

```python
@click.command()
@click.argument("ip_addresses", nargs=-1, required=True)
@click.option("--count", "-c", default=2, type=int, help="Number of packets")
def main(ip_addresses, count):
    """
    Ping IP_ADDRESS
    """
    reachable, unreachable = ping_ip_addresses(ip_addresses, count)
    for ip in reachable:
        click.secho(f"IP-адрес {ip:15} пингуется", fg="green", bold=True)
    for ip in unreachable:
        click.secho(f"IP-адрес {ip:15} не пингуется", fg="red", bold=True)
```

```
$ python example_03_ping_ip_list_progress_bar.py 8.8.8.8 8.8.4.4 10.1.1.1 192.168.100.1
IP-адрес 8.8.8.8         пингуется
IP-адрес 8.8.4.4         пингуется
IP-адрес 192.168.100.1   пингуется
IP-адрес 10.1.1.1        не пингуется
```

---
## Опции

```python
class click.Option(
    param_decls=None,
    show_default=False,
    prompt=False,
    confirmation_prompt=False,
    hide_input=False,
    is_flag=None,
    flag_value=None,
    multiple=False,
    count=False,
    allow_from_autoenv=True,
    type=None,
    help=None,
    hidden=False,
    show_choices=True,
    show_envvar=False,
    **attrs
)
```

---
### Имена опций:

* ``@click.option("-f", "--foo-bar")``, имя параметра функции будет "foo_bar"
* ``@click.option("-x")``, имя "x"
* ``@click.option("-f", "--filename", "dest")``, имя "dest"
* ``@click.option("--CamelCase")``, имя "camelcase"
* ``@click.option("-f", "-fb")``, имя "f"
* ``@click.option("--f", "--foo-bar")``, имя "f"
* ``@click.option("---f")``, имя "_f"

---
## Запрос значения у пользователя

Запрос выполняется только если в опции не указано значение:

```python
@click.command()
@click.option("--username", "-u", prompt=True)
@click.option("--password", "-p", prompt=True, hide_input=True)
@click.option("--secret", "-s", prompt=True, hide_input=True)
def cli(username, password, secret):
    pass
```

---
### Переменные окружения

```python
@click.command()
@click.option("--username", "-u", envvar="NET_USER")
@click.option("--password", "-p", envvar="NET_PASSWORD")
@click.option("--secret", "-s", envvar="NET_SECRET")
def cli(username, password, secret):
    pass
```

---
### Переменные окружения + prompt

Переменные окружения + prompt - сначала проверяется переменная окружения, потом, если ее нет, запрос у пользователя:

```python
@click.command()
@click.option("--username", "-u", envvar="NET_USER", prompt=True)
@click.option("--password", "-p", envvar="NET_PASSWORD", prompt=True, hide_input=True)
@click.option("--secret", "-s", envvar="NET_SECRET", prompt=True, hide_input=True)
def cli(username, password, secret):
    pass
```

---
### Флаг

```python
@click.option("--show-all", "-a", is_flag=True, help="show db content")
```

---
### Подтверждение ввода

confirmation_prompt может пригодится при запросе пароля или других критичных данных.
В этом случае пароль запрашивается повторно автоматически и два введенных значения сравниваются:

```python
@click.command()
@click.option("--username", "-u", prompt=True)
@click.option("--password", "-p", prompt=True, hide_input=True, confirmation_prompt=True)
def cli(username, password):
    print(username, password)
```

Так как это распространенная задача, ввод пароля таким образом можно заменить декоратором click.password_option.

---
### Дополнительные возможности click

* click.password_option
* click.confirmation_option
* click.version_option
* click.echo
* click.style
* click.secho
* click.clear
* click.pause
* click.prompt
* click.confirm
* click.echo_via_pager
* click.progressbar
