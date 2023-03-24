## Дескриптор

---
## Дескриптор

Дескриптор (descriptor) - любой объект, который определяет методы ``__get__``, ``__set__`` или ``__delete__``.

Когда атрибут класса является дескриптором, запускается особое поведение
при поиске атрибута. 

Обычно использование obj.attr для получения, установки или удаления атрибута
ищет объект с именем attr в словаре класса для obj, но если attr является
дескриптором, вызывается соответствующий метод дескриптора.  

Дескрипторы являются основой большого количества функционала в Python, включая функции,
методы, property, classmethod, staticmethod.

---
## Дескриптор

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

---
### property

```python
class C:
    def __init__(self):
        self._x = None

    def getx(self):
        return self._x

    def setx(self, value):
        self._x = value

    x = property(getx, setx)
```

```python
c1 = C()
c1.x
c1.x = 42
```

---
## Descriptor protocol

```python
descr.__get__(self, obj, type=None) -> value
descr.__set__(self, obj, value) -> None
descr.__delete__(self, obj) -> None
```

---
## [Descriptor protocol](https://docs.python.org/3/howto/descriptor.html#descriptor-protocol)

If an object defines ``__set__`` or ``__delete__``, it is considered a data
descriptor. Descriptors that only define ``__get__`` are called non-data
descriptors (they are often used for methods but other uses are possible).

Data and non-data descriptors differ in how overrides are calculated with
respect to entries in an instance’s dictionary. If an instance’s dictionary has
an entry with the same name as a data descriptor, the data descriptor takes
precedence. If an instance’s dictionary has an entry with the same name as a
non-data descriptor, the dictionary entry takes precedence.

To make a read-only data descriptor, define both ``__get__`` and ``__set__`` with
the ``__set__`` raising an AttributeError when called. Defining the ``__set__``
method with an exception raising placeholder is enough to make it a data
descriptor.


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
def find_name_in_mro(cls, name, default):
    "Emulate _PyType_Lookup() in Objects/typeobject.c"
    for base in cls.__mro__:
        if name in vars(base):
            return vars(base)[name]
    return default


def object_getattribute(obj, name):
    "Emulate PyObject_GenericGetAttr() in Objects/object.c"
    null = object()
    objtype = type(obj)
    cls_var = find_name_in_mro(objtype, name, null)
    descr_get = getattr(type(cls_var), '__get__', null)
    if descr_get is not null:
        if (
            hasattr(type(cls_var), '__set__')
            or hasattr(type(cls_var), '__delete__')
        ):
            return descr_get(cls_var, obj, objtype)     # data descriptor
    if hasattr(obj, '__dict__') and name in vars(obj):
        return vars(obj)[name]                          # instance variable
    if descr_get is not null:
        return descr_get(cls_var, obj, objtype)         # non-data descriptor
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

