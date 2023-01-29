---
## Декоратор

Декоратор в Python это функция (или класс), которая используется для изменения
функции, метода или класса.

Упрощенный вариант:
Декоратор в Python это функция, которая используется для изменения функции.
Декораторы используются для добавления какого-то функционала к функциям.

Синтаксис декоратора - это синтаксический сахар,
эти два определения функций эквивалентны:
```python
def f(...):
    ...
f = verbose(f)


@verbose
def f(...):
    ...
```

---
### Примеры декораторов

```python
def verbose(func):
    def wrapper(*args, **kwargs):
        print(f'Вызываю функцию {func.__name__}')
        return func(*args, **kwargs)
    return wrapper

@verbose
def upper(string):
    return string.upper()

@verbose
def lower(string):
    return string.lower()

@verbose
def capitalize(string):
    return string.capitalize()
```

---
### Примеры декораторов

```python
@pytest.fixture(scope='module')
def first_router_from_devices_yaml():
    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)
        r1 = devices[0]
    return r1
```


---
### Примеры декораторов

```python
@click.command()
@click.option("--username", "-u", prompt=True)
@click.option("--password", "-p", prompt=True, hide_input=True, confirmation_prompt=True)
def cli(username, password):
    pass
```


---
### Примеры декораторов

```python
@main.route('/')
def index():
    pass


@main.route('/labs', methods=['GET', 'POST'])
def labs():
    pass


@main.route('/stats')
def stats():
    pass
```

---
### Примеры декораторов

```python
@main.route('/labs', methods=['GET', 'POST'])
@login_required
def labs():
    pass

@main.route('/stats')
@login_required
@admin_required
def stats():
    pass
```

---
### Примеры декораторов

```python
class IPAddress:
    def __init__(self, address, mask):
        self._address = address
        self._mask = int(mask)

    @property
    def mask(self):
        return self._mask
```


---
### Примеры декораторов

```python
@dataclass
class IPAddress:
    ip: str
    mask: int
```

---
### Примеры декораторов

[backoff](https://github.com/litl/backoff)

```python
@backoff.on_exception(
    backoff.expo, requests.exceptions.RequestException
)
def get_url(url):
    return requests.get(url)
```

---
## Применение нескольких декораторов

К функции может применяться несколько декораторов. Порядок применения
декораторов будет зависеть от того в каком порядке они записаны.

