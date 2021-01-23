# Python для сетевых инженеров 

---

## Основы Python

---

### Python

[Wikipedia](https://ru.wikipedia.org/wiki/Python):

* Python - высокоуровневый язык программирования общего назначения, ориентированный на повышение производительности разработчика и читаемости кода.

---

### Стандартная библиотека Python

* [Python Module Index](https://docs.python.org/3/py-modindex.html)
* [The Python Standard Library](https://docs.python.org/3/library/index.html)
* [Python 3 Module of the Week](https://pymotw.com/3/)

---
## Система управления пакетами pip

---
### pip (Pip Installs Packages)

pip - это система управления пакетами, которая используется для установки пакетов из Python Package Index (PyPi).


---
### Установка модулей

Для установки модулей используется команда pip install:
```
$ pip install tabulate

Collecting tabulate
Installing collected packages: tabulate
Successfully installed tabulate-0.8.1
```
---
### Удаление и обновление

Удаление пакета выполняется таким образом:
```
$ pip uninstall tabulate
```

Обновление пакета:
```
$ pip install --upgrade netmiko
```

---
### pip или pip3

В зависимости от того как установлен и настроен Python в системе, может потребоваться использовать pip3, вместо pip.

Чтобы проверить какой вариант используется, надо выполнить команду ```pip --version```.
Вариант, когда pip соответствует Python 2.7:
```
$ pip --version
pip 9.0.1 from /usr/local/lib/python2.7/dist-packages (python 2.7)
```

А pip3 соответствует Python3.4:
```
$ pip3 --version
pip 1.5.6 from /usr/lib/python3/dist-packages (python 3.4)
```

---
### pip или pip3

Также можно использовать альтернативный вариант вызова pip:
```
$ python3.7 -m pip install tabulate
```

Таким образом всегда понятно для какой именно версии Python устанавливается пакет.

---
## Виртуальные окружения

---
### Виртуальные окружения

* позволяют изолировать различные проекты
* зависимости, которые нужны разным проектам, находятся в разных местах - например, если в проекте 1 требуется пакет версии 1.0, а в проекте 2 требуется тот же пакет, но версии 3.1
* пакеты, которые установлены в виртуальных окружениях, не перебивают глобальные пакеты

---
### virtualenvwrapper

Позволяет немного проще работать с виртуальными окружениями.

Установка virtualenvwrapper с помощью pip:
```
sudo pip install virtualenvwrapper
```

После установки в ```.bashrc``` нужно добавить несколько строк
```
export WORKON_HOME=~/venv

. /usr/local/bin/virtualenvwrapper.sh
```

---
### Работа с виртуальными окружениями

Создание нового виртуального окружения, в котором Python 3.7 используется по умолчанию:
```
$ mkvirtualenv --python=/usr/local/bin/python3.7 pyneng
New python executable in PyNEng/bin/python
Installing distribute........................done.
Installing pip...............done.
(pyneng)$ 
```

---
### Работа с виртуальными окружениями


Теперь в том каталоге, который был указан в WORKON_HOME, создан каталог PyNEng:
```
(pyneng)$ ls -ls venv
total 52
....
4 -rwxr-xr-x 1 nata nata   99 Sep 30 16:41 preactivate
4 -rw-r--r-- 1 nata nata   76 Sep 30 16:41 predeactivate
4 -rwxr-xr-x 1 nata nata   91 Sep 30 16:41 premkproject
4 -rwxr-xr-x 1 nata nata  130 Sep 30 16:41 premkvirtualenv
4 -rwxr-xr-x 1 nata nata  111 Sep 30 16:41 prermvirtualenv
4 drwxr-xr-x 6 nata nata 4096 Sep 30 16:42 pyneng
```

---
### Работа с виртуальными окружениями

Выйти из виртуального окружения:
```
(pyneng)$ deactivate 
$ 
```

Для перехода в созданное виртуальное окружение надо выполнить команду __workon__:
```
$ workon pyneng
(pyneng)$ 
```

---
### Работа с виртуальными окружениями

Если необходимо перейти из одного виртуального окружения в другое, то необязательно делать __deactivate__, можно перейти сразу через workon:
```
$ workon Test
(Test)$ workon pyneng
(pyneng)$ 
```

Если виртуальное окружение нужно удалить, используется команда __rmvirtualenv__:
```
$ rmvirtualenv Test
Removing Test...
$ 
```

---
## Синтаксис Python

---
## Синтаксис Python

Первое, что, как правило, бросается в глаза, если говорить о синтаксисе Python - это то, что отступы имеют значение:
* они определяют, какие выражения попадают в блок кода
* когда блок кода заканчивается

---
## Синтаксис Python

```python
a = 10
b = 5

if a > b:
    print("A больше B")
    print(a - b)
else:
    print("B больше или равно A")
    print(b - a)

print("The End")

def open_file(filename):
    print("Reading file", filename)
    with open(filename) as f:
        return f.read()
        print("Done")
```

---
## Синтаксис Python

Несколько правил и рекомендаций:
* В качестве отступов могут использоваться Tab или пробелы. 
  * лучше использовать пробелы, а точнее, настроить редактор так, чтобы Tab был равен 4 пробелам (тогда при использовании Tab будут ставиться 4 пробела)
* Количество пробелов должно быть одинаковым в одном блоке.
  * лучше, чтобы количество пробелов было одинаковым во всем коде
  * популярный вариант - использовать 2-4 пробела (в курсе используются 4 пробела)

---
### Комментарии

Комментарии в Python могут быть однострочными:
```python
# Очень важный комментарий
a = 10
b = 5 # Очень нужный комментарий
```

Многострочный комментарий:
```python
"""
Очень важный
и длинный комментарий
"""
a = 10
b = 5
```

---
## Интерпретатор IPython

---
### IPython

iPython позволяет намного больше, чем стандартный интерпретатор, который вызывается по команде python.

Несколько примеров (возможности ipython намного шире):
* автопродолжение команд по Tab или подсказка, если вариантов команд несколько
* более структурированный и понятный вывод команд
* автоматические отступы в циклах и других объектах
* история выполнения команд
  * по ней можно передвигаться
  * или посмотреть "волшебной" командой %history

---
### IPython

Установить iPython можно с помощью pip (установка в виртуальном окружении):
```
pip install ipython
```

После этого зайти в ipython можно таким образом:
```
$ ipython
Python 3.7.3 (default, May 13 2019, 15:44:23)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.5.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]:
```

---
### IPython

```python
In [1]: 1 + 2
Out[1]: 3

In [2]: 22*45
Out[2]: 990

In [3]: 2**3
Out[3]: 8
```

---
### IPython

В iPython ввод и вывод подписаны:
* In - это то, что написал пользователь
* Out - это вывод команды (если он есть)
* Числа после In и Out - это нумерация выполненных команд в текущей сессии iPython
 
Пример вывода строки:
```python
In [4]: print('Hello!')
Hello!
```

---
### IPython

Когда в интерпретаторе создается, например, цикл, то внутри цикла приглашение меняется на ```...```.
Для выполнения цикла и выхода из этого подрежима необходимо дважды нажать Enter:
```python
In [5]: for i in range(5):
   ...:     print(i)
   ...:     
0
1
2
3
4
```

---
### help

В ipython есть возможность посмотреть help по какому-то объекту, функции или методу:
```python
In [1]: help(str)
Help on class str in module builtins:
 
class str(object)
 |  str(object='') -> str
 |  str(bytes_or_buffer[, encoding[, errors]]) -> str
 |
 |  Create a new string object from the given object. If encoding or
 |  errors is specified, then the object must expose a data buffer
 |  that will be decoded using the given encoding and error handler.
...
 
In [2]: help(str.strip)
Help on method_descriptor:
 
strip(...)
    S.strip([chars]) -> str
 
    Return a copy of the string S with leading and trailing
    whitespace removed.
    If chars is given and not None, remove characters in chars instead.
```

---
### help

Второй вариант:
```python
In [3]: ?str
Init signature: str(self, /, *args, **kwargs)
Docstring:
str(object='') -> str
str(bytes_or_buffer[, encoding[, errors]]) -> str
 
Create a new string object from the given object. If encoding or
errors is specified, then the object must expose a data buffer
that will be decoded using the given encoding and error handler.
Otherwise, returns the result of object.__str__() (if defined)
or repr(object).
encoding defaults to sys.getdefaultencoding().
errors defaults to 'strict'.
Type:           type
 
In [4]: ?str.strip
Docstring:
S.strip([chars]) -> str
 
Return a copy of the string S with leading and trailing
whitespace removed.
If chars is given and not None, remove characters in chars instead.
Type:      method_descriptor
```

---
### print

Функция print позволяет вывести информацию на стандартный поток вывода.

Если необходимо вывести строку, то ее нужно обязательно заключить в кавычки (двойные или одинарные). Если же нужно вывести, например, результат вычисления или просто число, то кавычки не нужны:
```python
In [6]: print('Hello!')
Hello!

In [7]: print(5*5)
25
```

---
### print

Если нужно вывести несколько значений, можно перечислить их через запятую:
```python
In [8]: print(1*5, 2*5, 3*5, 4*5)
5 10 15 20

In [9]: print('one', 'two', 'three')
one two three
```
 

---
## Переменные

---
### Переменные

Переменные в Python:
* не требуют объявления типа переменной (Python - язык с динамической типизацией)
* являются ссылками на область памяти

Правила именования переменных:
* имя переменной может состоять только из букв, цифр и знака подчеркивания
* имя не может начинаться с цифры
* имя не может содержать специальных символов @, $, %

---
### Переменные

```python
In [1]: a = 3

In [2]: b = 'Hello'

In [3]: c, d = 9, 'Test'

In [4]: print(a,b,c,d)
3 Hello 9 Test
```

Обратите внимание, что в Python не нужно указывать, что a это число, а b это строка.

---
### Переменные

Переменные являются ссылками на область памяти. Это можно продемонстрировать с помощью функции ```id()```, которая показывает идентификатор объекта:
```python
In [5]: a = b = c = 33

In [6]: id(a)
Out[6]: 31671480

In [7]: id(b)
Out[7]: 31671480

In [8]: id(c)
Out[8]: 31671480
```

---
### Переменные

С числами у Python есть ещё одна особенность, которая может немного сбить.
Числа от -5 до 256 заранее созданы и хранятся в массиве (списке).
Поэтому при создании числа из этого диапазона фактически создается ссылка на число в созданном массиве.

Это можно проверить таким образом:
```python
In [9]: a = 3

In [10]: b = 3

In [11]: id(a)
Out[11]: 4400936168

In [12]: id(b)
Out[12]: 4400936168

In [13]: id(3)
Out[13]: 4400936168
```

---
### Переменные

У ```a```, ```b``` и числа ```3``` одинаковые идентификаторы.
Все они просто являются ссылками на существующее число в списке.

Но если сделать то же самое с числом больше 256, идентификаторы у всех разные:
```python
In [14]: a = 500

In [15]: b = 500

In [16]: id(a)
Out[16]: 140239990503056

In [17]: id(b)
Out[17]: 140239990503032

In [18]: id(500)
Out[18]: 140239990502960
```

---
### Переменные

При этом, если сделать присваивание такого вида:
```python
In [19]: a = b = c = 500
```

Идентификаторы будут у всех одинаковые:
```python
In [20]: id(a)
Out[20]: 140239990503080

In [21]: id(b)
Out[21]: 140239990503080

In [22]: id(c)
Out[22]: 140239990503080
```

---
### Имена переменных

В Python есть рекомендации по именованию функций, классов и переменных:
* имена переменных обычно пишутся полностью большими или маленькими буквами
  * DB_NAME
  * db_name
* имена функций задаются маленькими буквами, с подчеркиваниями между словами
  * get_names
* имена классов задаются словами с заглавными буквами, без пробелов
