# Декораторы (повторение)

---
## Декоратор

Декоратор в Python это функция, которая используется для изменения
функции, метода или класса.
Декораторы используются для добавления какого-то функционала к функциям/классам.

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
## functools.wraps

При использовании декораторов, информация исходной функции
заменяется внутренней функцией декоратора:

```python
In [2]: lower
Out[2]: <function __main__.verbose.<locals>.wrapper(*args, **kwargs)>

In [4]: lower?
Signature: lower(*args, **kwargs)
Docstring: <no docstring>
File:      ~/repos/experiments/netdev_try/<ipython-input-1-32089045b87b>
Type:      function
```

---
## functools.wraps

Чтобы исправить это необходимо воспользоваться декоратором wraps
из модуля functools:

```python
from functools import wraps

def verbose(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f'Вызываю функцию {func.__name__}')
        return func(*args, **kwargs)
    return wrapper


@verbose
def lower(string):
    return string.lower()


In [7]: lower
Out[7]: <function __main__.lower(string)>

In [8]: lower?
Signature: lower(string)
Docstring: <no docstring>
File:      ~/repos/experiments/netdev_try/<ipython-input-6-13e6266ce16f>
Type:      function
```

---
## Применение нескольких декораторов

К функции может применяться несколько декораторов. Порядок применения
декораторов будет зависеть от того в каком порядке они записаны.

---
# Декораторы с аргументами

---
### Примеры декораторов с аргументами

pytest
```python
@pytest.fixture(scope='module')
def first_router_from_devices_yaml():
    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)
        r1 = devices[0]
    return r1
```

click
```python
@click.command()
@click.option("--username", "-u", prompt=True)
@click.option("--password", "-p", prompt=True, hide_input=True, confirmation_prompt=True)
def cli(username, password):
    pass
```


---
### Примеры декораторов с аргументами

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
### Примеры декораторов с аргументами

[backoff](https://github.com/litl/backoff)

```python
@backoff.on_exception(
    backoff.expo, requests.exceptions.RequestException
)
def get_url(url):
    return requests.get(url)
```

---
### Декоратор с аргументом

```python
def restrict_args_type(required_type):
    def decorator(func):
        @wraps(func)
        def wrapper(*args):
            if not all(isinstance(arg, required_type) for arg in args):
                raise ValueError(f'Все аргументы должны быть {required_type.__name__}')
            return func(*args)
        return wrapper
    return decorator
```

Применение

```python
@restrict_args_type(str)
def to_upper(*args):
    result = [s.upper() for s in args]
    return result


In [90]: to_upper('a', 2)
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-90-b46c3ca71e5d> in <module>
----> 1 to_upper('a', 2)

<ipython-input-88-ea0c777e0f6e> in wrapper(*args)
      4             def wrapper(*args):
      5                 if not all(isinstance(arg, required_type) for arg in args):
----> 6                     raise ValueError(f'Все аргументы должны быть {required_type.__name__}')
      7                 return func(*args)
      8             return wrapper

ValueError: Все аргументы должны быть str

In [91]: to_upper('a', 'a')
Out[91]: ['A', 'A']
```

---
### Декоратор с аргументом

```python
@restrict_args_type(int)
def to_bin(*args):
    result = [bin(a) for a in args]
    return result


In [94]: to_bin(1,2,3)
Out[94]: ['0b1', '0b10', '0b11']

In [95]: to_bin('a', 'b')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-95-e4007cc06928> in <module>
----> 1 to_bin('a', 'b')

<ipython-input-88-ea0c777e0f6e> in wrapper(*args)
      4             def wrapper(*args):
      5                 if not all(isinstance(arg, required_type) for arg in args):
----> 6                     raise ValueError(f'Все аргументы должны быть {required_type.__name__}')
      7                 return func(*args)
      8             return wrapper

ValueError: Все аргументы должны быть int
```

---
### Декоратор с аргументом

Также при необходимости можно сделать готовые декораторы для определенных
типов данных:

```python
restrict_args_to_str = restrict_args_type(str)

restrict_args_to_int = restrict_args_type(int)

@restrict_args_to_str
def to_upper(*args):
    result = [s.upper() for s in args]
    return result


@restrict_args_to_int
def to_bin(*args):
    result = [bin(a) for a in args]
    return result
```
