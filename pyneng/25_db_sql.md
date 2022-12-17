# Python для сетевых инженеров 

---

# Работа с базами данных

---
### Работа с базами данных

__База данных (БД)__ - это данные, которые хранятся в соответствии с определенной схемой. В этой схеме каким-то образом описаны соотношения между данными.

__Язык БД (лингвистические средства)__ - используется для описания структуры БД, управления данными (добавление, изменение, удаление, получение), управления правами доступа к БД и ее объектам, управления транзакциями.

__Система управления базами данных (СУБД)__ - это программные средства, которые дают возможность управлять БД. СУБД должны поддерживать соответствующий язык (языки) для управления БД.

---
### SQL

__SQL (structured query language)__ - используется для описания структуры БД, управления данными (добавление, изменение, удаление, получение), управления правами доступа к БД и ее объектам, управления транзакциями.

Язык SQL подразделяется на такие 4 категории:
* DDL (Data Definition Language) - язык описания данных
* DML (Data Manipulation Language) - язык манипулирования данными
* DCL (Data Control Language) - язык определения доступа к данным
* TCL (Transaction Control Language) - язык управления транзакциями

---
### SQL

В каждой категории есть свои операторы (перечислены не все операторы):
* DDL
 * CREATE - создание новой таблицы, СУБД, схемы
 * ALTER - изменение существующей таблицы, колонки
 * DROP - удаление существующих объектов из СУБД
* DML
 * SELECT - выбор данных
 * INSERT - добавление новых данных
 * UPDATE - обновление существующих данных
 * DELETE - удаление данных
* DCL
 * GRANT - предоставление пользователям разрешения на чтение/запись определенных объектов в СУБД
 * REVOKE - отзывает ранее предоставленые разрешения
* TCL
 * COMMIT Transaction - применение транзакции
 * ROLLBACK Transaction - откат всех изменений сделанных в текущей транзакции

---

### SQL и Python
Для работы с реляционной СУБД в Python можно использовать два подхода:
* работать с библиотекой, которая соответствует конкретной СУБД и использовать для работы с БД язык SQL
 * Например, для работы с SQLite используется модуль sqlite3
* работать с [ORM](http://xgu.ru/wiki/ORM), которая использует объектно-ориентированный подход для работы с БД
 * Например, SQLAlchemy


---

## SQLite

---

### SQLite

[SQLite](http://xgu.ru/wiki/SQLite) — встраиваемая в процесс реализация SQL-машины.

На практике, SQLite часто используется как встроенная СУБД в приложениях.

---

### SQLite CLI

В комплекте поставки SQLite идёт также утилита для работы с SQLite в командной строке.
Утилита представлена в виде исполняемого файла sqlite3 (sqlite3.exe для Windows) и с ее помощью можно вручную выполнять команды SQL.

В помощью этой утилиты очень удобно проверять правильность команд SQL, а также в целом знакомится с языком SQL.

---

### SQLite CLI

Для того чтобы создать БД (или открыть уже созданную) надо просто вызвать sqlite3 таким образом:
```
$ sqlite3 testDB.db
SQLite version 3.34.1 2021-01-20 14:10:56
Enter ".help" for usage hints.
sqlite> 
```

Внутри sqlite3 можно выполнять команды SQL или, так называемые, метакоманды (или dot-команды).

---

### Метакоманды
К метакомандам относятся несколько специальных команд, для работы с SQLite.
Они относятся только к утилите sqlite3, а не к SQL языку. В конце этих команд ```;``` ставить не нужно.

Примеры метакоманд:
* ```.help``` - подсказка со списком всех метакоманд
* ```.exit``` или ```.quit``` - выход из сессии sqlite3
* ```.databases``` - показывает присоединенные БД
* ```.tables``` - показывает доступные таблицы

---

### Метакоманды

Примеры выполнения:
```
sqlite> .help
.backup ?DB? FILE      Backup DB (default "main") to FILE
.bail ON|OFF           Stop after hitting an error.  Default OFF
.databases             List names and files of attached databases
...

sqlite> .databases
seq  name      file                                   
---  --------  ----------------------------------
0    main      /home/nata/py_for_ne/db/db_article/testDB.db              
```

---

## Основы SQL (в sqlite3 CLI)

---
### SQL

* DDL (Data Definition Language) - язык описания данных
* DML (Data Manipulation Language) - язык манипулирования данными
* TCL (Transaction Control Language) - язык управления транзакциями

В каждой категории есть свои операторы (перечислены не все операторы):
* DDL
 * CREATE - создание новой таблицы, СУБД, схемы
 * ALTER - изменение существующей таблицы, колонки
 * DROP - удаление существующих объектов из СУБД
* DML
 * SELECT - выбор данных
 * INSERT - добавление новых данных
 * UPDATE - обновление существующих данных
 * DELETE - удаление данных
* TCL
 * COMMIT Transaction - применение транзакции
 * ROLLBACK Transaction - откат всех изменений сделанных в текущей транзакции

---

### CREATE

Оператор create позволяет создавать таблицы.

Создадим таблицу switch, в которой хранится информация о коммутаторах:
```sql
sqlite> CREATE table switch (
   ...>     mac          text not NULL primary key,
   ...>     hostname     text,
   ...>     model        text,
   ...>     location     text
   ...> );
```

---

### CREATE

Аналогично можно было создать таблицу и таким образом:
```sql
sqlite> create table switch (mac text not NULL primary key, hostname text, model text, location text);
```

Поле mac является первичным ключом. Это автоматически значит, что:
* поле должно быть уникальным
* в нем не может находиться значение NULL

В этом примере это вполне логично, так как MAC-адрес должен быть уникальным.


---

### CREATE

На данный момент записей в таблице нет, есть только ее определение.
Просмотреть определение можно такой командой:
```sql
sqlite> .schema switch
CREATE TABLE switch (
mac          text not NULL primary key,
hostname     text,
model        text,
location     text
);
```

---

### DROP

Оператор drop удаляет таблицу вместе со схемой и всеми данными.

Удалить таблицу можно так:
```sql
sqlite> DROP table switch;
```

---

### ALTER

Оператор ALTER позволяет менять существующую таблицу: добавлять новые колонки или переименовывать таблицу.

Добавим в таблицу новые поля:
* mngmt_ip - IP-адрес коммутатора в менеджмент VLAN
* mngmt_vid - VLAN ID (номер VLAN) для менеджмент VLAN

Добавление записей с помощью команды ALTER:
```sql
sqlite> ALTER table switch ADD COLUMN mngmt_ip text;
sqlite> ALTER table switch ADD COLUMN mngmt_vid integer;
```

---

### ALTER

Теперь таблица выглядит так (новые поля установлены в значение NULL):
```sql
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str

```

---

### INSERT

Оператор insert используется для добавления данных в таблицу.

Если указываются значения для всех полей, добавить запись можно таким образом (порядок полей должен соблюдаться):
```sql
sqlite> INSERT into switch values ('0010.A1AA.C1CC', 'sw1', 'Cisco 3750', 'London, Green Str');
```

Если нужно указать не все поля, или указать их в произвольном порядке, используется такая запись:
```sql
sqlite> INSERT into switch (mac, model, location, hostname)
   ...> values ('0020.A2AA.C2CC', 'Cisco 3850', 'London, Green Str', 'sw2');
```

---

### SELECT

Оператор select позволяет запрашивать информацию в таблице.

Например:
```sql
sqlite> SELECT * from switch;
0010.A1AA.C1CC|sw1|Cisco 3750|London, Green Str
0020.A2AA.C2CC|sw2|Cisco 3850|London, Green Str
```

---

### SELECT

Включить отображение названия полей можно с помощью команды ```.headers ON```.
```sql
sqlite> .headers ON
sqlite> SELECT * from switch;
mac|hostname|model|location
0010.A1AA.C1CC|sw1|Cisco 3750|London, Green Str
0020.A2AA.C2CC|sw2|Cisco 3850|London, Green Str

```

---

### SELECT

За форматирование вывода отвечает команда ```.mode```.

Режим ```.mode column``` включает отображение в виде колонок:
```sql
sqlite> .mode column
sqlite> SELECT * from switch;
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
```

---

### Метакоманда ```.read```

В таблице switch всего две записи:
```
sqlite> SELECT * from switch;
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str

```


---
### Метакоманда ```.read```

Метакоманда .read позволяет загружать команды SQL из файла.

Для добавления записей, заготовлен файл add_rows_to_testdb.txt:
```sql
INSERT into switch values ('0030.A3AA.C1CC', 'sw3', 'Cisco 3750', 'London, Green Str');
INSERT into switch values ('0040.A4AA.C2CC', 'sw4', 'Cisco 3850', 'London, Green Str');
INSERT into switch values ('0050.A5AA.C3CC', 'sw5', 'Cisco 3850', 'London, Green Str');
INSERT into switch values ('0060.A6AA.C4CC', 'sw6', 'C3750', 'London, Green Str');
INSERT into switch values ('0070.A7AA.C5CC', 'sw7', 'Cisco 3650', 'London, Green Str');
```

---
### Метакоманда ```.read```

Для загрузки команд из файла, надо выполнить команду:
```
sqlite> .read add_rows_to_testdb.txt

```

---
### SELECT

```sql
sqlite> SELECT * from switch;
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str

```


---

### WHERE

Оператор WHERE используется для уточнения запроса.
С помощью этого оператора можно указывать определенные условия, по которым отбираются данные.
Если условие выполнено, возвращается соответствующее значение из таблицы, если нет, не возвращается.

---
### WHERE

Таблица switch выглядит так:
```sql
sqlite> SELECT * from switch;
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str

```


---
### WHERE

Показать только те коммутаторы, модель которых 3850:
```sql
sqlite> SELECT * from switch WHERE model = 'Cisco 3850';
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str

```

---

### WHERE

Оператор WHERE позволяет указывать не только конкретное значение поля.
Если добавить к нему оператор like, можно указывать шаблон поля.

LIKE с помощью символов ```_``` и ```%``` указывает на что должно быть похоже значение:
* ```_``` - обозначает один символ или число
* ```%``` - обозначает ноль, один или много символов

Например, если поле model записано в разном формате, с помощью предыдущего запроса, не получится вывести нужные коммутаторы.

---

### WHERE

Например, у коммутатора sw6 поле model записано в таком формате C3750, а у коммутаторов sw1 и sw3 в таком Cisco 3750.

В таком варианте запрос с оператором WHERE не покажет sw6:
```sql
sqlite> SELECT * from switch WHERE model = 'Cisco 3750';
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str

```


---
### WHERE

Но, если вместе с оператором WHERE использовать оператор ```LIKE```:
```sql
sqlite> SELECT * from switch WHERE model LIKE '%3750';
mac             hostname    model       location
--------------  ----------  ----------  -----------------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str

```

---

### UPDATE

Оператор UPDATE используется для изменения существующей записи таблицы.

Обычно, UPDATE используется вместе с оператором where, чтобы уточнить какую именно запись необходимо изменить.

С помощью UPDATE, можно заполнить новые столбцы в таблице:
```sql
sqlite> UPDATE switch set mngmt_ip = '10.255.1.1' WHERE hostname = 'sw1';
```


---
### UPDATE

Результат будет таким:
```
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str  10.255.1.1
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str
```
---
### UPDATE

Аналогичным образом можно изменить и номер VLAN:
```sql
sqlite> UPDATE switch set mngmt_vid = 255 WHERE hostname = 'sw1';
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str

```

---
### UPDATE

И можно изменить несколько полей за раз:
```sql
sqlite> UPDATE switch set mngmt_ip = '10.255.1.2', mngmt_vid = 255 WHERE hostname = 'sw2';
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
0060.A6AA.C4CC  sw6         C3750       London, Green Str
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str

```

---
### UPDATE

Чтобы не заполнять поля mngmt_ip и mngmt_vid вручную, заполним остальное из файла update_fields_in_testdb.txt:
```sql
UPDATE switch set mngmt_ip = '10.255.1.3', mngmt_vid = 255 WHERE hostname = 'sw3';
UPDATE switch set mngmt_ip = '10.255.1.4', mngmt_vid = 255 WHERE hostname = 'sw4';
UPDATE switch set mngmt_ip = '10.255.1.5', mngmt_vid = 255 WHERE hostname = 'sw5';
UPDATE switch set mngmt_ip = '10.255.1.6', mngmt_vid = 255 WHERE hostname = 'sw6';
UPDATE switch set mngmt_ip = '10.255.1.7', mngmt_vid = 255 WHERE hostname = 'sw7';

```

---
### UPDATE

После загрузки команд, таблица выглядит так:
```sql
sqlite> .read update_fields_in_testdb.txt

sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str  10.255.1.3  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255

```

---
### REPLACE

Оператор REPLACE используется для добавления или замены данных в таблице.

Когда возникает нарушение условия уникальности поля, выражение с оператором REPLACE:
* удаляет существующую строку, которая вызвала нарушение
* добавляет новую строку

---
### REPLACE

У выражения REPLACE есть два вида:
```
sqlite> INSERT OR REPLACE INTO switch
   ...> VALUES ('0030.A3AA.C1CC', 'sw3', 'Cisco 3850', 'London, Green Str', '10.255.1.3', 255);
```

Или более короткий вариант:
```
sqlite> REPLACE INTO switch
   ...> VALUES ('0030.A3AA.C1CC', 'sw3', 'Cisco 3850', 'London, Green Str', '10.255.1.3', 255);
```

---
### REPLACE

Результатом любой из этих команд, будет замена модели коммутатора sw3:
```sql
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255

```

---
### REPLACE

При добавлении записи, для которой не возникает нарушения уникальности поля, replace работает как обычный insert:
```
sqlite> REPLACE INTO switch
   ...> VALUES ('0080.A8AA.C8CC', 'sw8', 'Cisco 3850', 'London, Green Str', '10.255.1.8', 255);

sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255
0080.A8AA.C8CC  sw8         Cisco 3850  London, Green Str  10.255.1.8  255

```

---

### DELETE

Оператор delete используется для удаления записей.

Как правило, он используется вместе с оператором where.

Например:
```sql
sqlite> DELETE from switch where hostname = 'sw8';
```

---
### DELETE

Теперь в таблице нет строки с коммутатором sw:
```sql
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255

```


---

### ORDER BY

Оператор ORDER BY используется для сортировки вывода по определенному полю, по возрастанию или убыванию.
Для этого он добавляется к оператору SELECT.

Если выполнить простой запрос SELECT, вывод будет таким:
```sql
sqlite> SELECT * from switch;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255

```

---
### ORDER BY

С помощью оператора ORDER BY можно вывести записи в таблице switch отсортированными их по имени коммутаторов:
```sql
sqlite> SELECT * from switch ORDER BY hostname ASC;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255

```

---
### ORDER BY

По умолчанию сортировка выполняется по возрастанию, поэтому в запросе можно было не указывать параметр ASC:
```sql
sqlite> SELECT * from switch ORDER BY hostname;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
```

---
### ORDER BY

Сортировка по IP-адресу, по убыванию:
```sql
sqlite> SELECT * from switch ORDER BY mngmt_ip DESC;
mac             hostname    model       location           mngmt_ip    mngmt_vid
--------------  ----------  ----------  -----------------  ----------  ----------
0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255
0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255

```

