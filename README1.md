# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Traceback (most recent call last):  File "<stdin>", line 1, in <module>TypeError: unsupported operand type(s) for +: 'int' and 'str'|
| Как получить для переменной `c` значение 12?  | c = str(a) + b  |
| Как получить для переменной `c` значение 3?  | c = a + int(b)  |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3
import os
bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os


repo_path = "~/devops-netology"
norm_repo_path = os.path.normpath(os.path.expanduser(repo_path))
# print(repo_path)
# print(norm_repo_path)
bash_command = [f"cd {norm_repo_path}", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
# is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(os.path.join(norm_repo_path, prepare_result))
    else:
        pass

```

### Вывод скрипта при запуске при тестировании:
```shell
vagrant@ubuntu-focal:~/devops-netology$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README0.md
        modified:   README3.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        git_check.py

no changes added to commit (use "git add" and/or "git commit -a")
vagrant@ubuntu-focal:~/devops-netology$
vagrant@ubuntu-focal:~/devops-netology$ ./git_check.py
/home/vagrant/devops-netology/README0.md
/home/vagrant/devops-netology/README3.md
vagrant@ubuntu-focal:~/devops-netology$ 
```


## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os, sys

try:
    repo_path = sys.argv[1]
except IndexError:
    print("Please run the script with the repository path")
    exit()
norm_repo_path = os.path.normpath(os.path.expanduser(repo_path))
try:
    os.chdir(norm_repo_path)
except FileNotFoundError:
    print("There is no such folder in the path. Set the correct path.")
    exit()
file_list = os.popen('ls -a').read().split('\n')
if '.git' not in file_list:
    print("There is no repository in this directory.\n \
    Please try again by specifying a different path.")
else:
    result_list = os.popen('git status --porcelain').read().split('\n')
    changed_files_dict = {}
    for line in result_list:
        line_list = line.split(' ')
        # print(line_list)
        for i in range(len(line_list)-1):
            # print(line, line_list[i])
            if line_list[i] in ['M','A', 'AM', '??']:
                if line_list[i] == 'A':
                    changed_files_dict.update({line_list[-1]: 'Added'})
                elif line_list[i] == 'M':
                    changed_files_dict.update({line_list[-1]: 'Modified'})
                elif line_list[i] == 'AM':
                    changed_files_dict.update({line_list[-1]: 'Added and Modified'})
                else:
                    changed_files_dict.update({line_list[-1]: 'Untracked'})
    for key, value in changed_files_dict.items():
        print(f"{key} -- {value}")
	if changed_files_dict == {}:
    	print('Not changed in this repository')
```

### Вывод скрипта при запуске при тестировании:
```shell
vagrant@ubuntu-focal:~$ cp -r devops-netology/ devops-netology2/
vagrant@ubuntu-focal:~$
vagrant@ubuntu-focal:~$ cp devops-netology/git_check.py git_check.py
vagrant@ubuntu-focal:~$
vagrant@ubuntu-focal:~$ ./git_check.py
Please run the script with the repository path
vagrant@ubuntu-focal:~$ ./git_check.py s
There is no such folder in the path. Set the correct path.
vagrant@ubuntu-focal:~$ ./git_check.py ~/devops-netology/
README0.md -- Modified
README3.md -- Modified
git_check.py -- Added and Modified
vagrant@ubuntu-focal:~$ ./git_check.py ~/devops-netology2/
README0.md -- Modified
README3.md -- Modified
git_check.py -- Added and Modified
vagrant@ubuntu-focal:~$
vagrant@ubuntu-focal:~$ ./git_check.py ~/.ssh
There is no repository in this directory.
     Please try again by specifying a different path.
vagrant@ubuntu-focal:~$
vagrant@ubuntu-focal:~/devops-netology$ git add *
vagrant@ubuntu-focal:~/devops-netology$ git commit -m 'comment'
[main 0c87030] comment
 3 files changed, 44 insertions(+), 24 deletions(-)
 rewrite git_check.py (82%)
vagrant@ubuntu-focal:~/devops-netology$ ./git_check.py ~/devops-netology/
Not changed in this repository
vagrant@ubuntu-focal:~/devops-netology$

```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket

hosts = {"drive.google.com": "64.233.165.194", 
         "mail.google.com": "192.168.0.1",
         "google.com": "74.125.205.139"}
for host, ip in hosts.items():
	old_ip = ip
	new_ip = socket.gethostbyname(host)
	if old_ip != new_ip:
		print(f'[ERROR] "{host}" IP mismatch: "{old_ip}" "{new_ip}"')
	print(f'"{host}" - "{new_ip}"')

```

### Вывод скрипта при запуске при тестировании:
```shell
vagrant@ubuntu-focal:~$ vi ip_check.py
vagrant@ubuntu-focal:~$ chmod +x ip_check.py
vagrant@ubuntu-focal:~$ ./ip_check.py
"drive.google.com" - "64.233.165.194"
[ERROR] "mail.google.com" IP mismatch: "192.168.0.1" "64.233.161.83"
"mail.google.com" - "64.233.161.83"
"google.com" - "74.125.205.139"
vagrant@ubuntu-focal:~$

```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```