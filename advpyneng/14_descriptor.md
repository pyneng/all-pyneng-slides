## Дескриптор

---
## Дескриптор

Дескрипторы позволяют объектам настраивать получение, запись и удаление атрибутов.

---
## obj.attr

* ``__getattribute__``
* ``__setattr__``
* ``__delattr__``
* ``__getattr__`` - срабатывает только для несуществующих атрибутов


---
## ``__getattribute__``

``object.__getattribute__()``

```python
def getattribute(instance, name):
    "Emulate PyObject_GenericGetAttr() in Objects/object.c"
    null = object()
    cls = type(instance)
    cls_var = getattr(cls, name, null)
    descr_get = getattr(type(cls_var), '__get__', null)
    if descr_get is not null:
        if (hasattr(type(cls_var), '__set__')
            or hasattr(type(cls_var), '__delete__')):
            return descr_get(cls_var, instance, cls)     # data descriptor
    if hasattr(instance, '__dict__') and name in vars(instance):
        return vars(instance)[name]                          # instance variable
    if descr_get is not null:
        return descr_get(cls_var, instance, cls)         # non-data descriptor
    if cls_var is not null:
        return cls_var                                  # class variable
    raise AttributeError(name)
```

---
### Примеры дескрипторов

* property
* classmethod
* staticmethod


---
### Дескриптор Interger

```python
class Integer:

    def __set_name__(self, owner, name):
        print("Integer __set_name__")
        print(f"__setname__ {self=} {owner=} {name=}")
        self.name = name

    def __get__(self, instance, cls):
        print(f"__get__ {self=} {instance=} {cls=}")
        return instance.__dict__[f"_{self.name}"]

    def __set__(self, instance, value):
        if not isinstance(value, int):
            raise ValueError("Значение должно быть числом")
        print(f"__set__ {self=} {instance=} {value=}")
        instance.__dict__[f"_{self.name}"] = value


class IPAddress:
    mask = Integer()

    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask
```

```python
class Integer:
    def __init__(self, name):
        self.name = name

    def __get__(self, instance, cls):
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not isinstance(value, int):
            raise TypeError('Wrong data type, expected int')
        instance.__dict__[self.name] = value
```


Дескриптор обязательно должен быть указан на уровне класса:

```python

class IPAddress:
    mask = Integer('mask')

    def __init__(self, ip, mask):
        self.ip = ip
        self.mask = mask


In [90]: ip1 = IPAddress('10.1.1.1', 28)

In [96]: ip1.mask = '24'
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-96-247b3f37d10f> in <module>
----> 1 ip1.mask = '24'

<ipython-input-93-5812cdd26ed1> in __set__(self, instance, value)
      8         def __set__(self, instance, value):
      9             if not isinstance(value, int):
---> 10                 raise TypeError('Wrong data type, expected int')
     11             instance.__dict__[self.name] = value
     12

TypeError: Wrong data type, expected int

In [97]: ip1.ip = 142
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-97-24102e80dc3a> in <module>
----> 1 ip1.ip = 142

<ipython-input-93-5812cdd26ed1> in __set__(self, instance, value)
     20         def __set__(self, instance, value):
     21             if not isinstance(value, str):
---> 22                 raise TypeError('Wrong data type, expected str')
     23             instance.__dict__[self.name] = value
```

Оптимизированный вариант:

```python

class Typed:
    attr_type = object

    def __init__(self, name):
        self.name = name

    def __get__(self, instance, cls):
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not isinstance(value, self.attr_type):
            raise TypeError(f'Wrong data type, expected {self.attr_type}')
        instance.__dict__[self.name] = value

class Integer(Typed):
    attr_type = int

class String(Typed):
    attr_type = str
```

