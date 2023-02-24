# Python для мережевих інженерів


---
## Основи Python

* Синтаксис
* Типи даних (int, str, list)
* Функції print, input, len, sorted
* Умови if/else
* Цикл for

---
## Синтаксис Python

---
## Синтаксис Python

Перше, що, як правило, всі помічають, якщо говорити про синтаксис Python - це
те, що відступи мають значення:
* вони визначають, які вирази потрапляють у блок коду
* коли блок коду закінчується

---
## Синтаксис Python

```python
a = 10
b = 5

if a > b:
    print("A більше B")
    print(a - b)
else:
    print("B більше чи рівне A")
    print(b - a)

print("The End")

words = ["test", "line", "hello"]
for word in words:
    print(word.upper())
```

---
## Синтаксис Python

Декілька правил та рекомендацій:

* Як відступи можуть використовуватися Tab або пробіли.
  * краще використовувати пробіли, а точніше, налаштувати редактор так, щоб Tab дорівнював 4 пробілам (тоді при використанні Tab будуть ставитися 4 пробіли)
* Кількість пробілів має бути однаковою в одному блоці.
  * краще, щоб кількість пробілів була однаковою у всьому коді
  * популярний варіант - використовувати 4 пробіли (у курсі використовуються 4 пробіли)

---
## Змінні

---
### Змінні

Змінні в Python:

* не вимагають оголошення типу змінної (Python – мова з динамічною типізацією)
* є посиланнями на область пам'яті


Правила іменування змінних:

* ім'я змінної може складатися тільки з літер, цифр та знака підкреслення
* ім'я не може починатися з цифри
* ім'я не може містити спеціальних символів @, $, %

---
### Змінні

```python
In [1]: a = 3

In [2]: b = 'Hello'

In [3]: c, d = 9, 'Test'

In [4]: print(a, b, c, d)
3 Hello 9 Test
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

### Імена змінних

У Python є рекомендації щодо іменування функцій, класів та змінних:

* імена змінних зазвичай пишуться повністю великими чи маленькими літерами: ``DB_NAME``, ``db_name``
* імена функцій задаються маленькими літерами, з підкресленням між словами: ``get_names``
* імена класів задаються словами з великими літерами: ``JuniperDevice``

---
### Коментарі

Коментарі в Python можуть бути однорядковими:

```python
# some text
a = 10
b = 5 # another text
```

Багаторядковий коментар:

```python
"""
a line drawn under a word
or phrase for emphasis
"""
a = 10
b = 5
```


---
### print

Функція print дозволяє вивести інформацію на стандартний потік виведення (на екран).

```python
In [6]: print('Hello!')
Hello!

In [7]: print(5*5)
25
```

---
### print

Якщо потрібно вивести кілька значень, можна записати їх через кому:

```python
In [8]: print(1, 2, 3, 4)
1 2 3 4

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
    print('a дорівнює 10')
elif a < 10:
    print('a менше 10')
else:
    print('a більше 10')
```

---
### True и False

* False (хибність)
 * 0
 * None
 * порожній рядок
 * порожній список, кортеж, словник, множина
* True (істина)
 * все інше

---
### Оператори порівняння

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
### Оператори and, or, not

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
### Приклад використання конструкції if/elif/else

check_password.py:
```python
username = input('Username: ')
password = input('Password: ')

if len(password) < 8:
    print('Password is too short')
elif username in password:
    print('Password contains username')
else:
    print('Password set for user' + username)
```

---
## for

---
### for

Цикл for проходить по зазначеній послідовності та виконує дії, які вказані в блоці for.

Приклади послідовностей елементів, якими може проходити цикл for:

* рядок
* список
* словник
* функція range
* будь-який ітерований об'єкт

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

Приклад циклу for з функцією range:

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

У цьому прикладі цикл проходить за списком VLAN:

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

