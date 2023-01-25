# Advanced Python для сетевых инженеров
## Автоматическое форматирование кода

---
## Линтеры и другие утилиты для анализа кода

* [pycodestyle](https://github.com/PyCQA/pycodestyle)
* [pylint](https://github.com/PyCQA/pylint) 
* [isort - сортировка строк import](https://github.com/PyCQA/isort)
* [Pyflakes](https://github.com/PyCQA/pyflakes)
* [Flake8](https://github.com/PyCQA/flake8) - glues together pycodestyle, pyflakes, mccabe

---
### pycodestyle

pycodestyle is a tool to check your Python code against some of the style conventions in PEP 8.

```
$ pycodestyle --first optparse.py
optparse.py:69:11: E401 multiple imports on one line
optparse.py:77:1: E302 expected 2 blank lines, found 1
optparse.py:88:5: E301 expected 1 blank line, found 0
optparse.py:222:34: W602 deprecated form of raising exception
optparse.py:347:31: E211 whitespace before '('
optparse.py:357:17: E201 whitespace after '{'
optparse.py:472:29: E221 multiple spaces before operator
optparse.py:544:21: W601 .has_key() is deprecated, use 'in'
```

You can also make pycodestyle.py show the source code for each error, and even the relevant text from PEP 8:
```
$ pycodestyle --show-source --show-pep8 testsuite/E40.py
testsuite/E40.py:2:10: E401 multiple imports on one line
import os, sys
         ^
    Imports should usually be on separate lines.

    Okay: import os\nimport sys
    E401: import sys, os
```

Or you can display how often each error was found:
```
$ pycodestyle --statistics -qq Python-2.5/Lib
232     E201 whitespace after '['
599     E202 whitespace before ')'
631     E203 whitespace before ','
842     E211 whitespace before '('
2531    E221 multiple spaces before operator
4473    E301 expected 1 blank line, found 0
4006    E302 expected 2 blank lines, found 1
165     E303 too many blank lines (4)
325     E401 multiple imports on one line
3615    E501 line too long (82 characters)
612     W601 .has_key() is deprecated, use 'in'
1188    W602 deprecated form of raising exception
```


---
### pylint

Pylint is a Python static code analysis tool which looks for programming errors,
helps enforcing a coding standard, sniffs for code smells and offers simple refactoring suggestions.


simplecaesar.py:
```python
#!/usr/bin/env python3

import string;

shift = 3
choice = input("would you like to encode or decode?")
word = input("Please enter text")
letters = string.ascii_letters + string.punctuation + string.digits
encoded = ''
if choice == "encode":
    for letter in word:
        if letter == ' ':
            encoded = encoded + ' '
        else:
            x = letters.index(letter) + shift
            encoded = encoded + letters[x]
if choice == "decode":
    for letter in word:
        if letter == ' ':
            encoded = encoded + ' '
        else:
            x = letters.index(letter) - shift
            encoded = encoded + letters[x]

print(encoded)
```

```
$ pylint simplecaesar.py
************* Module simplecaesar
simplecaesar.py:3:0: W0301: Unnecessary semicolon (unnecessary-semicolon)
simplecaesar.py:1:0: C0114: Missing module docstring (missing-module-docstring)
simplecaesar.py:5:0: C0103: Constant name "shift" doesn't conform to UPPER_CASE naming style (invalid-name)
simplecaesar.py:9:0: C0103: Constant name "encoded" doesn't conform to UPPER_CASE naming style (invalid-name)
simplecaesar.py:13:12: C0103: Constant name "encoded" doesn't conform to UPPER_CASE naming style (invalid-name)

Your code has been rated at 7.37/10
```

---
### pyflakes

```
$ pyflakes ./code.py
./code.py:1: 'pytest' imported but unused
./code.py:20: undefined name 'device_name'
```

---
### flake8

[Flake8](https://github.com/PyCQA/flake8) - flake8 is a python tool that glues together pycodestyle, pyflakes, mccabe

---
## Автоматическое форматирование кода

Варианты автоформатерров

* [black](https://github.com/psf/black)
* [blue](https://github.com/grantjenks/blue)
* [yapf](https://github.com/google/yapf)
* [autopep8](https://github.com/hhatto/autopep8)


---
## Black - бескомпромиссное форматирование кода

---
## Black

Black - бескомпромиссное средство форматирования кода Python. Используя его,
вы соглашаетесь передать контроль над мелочами ручного форматирования. В свою очередь,
Black дает вам скорость, детерминизм и свободу от ворчаний pycodestyle по поводу
форматирования. Вы сэкономите время и умственную энергию для более важных дел.


---
## Black

Установка
```
pip install black
```

Использование:
```
black somefile_or_dir
```

---
## Примеры стиля Black

```python
# in:

j = [1,
     2,
     3
]

# out:

j = [1, 2, 3]
```

---
## Примеры стиля Black

```python
# in:

ImportantClass.important_method(exc, limit, lookup_lines, capture_locals, extra_argument)

# out:

ImportantClass.important_method(
    exc, limit, lookup_lines, capture_locals, extra_argument
)
```

---
## Примеры стиля Black

```python
# in:

def very_important_function(template: str, *variables, file: os.PathLike, engine: str, header: bool = True, debug: bool = False):
    """Applies `variables` to the `template` and writes to `file`."""
    with open(file, 'w') as f:
        ...

# out:

def very_important_function(
    template: str,
    *variables,
    file: os.PathLike,
    engine: str,
    header: bool = True,
    debug: bool = False,
):
    """Applies `variables` to the `template` and writes to `file`."""
    with open(file, "w") as f:
        ...
```


---
## Примеры стиля Black

```python
# in:

if some_short_rule1 \
  and some_short_rule2:
      ...

# out:

if some_short_rule1 and some_short_rule2:
  ...


# in:

if some_long_rule1 \
  and some_long_rule2:
    ...

# out:

if (
    some_long_rule1
    and some_long_rule2
):
    ...
```


---
### Примеры стиля Black

До

```python
# fmt: off
custom_formatting = [
    0,  1,  2,
    3,  4,  5,
    6,  7,  8,
]
# fmt: on
regular_formatting = [
    0,  1,  2,
    3,  4,  5,
    6,  7,  8,
]
```

После
```python
# fmt: off
custom_formatting = [
    0,  1,  2,
    3,  4,  5,
    6,  7,  8,
]
# fmt: on
regular_formatting = [
    0,
    1,
    2,
    3,
    4,
    5,
    6,
    7,
    8,
]
```

---
## Волшебная запятая в конце строки

```python
TRANSLATIONS = {
    "en_us": "English (US)",
    "pl_pl": "polski",
}
```


---
## Настройки

pyproject.toml 

```python
[tool.black]
line-length = 88
target-version = ['py37']
include = '\.pyi?$'
extend-exclude = '''
# A regex preceded with ^/ will apply only to files and directories
# in the root of the project.
^/foo.py  # exclude a file named foo.py in the root of the project (in addition to the defaults)
'''
```

