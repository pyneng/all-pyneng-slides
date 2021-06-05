## Python для сетевых инженеров 

---
## Инструменты курса

* Slack - общение, вопросы
* Git, GitHub - хранение заданий, проверка заданий
* pyneng - скрипт для проверки заданий, просмотра вариантов решения и сдачи заданий на проверку
* Текстовый редактор: Mu, Geany, ...

---
### Git

---
### Система контроля версий

Система контроля версий (Version Control System, VCS):
* отслеживает изменения в файлах
* хранит несколько версий одного файла
* позволяет откатить изменения
* знает кто и когда сделал изменения

---
### Git

* распределенная система контроля версий
* широко используется
* выпущен под лицензией GNU GPL v2


---
### Git

Файл в Git может быть в таких состояниях:

* committed - файл сохранен в локальной базе
* modified - файл был изменен, но еще не сохранен в локальной базе
* staged - файл отмечен на добавление в следующий commit

---
### Git

![git areas](https://git-scm.com/book/en/v2/images/areas.png)

---
### Git


Три основные части проекта Git:

* каталог Git (.git) - тут хранятся метаданные и база данных объектов проект
* рабочий каталог -  копия определённой версии проекта
* область подготовленных файлов (staging area) - информация о том, что должно попасть в следующий commit

---
### Установка Git

Установка Git
```
$ sudo apt-get install git
```

---
### Основы Git

---
### Первичная настройка Git

Для начала работы с Git необходимо указать имя и email пользователя, которые будут использоваться в commit:

```
$ git config --global user.name "username"
$ git config --global user.email "username@example.com"
```

Посмотреть настройки можно таким образом:
```
$ git config --list
```

---
### Инициализация репозитория

```
[~/tools/first_repo]
$ git init
Initialized empty Git repository in /home/vagrant/tools/first_repo/.git/
```

После команды git init, в каталоге создается каталог .git, в котором содержится вся информация, которая необходима для работы Git.


---
### Работа с Git

---
### git status

![alt](https://pyneng.github.io/assets/images/git_status_0.png)

Git сообщает, что мы находимся в ветке master (эта ветка создается сама и используется по умолчанию) и что ему нечего добавлять в commit.
Кроме этого, git предлагает создать или скопировать файлы и после этого воспользоваться командой git add, чтобы git начал за ними следить.

---
### Работа с Git

Создадим первый файл README и добавим в него пару произвольных строк текста:

![alt](https://pyneng.github.io/assets/images/vi_readme.png)

После этого приглашение выглядит таким образом:

![alt](https://pyneng.github.io/assets/images/bash_prompt.png)

---
### Работа с Git

В приглашении показано, что есть два файла, за которыми git еще не следит.
Посмотрим в git status откуда взялся второй файл:

![alt](https://pyneng.github.io/assets/images/git_status_1.png)

Git сообщает, что есть файлы за которыми он не следит, подсказывает какой командой это сделать.

---
### .gitignore

.README.un~ - это служебный файл, который не нужно добавлять в репозиторий.

В git есть возможность сказать, что какие-то файлы или каталоги нужно игнорировать.
Для этого, надо указать соответствующие шаблоны в файле .gitignore в текущем каталоге:

Для того чтобы git игнорировал undo файлы vim, можно добавить, например, такую строку в файл .gitignore:
```
*.un~
```

Это значит, что Git должен игнорировать все файлы, которые заканчиваются на ```.un~```.

---
### .gitignore

После этого, git status показывает:

![alt](https://pyneng.github.io/assets/images/git_status_2.png)


---
### git add

Для того чтобы Git начал следить за файлами, используется команда git add.

Можно указать, что надо следить за конкретным файлом:

![alt](https://pyneng.github.io/assets/images/git_add_readme.png)

Или за всеми файлами:

![alt](https://pyneng.github.io/assets/images/git_add_all.png)

---
### git add

Вывод git status после добавления файлов:

![alt](https://pyneng.github.io/assets/images/git_status_3.png)


Теперь файлы находятся в секции "Changes to be committed".

---
### git commit

После того как все нужные файлы были добавлены в staging, можно закоммитить изменения.

У команды git commit есть только один обязательный параметр - флаг ```-m```.
Он позволяет указать сообщение для этого коммита:

![alt](https://pyneng.github.io/assets/images/git_commit_1.png)

---
### git commit


После этого, git status отображает:

![alt](https://pyneng.github.io/assets/images/git_status_4.png)


Фраза "working directory clean" обозначает, что нет изменений, которые нужно добавить в Git или закоммитить.


---
### Дополнительные возможности git

---
### git diff

Команда git diff позволяет просмотреть разницу между различными состояниями.

Например, внесем изменения в файл README и .gitignore, но не будем добавлять их в репозиторий.
Команда git status показывает, что оба файла изменены:

![alt](https://pyneng.github.io/assets/images/git_status_5.png)

---
### git diff

Команда git diff показывает какие изменения были внесены с последнего коммита:

![alt](https://pyneng.github.io/assets/images/git_diff.png)


---
### git diff

Если теперь добавить изменения в файлах, и ещё раз выполнить команду git diff, она ничего не покажет:

![alt](https://pyneng.github.io/assets/images/git_add_git_diff.png)

---
### git diff

Чтобы показать отличия между staging и последним коммитом, надо добавить параметр --staged (или --cached):

![alt](https://pyneng.github.io/assets/images/git_diff_staged.png)


---
### git log

По умолчанию команда git log показывает все коммиты, начиная с самого свежего:

![alt](https://pyneng.github.io/assets/images/git_log.png)


---
### git log

Флаг -p позволяет отобразить отличия, которые были внесены каждым коммитом:

![alt](https://pyneng.github.io/assets/images/git_log_p.png)

---
### git log

Более короткий вариант вывода можно вывести с флагом ```--stat```:

![alt](https://pyneng.github.io/assets/images/git_log_stat.png)


