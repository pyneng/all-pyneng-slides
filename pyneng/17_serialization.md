# Python для сетевых инженеров 

---

# Сериализация данных

---
### Сериализация данных

Сериализация данных - это сохранение данных в каком-то формате.
Чаще всего, это сохранение в каком-то структурированном формате.

Например, это могут быть:
* файлы в формате YAML или JSON
* файлы в формате CSV
* база данных

---
### Сериализация данных

Для чего могут пригодится форматы YAML, JSON, CSV:
* у вас могут быть данные о IP-адресах и подобной информации, которую нужно обработать, в таблицах
 * таблицу можно экспортировать в формат CSV и обрабатывать её с помощью Python
* управляющий софт может возвращать данные в JSON. Соответственно, преобразовав эти данные в объект Python, с ними можно работать и делать, что угодно
* YAML очень удобно использовать для описания параметров
 * например, это могут быть параметры настройки различных объектов (IP-адреса, VLAN и др)

---

## Работа с файлами в формате CSV

---

### Работа с файлами в формате CSV

__CSV (comma-separated value)__ - это формат представления табличных данных (например, это могут быть данные из таблицы, или данные из БД).

В этом формате, каждая строка файла - это строка таблицы.
Несмотря на название формата, разделителем может быть не только запятая.

У форматов с другим разделителем может быть и собственное название, например, TSV (tab separated values), тем не менее под форматом CSV понимают, как правило, любые разделители.

---

### Работа с файлами в формате CSV

Пример файла в формате CSV (sw_data.csv):
```
hostname,vendor,model,location
sw1,Cisco,3750,London
sw2,Cisco,3850,Liverpool
sw3,Cisco,3650,Liverpool
sw4,Cisco,3650,London
```

В стандартной библиотеке Python есть модуль csv, который позволяет работать с файлами в CSV формате.

---

### Чтение файлов в формате CSV

Пример использования модуля csv (файл csv_read.py):
```python
import csv

with open('sw_data.csv') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)
```

Вывод будет таким:
```
$ python csv_read.py
['hostname', 'vendor', 'model', 'location']
['sw1', 'Cisco', '3750', 'London']
['sw2', 'Cisco', '3850', 'Liverpool']
['sw3', 'Cisco', '3650', 'Liverpool']
['sw4', 'Cisco', '3650', 'London']
```

---

### Чтение файлов в формате CSV

reader - это итератор:
```python
In [1]: import csv

In [2]: with open('sw_data.csv') as f:
   ...:     reader = csv.reader(f)
   ...:     print reader
   ...:
<_csv.reader object at 0x10385b050>
```

---

### Чтение файлов в формате CSV

Заголовки столбцов удобней получить отдельным объектом (файл csv_read_headers.py):
```py
import csv

with open('sw_data.csv') as f:
    reader = csv.reader(f)
    headers = next(reader)
    print('Headers: ', headers)
    for row in reader:
        print(row)
```


---

### Чтение файлов в формате CSV

DictReader позволяет получить словари, в которых ключи - это названия столбцов, а значения - значения столбцов (файл csv_read_dict.py):
```python
import csv

with open('sw_data.csv') as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row)
        print(row['hostname'], row['model'])

```

---

### Чтение файлов в формате CSV

Вывод будет таким:
```
$ python csv_read_dict.py
OrderedDict([('hostname', 'sw1'), ('vendor', 'Cisco'), ('model', '3750'), ('location', 'London')])
sw1 3750
OrderedDict([('hostname', 'sw2'), ('vendor', 'Cisco'), ('model', '3850'), ('location', 'Liverpool')])
sw2 3850
OrderedDict([('hostname', 'sw3'), ('vendor', 'Cisco'), ('model', '3650'), ('location', 'Liverpool')])
sw3 3650
OrderedDict([('hostname', 'sw4'), ('vendor', 'Cisco'), ('model', '3650'), ('location', 'London')])
sw4 3650
```

---

### Запись файлов в формате CSV

Аналогичным образом, с помощью модуля csv, можно и записать файл в формате CSV (файл csv_write.py):

```python
import csv

data = [['hostname', 'vendor', 'model', 'location'],
        ['sw1', 'Cisco', '3750', 'London, Best str'],
        ['sw2', 'Cisco', '3850', 'Liverpool, Better str'],
        ['sw3', 'Cisco', '3650', 'Liverpool, Better str'],
        ['sw4', 'Cisco', '3650', 'London, Best str']]

with open('sw_data_new.csv', 'w') as f:
    writer = csv.writer(f)
    for row in data:
        writer.writerow(row)

with open('sw_data_new.csv') as f:
    print(f.read())
```

---

### Запись файлов в формате CSV

Вывод будет таким:
```
$ python csv_write.py
hostname,vendor,model,location
sw1,Cisco,3750,"London, Best str"
sw2,Cisco,3850,"Liverpool, Better str"
sw3,Cisco,3650,"Liverpool, Better str"
sw4,Cisco,3650,"London, Best str"
```

Обратите внимание: последнее значение, взято в кавычки, а остальные строки - нет.

---

### Запись файлов в формате CSV

Для того, чтобы все строки записывались в файл csv с кавычками, надо изменить скрипт таким образом (файл csv_write_quoting.py):
```python
import csv

data = [['hostname', 'vendor', 'model', 'location'],
        ['sw1', 'Cisco', '3750', 'London, Best str'],
        ['sw2', 'Cisco', '3850', 'Liverpool, Better str'],
        ['sw3', 'Cisco', '3650', 'Liverpool, Better str'],
        ['sw4', 'Cisco', '3650', 'London, Best str']]

with open('sw_data_new.csv', 'w') as f:
    writer = csv.writer(f, quoting=csv.QUOTE_NONNUMERIC)
    for row in data:
        writer.writerow(row)

with open('sw_data_new.csv') as f:
    print(f.read())
```

---

### Запись файлов в формате CSV

Теперь вывод будет таким:
```
$ python csv_write_quoting.py
"hostname","vendor","model","location"
"sw1","Cisco","3750","London, Best str"
"sw2","Cisco","3850","Liverpool, Better str"
"sw3","Cisco","3650","Liverpool, Better str"
"sw4","Cisco","3650","London, Best str"
```

Теперь все значения с кавычками. И, так как номер модели задан как строка, в изначальном списке, тут он тоже в кавычках.

---

### Запись файлов в формате CSV

Кроме метода writerow, поддерживается метод writerows (файл csv_writerows.py):
```python
import csv

data = [['hostname', 'vendor', 'model', 'location'],
        ['sw1', 'Cisco', '3750', 'London, Best str'],
        ['sw2', 'Cisco', '3850', 'Liverpool, Better str'],
        ['sw3', 'Cisco', '3650', 'Liverpool, Better str'],
        ['sw4', 'Cisco', '3650', 'London, Best str']]

with open('sw_data_new.csv', 'w') as f:
    writer = csv.writer(f, quoting=csv.QUOTE_NONNUMERIC)
    writer.writerows(data)

with open('sw_data_new.csv') as f:
    print(f.read())
```

---
### DictWriter

С помощью DictWriter можно записать словари в формат csv.

В целом DictWriter работает так же, как writer,
но так как словари не упорядочены, надо указывать явно в каком порядке будут идти столбцы в файле.
Для этого используется параметр fieldnames.

---
### DictWriter

Файл csv_write_dict.py:
```python
import csv

data = [{'hostname': 'sw1',
         'location': 'London',
         'model': '3750',
         'vendor': 'Cisco'},
        {'hostname': 'sw2',
         'location': 'Liverpool',
         'model': '3850',
         'vendor': 'Cisco'},
        {'hostname': 'sw3',
         'location': 'Liverpool',
         'model': '3650',
         'vendor': 'Cisco'},
        {'hostname': 'sw4',
         'location': 'London',
         'model': '3650',
         'vendor': 'Cisco'}]

with open('csv_write_dictwriter.csv', 'w') as f:
    writer = csv.DictWriter(f, fieldnames=list(data[0].keys()),
                            quoting=csv.QUOTE_NONNUMERIC)
    writer.writeheader()
    for d in data:
        writer.writerow(d)
```

---

### Указание разделителя

Например, если в файле используется разделитель ```;``` (файл sw_data2.csv):
```
hostname;vendor;model;location
sw1;Cisco;3750;London
sw2;Cisco;3850;Liverpool
sw3;Cisco;3650;Liverpool
sw4;Cisco;3650;London
```

Достаточно просто указать какой разделитель используется в reader (файл csv_read_delimiter.py):
```python
import csv

with open('sw_data2.csv') as f:
    reader = csv.reader(f, delimiter=';')
    for row in reader:
        print(row)
```


---

## Работа с файлами в формате JSON

---

### Работа с файлами в формате JSON

__JSON (JavaScript Object Notation)__ - это текстовый формат для хранения и обмена данными.

[JSON](https://ru.wikipedia.org/wiki/JSON) по синтаксису очень похож на Python. И достаточно удобен для восприятия.

Как и в случае с CSV, в Python есть модуль, который позволяет легко записывать и читать данные в формате JSON.


---

### Чтение

Файл sw_templates.json:
```json
{
  "access": [
    "switchport mode access", 
    "switchport access vlan", 
    "switchport nonegotiate", 
    "spanning-tree portfast", 
    "spanning-tree bpduguard enable"
  ], 
  "trunk": [
    "switchport trunk encapsulation dot1q", 
    "switchport mode trunk", 
    "switchport trunk native vlan 999", 
    "switchport trunk allowed vlan"
  ]
}
```

---

### Чтение. json.load()

Чтение файла в формате JSON в объект Python (файл json_read_load.py):
```python
import json

with open('sw_templates.json') as f:
    templates = json.load(f)

for section, commands in templates.items():
    print(section)
    print('\n'.join(commands))

```

---

### Чтение. json.load()

Вывод будет таким:
```python
$ python json_read_load.py
{'access': ['switchport mode access', 'switchport access vlan', 'switchport nonegotiate', 'spanning-tree portfast', 'spanning-tree bpduguard enable'], 'trunk': ['switchport trunk encapsulation dot1q', 'switchport mode trunk', 'switchport trunk native vlan 999', 'switchport trunk allowed vlan']}
access
switchport mode access
switchport access vlan
switchport nonegotiate
spanning-tree portfast
spanning-tree bpduguard enable
trunk
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 999
switchport trunk allowed vlan

```

---

### Чтение. json.loads()

Считывание строки в формате JSON в объект Python (файл json_read_loads.py):
```python
import json

with open('sw_templates.json') as f:
    file_content = f.read()
    templates = json.loads(file_content)

print(templates)

for section, commands in templates.items():
    print(section)
    print('\n'.join(commands))

```


---
### Запись. json.dumps()

Преобразование объекта в строку в формате JSON (json_write_dumps.py):
```python
import json


trunk_template = ['switchport trunk encapsulation dot1q',
                  'switchport mode trunk',
                  'switchport trunk native vlan 999',
                  'switchport trunk allowed vlan']

access_template = ['switchport mode access',
                   'switchport access vlan',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

to_json = {'trunk':trunk_template, 'access':access_template}

with open('sw_templates.json', 'w') as f:
    f.write(json.dumps(to_json))

with open('sw_templates.json') as f:
    print(f.read())

```

---
### Запись. json.dump()

Запись объекта Python в файл в формате JSON (файл json_write_dump.py):
```python
import json


trunk_template = ['switchport trunk encapsulation dot1q',
                  'switchport mode trunk',
                  'switchport trunk native vlan 999',
                  'switchport trunk allowed vlan']

access_template = ['switchport mode access',
                   'switchport access vlan',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

to_json = {'trunk':trunk_template, 'access':access_template}

with open('sw_templates.json', 'w') as f:
    json.dump(to_json, f)

with open('sw_templates.json') as f:
    print(f.read())

```

---
### Запись

Более удобный для чтения вывод (файл json_write_indent.py):
```python
import json


trunk_template = ['switchport trunk encapsulation dot1q',
                  'switchport mode trunk',
                  'switchport trunk native vlan 999',
                  'switchport trunk allowed vlan']

access_template = ['switchport mode access',
                   'switchport access vlan',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

to_json = {'trunk':trunk_template, 'access':access_template}

with open('sw_templates.json', 'w') as f:
    json.dump(to_json, f, sort_keys=True, indent=2)

with open('sw_templates.json') as f:
    print(f.read())
``` 

---
### Запись

Теперь содержимое файла sw_templates.json выглядит так:
```json
{
  "access": [
    "switchport mode access",
    "switchport access vlan",
    "switchport nonegotiate",
    "spanning-tree portfast",
    "spanning-tree bpduguard enable"
  ],
  "trunk": [
    "switchport trunk encapsulation dot1q",
    "switchport mode trunk",
    "switchport trunk native vlan 999",
    "switchport trunk allowed vlan"
  ]
}
```

---
### Изменение типа данных

При работе с форматом json, данные не всегда будут того же типа, что исходные данные в Python.

Например, кортежи, при записи в JSON, превращаются в списки:
```python

In [1]: import json

In [2]: trunk_template = ('switchport trunk encapsulation dot1q',
   ...:                   'switchport mode trunk',
   ...:                   'switchport trunk native vlan 999',
   ...:                   'switchport trunk allowed vlan')

In [3]: print type(trunk_template)
<type 'tuple'>

In [4]: with open('trunk_template.json', 'w') as f:
   ...:     json.dump(trunk_template, f, sort_keys=True, indent=2)
   ...:
```

---
### Изменение типа данных

```python

In [5]: cat trunk_template.json
[
  "switchport trunk encapsulation dot1q",
  "switchport mode trunk",
  "switchport trunk native vlan 999",
  "switchport trunk allowed vlan"
]
In [6]: templates = json.load(open('trunk_template.json'))

In [7]: type(templates)
Out[7]: list

In [8]: print(templates)
['switchport trunk encapsulation dot1q', 'switchport mode trunk', 'switchport trunk native vlan 999', 'switchport trunk allowed vlan']
```

---
### Конвертация данных Python в JSON

|  Python     | JSON  |
|:-----------:|:-----:|
|  dict       | object|
| list, tuple | array |
| str         | string|
| int, float  | number|
| True        | true  |
| False       | false |
| None        | null  |

---
### Конвертация JSON в данные Python

| JSON  |  Python |
|:-----:|:-------:|
| object| dict
| array | list
| string| str
| number (int) | int
| number (real)| float
| true  | True
| false | False
| null  | None



---
### Ключи словарей

В формат JSON нельзя записать словарь у которого ключи - кортежи:
```python
In [9]: to_json = {('trunk', 'cisco'):trunk_template, 'access':access_template}

In [10]: with open('sw_templates.json', 'w') as f:
    ...:     json.dump(to_json, f)
    ...:
...
TypeError: key ('trunk', 'cisco') is not a string
```

---
### Ключи словарей

Специальный параметр позволяет игнорировать такие ключи:
```python
In [11]: with open('sw_templates.json', 'w') as f:
    ...:     json.dump(to_json, f, skipkeys=True)
    ...:
    ...:

In [12]: cat sw_templates.json
{"access": ["switchport mode access", "switchport access vlan", "switchport nonegotiate",
"spanning-tree portfast", "spanning-tree bpduguard enable"]}
```

---

## Работа с файлами в формате YAML

---

### Работа с файлами в формате YAML

__YAML (YAML Ain't Markup Language)__ - еще один текстовый формат для записи данных.

YAML более приятен для восприятия человеком, чем JSON, поэтому его часто используют для описания сценариев в ПО.
Например, в Ansible.

---

### Синтаксис YAML

---
### Синтаксис YAML

Как и Python, YAML использует отступы для указания структуры документа.
Но в YAML можно использовать только пробелы и нельзя использовать знаки табуляции.

Еще одна схожесть с Python: комментарии начинаются с символа # и продолжаются до конца строки.

---

#### Список

Список может быть записан в одну строку:
```yaml
[switchport mode access, switchport access vlan, switchport nonegotiate, spanning-tree portfast, spanning-tree bpduguard enable]
```

Или каждый элемент списка в своей строке:
```yaml
- switchport mode access
- switchport access vlan
- switchport nonegotiate
- spanning-tree portfast
- spanning-tree bpduguard enable
```

Когда список записан таким блоком, каждая строка должна начинаться с ```- ``` (минуса и пробела). И все строки в списке должны быть на одном уровне отступа.

---

#### Словарь

Словарь также может быть записан в одну строку:
```yaml
{ vlan: 100, name: IT }
```

Или блоком:
```yaml
vlan: 100
name: IT
```
---

#### Строки

Строки в YAML не обязательно брать в кавычки.
Это удобно, но иногда всё же следует использовать кавычки.
Например, когда в строке используется какой-то специальный символ (специальный для YAML).

Такую строку, например, нужно взять в кавычки, чтобы она была корректно воспринята YAML:
```yaml
command: "sh interface | include Queueing strategy:"
```
---

#### Комбинация элементов

Словарь, в котором есть два ключа: access и trunk.
Значения, которые соответствуют этим ключам - списки команд:
```yaml
access:
- switchport mode access
- switchport access vlan
- switchport nonegotiate
- spanning-tree portfast
- spanning-tree bpduguard enable

trunk:
- switchport trunk encapsulation dot1q
- switchport mode trunk
- switchport trunk native vlan 999
- switchport trunk allowed vlan
```

---

#### Комбинация элементов

Список словарей:
```yaml
- BS: 1550
  IT: 791
  id: 11
  name: Liverpool
  to_id: 1
  to_name: LONDON
- BS: 1510
  IT: 793
  id: 12
  name: Bristol
  to_id: 1
  to_name: LONDON
- BS: 1650
  IT: 892
  id: 14
  name: Coventry
  to_id: 2
  to_name: Manchester
```

---

### Модуль PyYAML

---

### Модуль PyYAML
Для работы с YAML в Python используется модуль PyYAML.
Он не входит в стандартную библиотеку модулей, поэтому его нужно установить:
```
pip install pyyaml
```

Работа с ним аналогична модулям csv и json.

---

#### Чтение из YAML

Файл info.yaml:
```yaml
- BS: 1550
  IT: 791
  id: 11
  name: Liverpool
  to_id: 1
  to_name: LONDON
- BS: 1510
  IT: 793
  id: 12
  name: Bristol
  to_id: 1
  to_name: LONDON
- BS: 1650
  IT: 892
  id: 14
  name: Coventry
  to_id: 2
  to_name: Manchester
```

---

#### Чтение из YAML

Чтение из YAML (файл yaml_read.py):
```python
import yaml
import pprint

with open('info.yaml') as f:
    templates = yaml.load(f)

pprint.pprint(templates)

```

---

#### Чтение из YAML
Результат:
```python
$ python yaml_read.py
[{'BS': 1550,
  'IT': 791,
  'id': 11,
  'name': 'Liverpool',
  'to_id': 1,
  'to_name': 'LONDON'},
 {'BS': 1510,
  'IT': 793,
  'id': 12,
  'name': 'Bristol',
  'to_id': 1,
  'to_name': 'LONDON'},
 {'BS': 1650,
  'IT': 892,
  'id': 14,
  'name': 'Coventry',
  'to_id': 2,
  'to_name': 'Manchester'}]

```


---

#### Запись в YAML

Запись объектов Python в YAML (файл yaml_write.py):
```python
import yaml

trunk_template = ['switchport trunk encapsulation dot1q',
                  'switchport mode trunk',
                  'switchport trunk native vlan 999',
                  'switchport trunk allowed vlan']

access_template = ['switchport mode access',
                   'switchport access vlan',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

to_yaml = {'trunk':trunk_template, 'access':access_template}

with open('sw_templates.yaml', 'w') as f:
    yaml.dump(to_yaml, f)

with open('sw_templates.yaml') as f:
    print(f.read())

```

---

#### Запись в YAML

Файл sw_templates.yaml выглядит таким образом:
```yaml
access: [switchport mode access, switchport access vlan, switchport nonegotiate, spanning-tree
    portfast, spanning-tree bpduguard enable]
trunk: [switchport trunk encapsulation dot1q, switchport mode trunk, switchport trunk
    native vlan 999, switchport trunk allowed vlan]
```

---

#### Запись в YAML

Параметр ```default_flow_style=False``` (файл yaml_write_default_flow_style.py):
```python
import yaml

trunk_template = ['switchport trunk encapsulation dot1q',
                  'switchport mode trunk',
                  'switchport trunk native vlan 999',
                  'switchport trunk allowed vlan']

access_template = ['switchport mode access',
                   'switchport access vlan',
                   'switchport nonegotiate',
                   'spanning-tree portfast',
                   'spanning-tree bpduguard enable']

to_yaml = {'trunk':trunk_template, 'access':access_template}

with open('sw_templates.yaml', 'w') as f:
    yaml.dump(to_yaml, f, default_flow_style=False)

with open('sw_templates.yaml') as f:
    print f.read()
```

---

#### Запись в YAML

Теперь содержимое файла sw_templates.yaml выглядит таким образом:
```yaml
access:
- switchport mode access
- switchport access vlan
- switchport nonegotiate
- spanning-tree portfast
- spanning-tree bpduguard enable
trunk:
- switchport trunk encapsulation dot1q
- switchport mode trunk
- switchport trunk native vlan 999
- switchport trunk allowed vlan
```

