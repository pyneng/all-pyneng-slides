# Python для сетевых инженеров 


---

# Unicode

---

### Зачем нужна кодировка?

---
### Компьютеры работают с байтами

Программы, которые мы пишем, не изолированы в себе. Они скачивают данные из Интернета, читают и записывают данные на диск, передают данные через сеть.

Поэтому очень важно понимать разницу между тем, как компьютер хранит и передает данные, и как эти данные воспринимает человек. Мы воспринимаем текст, а компьютер - байты.

---
### Компьютеры работают с байтами


В Python 3, соответственно, есть две концепции:

* текст - неизменяемая последовательность Unicode символов. Для хранения этих символов используется тип строка (str)
* данные - неизменяемая последовательность байтов. Для хранения используется тип bytes

Мы получаем байты при работе с:

* сетью
* файлами


---
### Зачем нужна кодировка?

Для записи символов в байты, нужна определенная договоренность как они будут выглядеть:

* A - 0x41
* F - 0x46


---
### Стандарт ASCII

ASCII (American standard code for information interchange) - описывает соответствие между символом и его числовым кодом. Изначально описывал только 127 символов:

* коды от 32 до 127 описывали печатные символы
* коды до 32 описывали специальные управляющие символы

![ascii](https://upload.wikimedia.org/wikipedia/commons/4/4f/ASCII_Code_Chart.svg)

---

### ISO Latin 1 (ISO 8859-1)

![latin1](http://rabbit.eng.miami.edu/info/asciiiso.gif)

---

### Windows CP1252

![cp1252](http://rabbit.eng.miami.edu/info/cp1252.gif)

---

### Unicode &#129412;


---

### Стандарт Unicode

* 1,114,112 кодов &#129318;
* диапазон 0x0 - 0x10FFFF &#129365;
* стандарт Unicode версии 14.0 (Июнь 2021) определяет 144 697 символов  &#128519;
* каждый код - это номер, который соответствует определенному символу
* стандарт также определяет кодировки - способ представления кода символа в байтах

---
### Примеры символов

* U+1F383 JACK-O-LANTERN &#127875;
* U+2615 HOT BEVERAGE  &#9749;
* U+1f600 GRINNING FACE &#128512;

---
### schön в Unicode

### schön

### U+0073 U+0063 U+0068 U+00f6 U+006e


---

### &#128579; &#8238; Unicode, ты супер!

![xkcd](https://imgs.xkcd.com/comics/rtl_2x.png)

---
### Кодировки

* UTF-8
* UTF-16
* UTF-32

---
### UTF-8

* позволяет хранить символы Юникода
* использует переменное количество байт
* символы ASCII обозначаются такими же кодами

---
### Примеры символов

| H | i | &#128704; | &#128640; | &#9731; |
|:-:|:-:|:---------:|:---------:|:-------:|
| 48|69 | 01 f6 c0  | 01 f6 80  | 26 03   |


---
### Unicode в Python 3

---
### Unicode в Python 3

В Python 3 есть:

* строки - неизменяемая последовательность Unicode символов. Для хранения этих символов используется тип строка (str)
* байты - неизменяемая последовательность байтов. Для хранения используется тип bytes

---
### Строки

---
### str

Строка в Python 3 - это последовательность кодов Unicode.

```python
In [1]: hi = 'привет'

In [2]: type(hi)
Out[2]: str

In [3]: hi.upper()
Out[3]: 'ПРИВЕТ'
```

---
### str

Так как строки - это последовательность кодов Юникод, можно записать строку разными способами.

Символ Юникод можно записать, используя его имя:
```python
In [1]: "\N{LATIN SMALL LETTER O WITH DIAERESIS}"
Out[1]: 'ö'
```

Или использовав такой формат:
```python
In [4]: "\u00F6"
Out[4]: 'ö'
```

---
### str

Строку можно записать как последовательность кодов Unicode

```python
In [19]: hi1 = 'привет'

In [20]: hi2 = '\u043f\u0440\u0438\u0432\u0435\u0442'

In [21]: hi2
Out[21]: 'привет'

In [22]: hi1 == hi2
Out[22]: True

In [23]: len(hi2)
Out[23]: 6
```


---
### ord

Функция ord возвращает значение кода Unicode для символа:
```python
In [7]: ord('п')
Out[7]: 1087

In [8]: hex(ord("a"))
Out[8]: '0x61'
```

---
### chr

Функция chr возвращает строку Unicode, которая символу, чем код был передан как аргумент:
```python
In [9]: chr(1087)
Out[9]: 'п'

In [10]: chr(8364)
Out[10]: '€'

In [11]: chr(9731)
Out[11]: '☃'

```


---
### bytes

---
### bytes

Тип bytes - это неизменяемая последовательность байтов.

Байты обозначаются так же, как строки, но с добавлением буквы "b" перед строкой

---
### bytes

```python
In [30]: b1 = b'\xd0\xb4\xd0\xb0'

In [31]: b2 = b"\xd0\xb4\xd0\xb0"

In [32]: b3 = b'''\xd0\xb4\xd0\xb0'''

In [36]: type(b1)
Out[36]: bytes

In [37]: len(b1)
Out[37]: 4
```

---
### ASCII в bytes

В Python байты, которые соответствуют символам ASCII, отображаются как эти символы, а не как соответствующие им байты. Это может немного путать, но всегда можно распознать тип bytes по букве b:
```python
In [38]: bytes1 = b'hello'

In [39]: bytes1
Out[39]: b'hello'

In [40]: len(bytes1)
Out[40]: 5

In [42]: bytes2 = b'\x68\x65\x6c\x6c\x6f'

In [43]: bytes2
Out[43]: b'hello'
```

---
### Non ASCII

Если попытаться написать не ASCII символ в байтовом литерале, возникнет ошибка:
```python
In [44]: bytes3 = b'привет'
  File "<ipython-input-44-dc8b23504fa7>", line 1
    bytes3 = b'привет'
            ^
SyntaxError: bytes can only contain ASCII literal characters.
```

---
### bytes

Можно работать с байтовыми строками, как с unicode строками:
```python
In [17]: d = {b'hi':'Hello', b'by':'Goodbye'}

In [18]: d[b'hi']
Out[18]: 'Hello'

In [19]: d['hi']
----------------------------------------------------------
KeyError                 Traceback (most recent call last)
<ipython-input-38-259732fc8381> in <module>()
----> 1 d['hi']

KeyError: 'hi'

```



---
### Конвертация между байтами и строками

---
### encode vs decode

Избежать работы с байтами нельзя. Например, при работе с сетью или файловой системой, чаще всего, результат возвращается в байтах.

Соответственно, надо знать, как выполнять преобразование байтов в строку и наоборот. Для этого и нужна кодировка.

#### unicode .encode() &#8594; bytes
#### bytes .decode() &#8594; unicode

---
### encode vs decode

Кодировку можно представлять как ключ шифрования, который указывает:

* как "зашифровать" строку в байты (str -> bytes). Используется метод encode (похож на encrypt)
* как "расшифровать" байты в строку (bytes -> str). Используется метод decode (похож на decrypt)

Эта аналогия позволяет понять, что преобразования строка-байты и байты-строка должны использовать одинаковую кодировку.

---
### encode

Для преобразования строки в байты используется метод encode:
```python
In [1]: hi = 'привет'

In [2]: hi.encode('utf-8')
Out[2]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'

In [3]: hi_bytes = hi.encode('utf-8')
```

---
### decode

Чтобы получить строку из байт, используется метод decode:
```python
In [4]: hi_bytes
Out[4]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'

In [5]: hi_bytes.decode('utf-8')
Out[5]: 'привет'
```

---
### str.encode

Метод encode есть также в классе str:
```python
In [6]: hi
Out[6]: 'привет'

In [7]: str.encode(hi, encoding='utf-8')
Out[7]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'
```

---
### bytes.decode

Метод decode есть у класса bytes (как и другие методы):
```python
In [8]: hi_bytes
Out[8]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'

In [9]: bytes.decode(hi_bytes, encoding='utf-8')
Out[9]: 'привет'
```

---
### str.encode, bytes.decode

В этих методах кодировка может указываться как ключевой аргумент (примеры выше) или как позиционный:
```
In [10]: hi_bytes
Out[10]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'

In [11]: bytes.decode(hi_bytes, 'utf-8')
Out[11]: 'привет'
```

---
### Как работать с Юникод и байтами

---
### Unicode sandwich

Есть очень простое правило, придерживаясь которого, можно избежать, как минимум, части проблем. Оно называется "Юникод сендвич":

* байты, которые программа считывает, надо как можно раньше преобразовать в юникод (строку)
* внутри программы работать с юникод
* юникод надо преобразовать в байты как можно позже, перед передачей

---

[unicodesandwich](http://engineering.cerner.com/assets/2014-08-02-the-plain-text-is-a-lie/unicode-sandwich.png)

[us](https://astrodsg.github.io/static/img/blog/unicode_sandwich.jpg)


---
### Примеры конвертации между байтами и строками

---
### subprocess

---
### subprocess

Модуль subprocess возвращает результат команды в виде байт:
```python
In [1]: import subprocess

In [2]: result = subprocess.run(['ping', '-c', '3', '-n', '8.8.8.8'],
   ...:                         stdout=subprocess.PIPE)
   ...:

In [3]: result.stdout
Out[3]: b'PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.\n64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=59.4 ms\n64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.4 ms\n64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=55.1 ms\n\n--- 8.8.8.8 ping statistics ---\n3 packets transmitted, 3 received, 0% packet loss, time 2002ms\nrtt min/avg/max/mdev = 54.470/56.346/59.440/2.220 ms\n'
```

---
### subprocess

Если дальше необходимо работать с этим выводом, надо сразу конвертировать его в строку:
```python
In [4]: output = result.stdout.decode('utf-8')

In [5]: print(output)
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=59.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.4 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=55.1 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 54.470/56.346/59.440/2.220 ms
```

---
### subprocess encoding

Модуль subprocess поддерживает еще один вариант преобразования - параметр encoding.

Если указать его при вызове функции run, результат будет получен в виде строки:
```python
In [6]: result = subprocess.run(['ping', '-c', '3', '-n', '8.8.8.8'],
   ...:                         stdout=subprocess.PIPE, encoding='utf-8')
   ...:

In [7]: result.stdout
Out[7]: 'PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.\n64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=55.5 ms\n64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.6 ms\n64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=53.3 ms\n\n--- 8.8.8.8 ping statistics ---\n3 packets transmitted, 3 received, 0% packet loss, time 2003ms\nrtt min/avg/max/mdev = 53.368/54.534/55.564/0.941 ms\n'

In [8]: print(result.stdout)
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=55.5 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.6 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=53.3 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 53.368/54.534/55.564/0.941 ms
```

---
### telnetlib

---
### telnetlib

В зависимости от модуля, преобразование между строками и байтами может выполняться автоматически, а может требоваться явно.

Например, в модуле telnetlib необходимо передавать байты в методах read_until и write:
```python
import telnetlib
import time

t = telnetlib.Telnet('192.168.100.1')

t.read_until(b'Username:')
t.write(b'cisco\n')

t.read_until(b'Password:')
t.write(b'cisco\n')
t.write(b'sh ip int br\n')

time.sleep(5)

output = t.read_very_eager().decode('utf-8')
print(output)
```

---
### pexpect

---
### pexpect

Модуль pexpect как аргумент ожидает строку, а возвращает байты:
```python
In [9]: import pexpect

In [10]: output = pexpect.run('ls -ls')

In [11]: output
Out[11]: b'total 8\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 28 12:16 concurrent_futures\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug  3 07:59 iterator_generator\r\n'

In [12]: output.decode('utf-8')
Out[12]: 'total 8\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 28 12:16 concurrent_futures\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug  3 07:59 iterator_generator\r\n'
```

---
### pexpect encoding

И также поддерживает вариант передачи кодировки через параметр encoding:
```python
In [13]: output = pexpect.run('ls -ls', encoding='utf-8')

In [14]: output
Out[14]: 'total 8\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 28 12:16 concurrent_futures\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug  3 07:59 iterator_generator\r\n'
```

---
### Работа с файлами

---
### Работа с файлами

До сих пор при работе с файлами использовалась такая конструкция:
```python
with open(filename) as f:
    for line in f:
        print(line)
```

---
### Кодировка по умолчанию

При чтении файла происходит конвертация байт в строки. И при этом использовалась кодировка по умолчанию:
```python
In [1]: import locale

In [2]: locale.getpreferredencoding()
Out[2]: 'UTF-8'
```

---
### Кодировка по умолчанию

Кодировка по умолчанию в файле:
```python
In [2]: f = open('r1.txt')

In [3]: f
Out[3]: <_io.TextIOWrapper name='r1.txt' mode='r' encoding='UTF-8'>
```

---
### Явное задание кодировки

При работе с файлами лучше явно указывать кодировку, так как в разных ОС она может отличаться:
```python
In [4]: with open('r1.txt', encoding='utf-8') as f:
   ...:     for line in f:
   ...:         print(line, end='')
   ...:
!
service timestamps debug datetime msec localtime show-timezone year
service timestamps log datetime msec localtime show-timezone year
service password-encryption
service sequence-numbers
!
no ip domain lookup
!
ip ssh version 2
!
```


---
### Ошибки при конвертации

---
### Ошибки при конвертации

При конвертации между строками и байтами очень важно точно знать, какая кодировка используется, а также знать о возможностях разных кодировок.

---
### Ошибки

Например, кодировка ASCII не м ожет преобразовать в байты кириллицу:
```python
In [32]: hi_unicode = 'привет'

In [33]: hi_unicode.encode('ascii')
---------------------------------------------------------------------------
UnicodeEncodeError                        Traceback (most recent call last)
<ipython-input-33-ec69c9fd2dae> in <module>()
----> 1 hi_unicode.encode('ascii')

UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-5: ordinal not in range(128)
```

---
### Ошибки

Аналогично, если строка "привет" преобразована в байты, и попробовать преобразовать ее в строку с помощью ascii, тоже получим ошибку:
```python
In [34]: hi_unicode = 'привет'

In [35]: hi_bytes = hi_unicode.encode('utf-8')

In [36]: hi_bytes.decode('ascii')
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-36-aa0ada5e44e9> in <module>()
----> 1 hi_bytes.decode('ascii')

UnicodeDecodeError: 'ascii' codec can't decode byte 0xd0 in position 0: ordinal not in range(128)
```

---
### Ошибки

Еще один вариант ошибки, когда используются разные кодировки для преобразований:
```python
In [37]: de_hi_unicode = 'grüezi'

In [38]: utf_16 = de_hi_unicode.encode('utf-16')

In [39]: utf_16.decode('utf-8')
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-39-4b4c731e69e4> in <module>()
----> 1 utf_16.decode('utf-8')

UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 0: invalid start byte
```

---
### Надо знать какая кодировка использовалась

Но на самом деле, предыдущие ошибки - это хорошо. Они явно говорят, в чем проблема.

Хуже, когда получается так:
```python
In [40]: hi_unicode = 'привет'

In [41]: hi_bytes = hi_unicode.encode('utf-8')

In [42]: hi_bytes
Out[42]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'

In [43]: hi_bytes.decode('utf-16')
Out[43]: '뿐胑룐닐뗐苑'
```

---
### Обработка ошибок

---
### Обработка ошибок

У методов encode и decode есть режимы обработки ошибок, которые указывают, как реагировать на ошибку преобразования.

---
### encode replace

По умолчанию encode использует режим 'strict' - при возникновении ошибок кодировки генерируется исключение UnicodeError. Примеры такого поведения были выше.

Режим replace заменит символ знаком вопроса:
```python
In [44]: de_hi_unicode = 'grüezi'

In [45]: de_hi_unicode.encode('ascii', 'replace')
Out[45]: b'gr?ezi'
```

---
### encode namereplace

Или namereplace, чтобы заменить символ именем:
```python
In [46]: de_hi_unicode = 'grüezi'

In [47]: de_hi_unicode.encode('ascii', 'namereplace')
Out[47]: b'gr\\N{LATIN SMALL LETTER U WITH DIAERESIS}ezi'
```

---
### encode ignore

Кроме того, можно полностью игнорировать символы, которые нельзя закодировать:
```python
In [48]: de_hi_unicode = 'grüezi'

In [49]: de_hi_unicode.encode('ascii', 'ignore')
Out[49]: b'grezi'
```

---
### Параметр errors в decode

В методе decode по умолчанию тоже используется режим strict и генерируется исключение UnicodeDecodeError.

---
### decode ignore

Если изменить режим на ignore, как и в encode, символы будут просто игнорироваться:
```python
In [50]: de_hi_unicode = 'grüezi'

In [51]: de_hi_utf8 = de_hi_unicode.encode('utf-8')

In [52]: de_hi_utf8
Out[52]: b'gr\xc3\xbcezi'

In [53]: de_hi_utf8.decode('ascii', 'ignore')
Out[53]: 'grezi'
```

---
### decode replace

Режим replace заменит символы:
```python
In [54]: de_hi_unicode = 'grüezi'

In [55]: de_hi_utf8 = de_hi_unicode.encode('utf-8')

In [56]: de_hi_utf8.decode('ascii', 'replace')
Out[56]: 'gr��ezi'
```

---

# &#128561;

---

# &#9749; &#127829; &#127828;

---


1. &#128013; [Pragmatic Unicode](https://nedbatchelder.com/text/unipain.html)
2. &#129417; [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)
3. &#128013; [Unicode HOWTO](https://docs.python.org/3/howto/unicode.html)


---

# &#128564;

