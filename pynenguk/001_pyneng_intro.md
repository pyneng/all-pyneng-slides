# Python для сетевых инженеров 

## Основы Python

---
## Основы Python

* Синтаксис
* Типы данных (int, str, list)
* Функции print, input, len, sorted
* Условия if/else
* Цикл for

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

words = ["test", "line", "hello"]
for word in words:
    print(word.upper())
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

In [4]: print(a, b, c, d)
3 Hello 9 Test
```

Обратите внимание, что в Python не нужно указывать, что a это число, а b это строка.

---
### Имена переменных

В Python есть рекомендации по именованию функций, классов и переменных:
* имена переменных обычно пишутся полностью большими или маленькими буквами
  * DB_NAME
  * db_name
* имена функций задаются маленькими буквами, с подчеркиваниями между словами
  * get_names
* имена классов задаются словами с заглавными буквами, без пробелов


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
### print

Функция print позволяет вывести информацию на стандартный поток вывода (на экран).

Если необходимо вывести строку, то ее нужно обязательно заключить в кавычки
(двойные или одинарные). Если же нужно вывести, например, результат вычисления
или просто число, то кавычки не нужны:
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

## if/elif/else

---
### if/elif/else

```python
a = 9

if a == 10:
    print('a равно 10')
elif a < 10:
    print('a меньше 10')
else:
    print('a больше 10')
```

---
### True и False

* True (истина)
 * любое ненулевое число
 * любая непустая строка
 * любой непустой объект
* False (ложь)
 * 0
 * None
 * пустая строка
 * пустой объект

---
### Операторы сравнения

```python
In [3]: 5 > 6
Out[3]: False

In [4]: 5 > 2
Out[4]: True

In [5]: 5 < 2
Out[5]: False

In [6]: 5 == 2
Out[6]: False

In [7]: 5 == 5
Out[7]: True

In [8]: 5 >= 5
Out[8]: True

In [9]: 5 <= 10
Out[9]: True

In [10]: 8 != 10
Out[10]: True
```

---
### Оператор in

```python
In [8]: 'Fast' in 'FastEthernet'
Out[8]: True

In [9]: 'Gigabit' in 'FastEthernet'
Out[9]: False

In [10]: vlan = [10, 20, 30, 40]

In [11]: 10 in vlan
Out[11]: True

In [12]: 50 in vlan
Out[12]: False
```

---
### Операторы and, or, not

```python
In [17]: line = "test line"

In [18]: vlan = [10, 20, 30, 40]

In [19]: 'test' in line and 10 in vlan
Out[19]: True

In [20]: 'hello' in line and 10 in vlan
Out[20]: False

In [21]: 'hello' in line or 10 in vlan
Out[21]: True

In [23]: 'hello' not in line
Out[23]: True
```


---
### Пример использования конструкции if/elif/else

check_password.py:
```python
username = input('Введите имя пользователя: ')
password = input('Введите пароль: ')

if len(password) < 8:
    print('Пароль слишком короткий')
elif username in password:
    print('Пароль содержит имя пользователя')
else:
    print('Установлен пароль для пользователя' + username)
```

---
## for

---
### for

Цикл for проходится по указанной последовательности и выполняет действия, которые указаны в блоке for.

Примеры последовательностей элементов, по которым может проходиться цикл for:

* строка
* список
* словарь
* функция [range](../10_useful_functions/range.md)
* любой итерируемый объект

---
### for

```python
In [1]: for letter in 'Test string':
   ...:     print(letter)
   ...:     
T
e
s
t

s
t
r
i
n
g
```

---
### for

Пример цикла for с функцией range():

```python
In [2]: for i in range(10):
   ...:     print('interface FastEthernet0/' + str(i))
   ...:     
interface FastEthernet0/0
interface FastEthernet0/1
interface FastEthernet0/2
interface FastEthernet0/3
interface FastEthernet0/4
interface FastEthernet0/5
interface FastEthernet0/6
interface FastEthernet0/7
interface FastEthernet0/8
interface FastEthernet0/9
```

---
### for

В этом примере цикл проходит по списку VLANов, поэтому переменную можно назвать vlan:

```python
In [3]: vlans = [10, 20, 30, 40, 100]
In [4]: for vlan in vlans:
   ...:     print('vlan', vlan)
   ...:     print(' name VLAN_' + str(vlan))
   ...:     
vlan 10
 name VLAN_10
vlan 20
 name VLAN_20
vlan 30
 name VLAN_30
vlan 40
 name VLAN_40
vlan 100
 name VLAN_100
```

