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
    print("A менше чи рівне B")
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
## Рядки (Strings)

---
### Рядки

Рядок в Python:

* послідовність символів у лапках
* незмінний упорядкований тип даних

```python
In [9]: 'Hello'
Out[9]: 'Hello'

In [10]: "Hello"
Out[10]: 'Hello'
```

---
### Рядки

```python
tunnel = """
interface Tunnel0
 ip address 10.10.10.1 255.255.255.0
 ip mtu 1416
 ip ospf hello-interval 5
 tunnel source FastEthernet1/0
 tunnel protection ipsec profile DMVPN
"""

In [12]: tunnel
Out[12]: '\ninterface Tunnel0\n ip address 10.10.10.1 255.255.255.0\n ip mtu 1416\n ip ospf hello-interval 5\n tunnel source FastEthernet1/0\n tunnel protection ipsec profile DMVPN\n'

In [13]: print(tunnel)

interface Tunnel0
 ip address 10.10.10.1 255.255.255.0
 ip mtu 1416
 ip ospf hello-interval 5
 tunnel source FastEthernet1/0
 tunnel protection ipsec profile DMVPN
```

---
### Методи для роботи з рядками

```python
In [1]: line = "test"

In [2]: line.
          capitalize   find         isdecimal    istitle      partition    rstrip       translate
          casefold     format       isdigit      isupper      replace      split        upper
          center       format_map   isidentifier join         rfind        splitlines   zfill
          count        index        islower      ljust        rindex       startswith
          encode       isalnum      isnumeric    lower        rjust        strip
          endswith     isalpha      isprintable  lstrip       rpartition   swapcase
          expandtabs   isascii      isspace      maketrans    rsplit       title
```

---
## Список (List)

---
### Список

Список у Python це:

* послідовність елементів, розділених між собою комою та укладених у квадратні дужки
* змінюваний упорядкований тип даних

```python
In [1]: list1 = [10, 20, 30, 77]

In [2]: list2 = ['one', 'dog', 'seven']

In [3]: list3 = [1, 20, 4.0, 'word']
```

---
### Список

Так як список - це впорядкований тип даних, то, як і в рядках, у списках можна звертатися до елемента за номером, робити зрізи:

```python
In [4]: list3 = [1, 20, 4.0, 'word']

In [5]: list3[1]
Out[5]: 20

In [6]: list3[1::]
Out[6]: [20, 4.0, 'word']

In [7]: list3[-1]
Out[7]: 'word'

In [8]: list3[::-1]
Out[8]: ['word', 4.0, 20, 1]
```

---
### Список

Оскільки список є змінним типом даних, елементи списку можна змінювати:

```python
In [13]: list3
Out[13]: [1, 20, 4.0, 'word']

In [14]: list3[0] = 'test'

In [15]: list3
Out[15]: ['test', 20, 4.0, 'word']
```

---
### Список

Можна також створювати список списків. І, як і у звичайному списку, можна звертатися до елементів у вкладених списках:

```python
interfaces = [
    ['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up'],
    ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'],
    ['FastEthernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']
]

In [17]: interfaces[0][0]
Out[17]: 'FastEthernet0/0'

In [18]: interfaces[2][0]
Out[18]: 'FastEthernet0/2'

In [19]: interfaces[2][1]
Out[19]: '10.0.2.1'
```

---
### Методи списків

```
In [2]: vlans = [1, 2, 3, 4, 10, 11, 12, 100]

In [3]: vlans.
               append()  count()   insert()  reverse()
               clear()   extend()  pop()     sort()
               copy()    index()   remove()
```


---

## if/elif/else

---
### if/elif/else

```python
a = 9

if a == 10:
    print('a дорівнює 10')
```


```python
a = 9

if a == 10:
    print('a дорівнює 10')
else:
    print('a не дорівнює 10')
```


```python
a = 9

if a == 10:
    print('a дорівнює 10')
elif a < 10:
    print('a менше 10')
else:
    print('a більше 10')
```

```python
a = 9

if a == 10:
    print('a дорівнює 10')
elif a < 10:
    print('a менше 10')
elif a > 10:
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

У цьому прикладі цикл проходить за списком VLAN:

```python
In [3]: vlans = [10, 20, 30, 40, 100]

In [4]: for vlan in vlans:
   ...:     print(vlan)
   ...:     
10
20
30
40
100
```


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
In [2]: for i in [0, 1, 2, 3, 4, 5, 6, 7]:
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

