## Продолжение обучения после курса


---
## Продолжение обучения

Как правило, информацию тяжело усвоить с первого раза.
Особенно, новую информацию.

Если делать практические задания и пометки, в ходе изучения,
то усвоится намного больше информации, чем, если просто читать книгу.
Скорее всего, в каком-то виде, надо будет читать о той же информации несколько раз.

---
### Идея для финального проекта

[Сбор информации о статусе портов, изменения в статусе](https://github.com/natenka/q_and_a/tree/main/code/04_collect_ports_status)
Задача собрать информацию о статусе портов на оборудовании (up/down/admin down для Cisco IOS):

* На первом этапе собирается информация о всех портах (Loopback/физические/Tunnel/...) и их статусе.
* На втором этапе из собранной информации надо отобрать только физические порты.
* На третьем сохранять информацию о статусе портов в определенный день в файлы и добавить возможность сравнивать изменения в статусе портов (текущий статус с последним записанным).


---
## Бонусные лекции

* работа с базами данных в Python
* новые возможности в Python 3.x
* руководство стилю кода PEP8
* ansible
* scrapli
* vim, tmux

---
### Бонусные лекции. Полезно знать

* аннотация типов
* black
* click (typer)
* logging
* pdb
* pytest
* rich
* python package

---
### Бонусные лекции. Продвинутые темы

* генераторы
* декораторы
* ООП: classmethod, staticmethod, property
* ООП: namedtuple, dataclasses

---
## Продолжение обучения

Курс дает лишь основы Python и поэтому надо обязательно продолжать учиться и повторять уже пройденные темы и изучать новое.
И тут есть множество вариантов:

* автоматизировать что-то в работе
* изучать дальше Python для автоматизации работы с сетью
* изучать Python без привязки к сетевому оборудованию

[Подборка ресурсов](https://natenka.github.io/pyneng-resources/) в которой можно найти и другие материалы.

---
## Написание скриптов для автоматизации рабочих процессов

Скорее всего, после прочтения книги, появятся идеи, что можно автоматизировать на работе.
Это отличный вариант, так как на реальной задаче всегда проще учиться и изучать новое.
Но лучше не ограничиваться только рабочими задачами и изучать Python дальше.

---
## Написание скриптов для автоматизации рабочих процессов

Python позволяет делать достаточно многое обладая только базовыми знаниями.
Поэтому не всегда рабочие задачи позволят принципиально повысить уровень знаний или подтолкнуть к этому.

Но зная Python лучше, те же задачи можно решать, как правило, намного проще.
Поэтому лучше не останавливаться и учиться дальше.

---
## Python для автоматизации работы с сетевым оборудованием

---
### Курсы

[Курсы на Cisco DevNet](https://learninglabs.cisco.com/)

Nicholas Russo:

* [Automating Networks with Python](https://www.pluralsight.com/courses/automating-networks-python)
* [Automating Networks with Ansible the Right Way](https://www.pluralsight.com/courses/automating-networks-ansible-right-way)

John John McGovern. CBT Nuggets:

* [Advanced Network Automation with Cisco and Python. CBT Nuggets](https://www.cbtnuggets.com/it-training/networking/network-automation-cisco-python)


---
### Книги

* [Network Programmability and Automation: Skills for the Next-Generation Network Engineer](https://www.amazon.com/Network-Programmability-Automation-Next-Generation-Engineer/dp/1491931256)
* [Mastering Python Networking (Eric Chou)](https://www.packtpub.com/networking-and-servers/mastering-python-networking) - отчасти перекликается с тем, что рассматривалось в этой книге, но в ней есть и много новых тем. Плюс, рассматриваются примеры не только на оборудовании Cisco, но Juniper и Arista.


---
### Проекты:

* [CiscoConfParse](https://github.com/mpenning/ciscoconfparse) - библиотека, которая парсит конфигурации типа Cisco IOS. С ее помощью можно: проверять существующие конфигурации маршрутизаторов/коммутаторов, получать определенную часть конфигурации, изменять конфигурацию
* [NAPALM](https://github.com/napalm-automation/napalm) - NAPALM (Network Automation and Programmability Abstraction Layer with Multivendor support) это библиотека, которая позволяет работать с сетевым оборудованием разных вендоров, используя унифицированный API
* [NOC Project](https://kb.nocproject.org/display/SITE/NOC) - NOC is the scalable, high-performance and open-source OSS system for ISP, service and content providers
* [Requests](https://github.com/kennethreitz/requests) - библиотека для работы с HTTP
* [SaltStack](https://saltstack.com/) - аналог Ansible

---
### Проекты:

* [Scapy](https://github.com/secdev/scapy) - сетевая утилита, которая позволяет манипулировать сетевыми пакетами
* [StackStorm](https://stackstorm.com/) - StackStorm is event-driven automation commonly used for auto-remediation, security responses, facilitated troubleshooting, complex deployments, and more
* [Nornir](https://github.com/nornir-automation/nornir)
* [eNMS](https://github.com/afourmy/eNMS)
* [netdev](https://github.com/selfuryon/netdev)

---
## Python без привязки к сетевому оборудованию

---
### Книги. Основы

* [Think Python](https://greenteapress.com/wp/think-python-2e/) - хорошая книга по основам Python. В книге есть задания.
* [Python Crash Course: A Hands-On, Project-Based Introduction to Programming](https://www.amazon.com/Python-Crash-Course-Hands-Project-Based-ebook/dp/B018UXJ9RI/) - книга по основам Python. Половина книги посвящена "стандартному" описанию основ Python, а во второй половине эти основы используются для проектов. В книге есть задания.
* [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/). [На русском](https://www.ozon.ru/context/detail/id/137673590/)

---
### Книги. Среднего/продвинутого уровня

[Practices of the Python Pro](https://www.manning.com/books/practices-of-the-python-pro):

* когда использовать наследование, а когда композицию
* о чем надо помнить при написании функций, классов и их взаимодействия
* как писать код, чтобы его было легко масштабировать
* "сколько" кода писать в функции/методе

---
### Книги. Среднего/продвинутого уровня

* [Python Tricks](https://www.amazon.com/Python-Tricks-Buffet-Awesome-Features-ebook/dp/B0785Q7GSY) - отличный вариант для 2-3 книги по Python. В книге описываются различные аспекты Python и то как правильно использовать. Книга достаточно новая (конец 2017 года), в ней рассматривается Python 3.
* [Effective Python: 59 Specific Ways to Write Better Python (Effective Software Development Series)](https://www.amazon.com/Effective-Python-Specific-Software-Development-ebook-dp-B00TKGY0GU/dp/B00TKGY0GU/) - книга полезных советов как лучше писать код. В конце 2019 года [планируется выход второго издания книги](https://www.amazon.com/Effective-Python-Specific-Software-Development/dp/0134853989/).
* [Dive Into Python 3](http://diveintopython3.problemsolving.io/) - коротко рассматриваются основы Python, а затем более продвинутые темы: closure, генераторы, тесты и так далее. Книга 2009 года, но рассматриваюется Python 3 и 99% тем остались без изменений.

---
### Книги. Среднего/продвинутого уровня

* [Problem Solving with Algorithms and Data Structures using Python](https://runestone.academy/runestone/static/pythonds/index.html) - отличная книга по структурам данных и алгоритмам. Много примеров и домашних заданий. [На русском](http://aliev.me/runestone/)
* [Fluent Python](https://www.amazon.com/gp/product/1491946008/) - отличная книга по более продвинутым темам. Даже те темы, которые устарели в текущей версии Python (asyncio) стоит прочитать ради прекрасного объяснения темы.
* [Python Cookbook](https://www.amazon.com/gp/product/1449340377/) - отличная книга рецептов. Рассматривается огромное количество сценариев с решениями и пояснением.

---
### Курсы

* [MITx - 6.00.1x Introduction to Computer Science and Programming Using Python](https://www.edx.org/course/introduction-computer-science-mitx-6-00-1x-9) - очень хороший курспо Python. Отличный вариант для продолжения обучения после книги. В нем вы и повторите пройденный материал по основам Python, но под другим углом и узнаете много нового. В курсе много практических заданий и он достаточно интенсивный.
* [Python от Computer Science Center](https://www.youtube.com/playlist?list=PLlb7e2G7aSpTTNp7HBYzCBByaE1h54ruW) - отличные видеолекции по Python. Тут есть и немного основ и более продвинутые темы

---
### Платные курсы:

* [#100DaysOfCode in Python](https://training.talkpython.fm/courses/explore_100days_in_python/100-days-of-code-in-python)
* [Write Pythonic Code Like a Seasoned Developer](https://training.talkpython.fm/courses/explore_pythonic_code/write-pythonic-code-like-a-seasoned-developer)


---
### Сайты с задачами

* [Bites of Py](https://codechalleng.es/bites/) - очень хороший сайт на котором много заданий разного уровня сложности. На нем вполне можно набить руку и на базовых вещах, но в то же время есть куда расти
* [HackerRank](https://www.hackerrank.com/) - на этом сайте задачи разбиты по областям: алгоритмы, регулярные выражения, базы данных и другие. Но есть и базовые задачи 
* [CheckIO - online game for Python and JavaScript coders](https://checkio.org/)


---
### Полезные ресурсы

* [Real Python](https://realpython.com/)
* [Corey Schafer](https://www.youtube.com/channel/UCCezIgC97PvUuR4_gbFUs5g)

---
### Подкасты

Позволят в целом расширить кругозор и получить представление о разных проектах, модулях и библиотеках Python:

* [Talk Python To Me](https://talkpython.fm/)
* [Best Python Podcasts](https://www.fullstackpython.com/best-python-podcasts.html)

---
### Документация

* [Официальная документация Python](https://docs.python.org/3/index.html)
* [Python Module of the Week](https://pymotw.com/3/index.html)
* [Tiny-Python-3.6-Notebook](https://github.com/mattharrison/Tiny-Python-3.6-Notebook/blob/master/python.rst) - Отличная шпаргалка по Python 3.6


---

![automate](https://raw.githubusercontent.com/natenka/pyneng-slides/course_intro/images/course_intro/automate.jpg)
