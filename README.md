# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"

## Обязательная задача 1

Есть скрипт:
```bash
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная  | Значение | Обоснование |
| ------------- | ------------- | ------------- |
| `c`  | `a+b` | `"a" и "b" здесь не являются переменными, т.к. нет указания "$", а "+" это по сути конкатенация строк` |
| `d`  | `1+2`  | `как и в предыдущем варианте с той разницей, что "a" и "b" - переменные, которые по-умолчанию являются строчными в bash'е` |
| `e`  | `3`  | `внутри конструкции "$((...))" произойдет вычисление арифметических операций` |


## Обязательная задача 2
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
```bash
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```

* В первой строке не хватает замыкающей скобки
* Чтобы лог не переполнялся в данном случае, достаточно перезаписывающего файл перенаправления ">"
* Чтобы скрипт завершался, необходимо задать условие выхода из цикла "break"

Исправленный вариант:
```bash
while ((1==1))
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date > curl.log
	else 
	  	break 
	fi
done
```

Необходимо написать скрипт, который проверяет доступность трёх IP: `192.168.0.1`, `173.194.222.113`, `87.250.250.242` по `80` порту и записывает результат в файл `log`. Проверять доступность необходимо пять раз для каждого узла.

### Ваш скрипт:
```bash
#!/usr/bin/env bash
hosts=("192.168.0.1" "173.194.222.113" "87.250.250.242")
declare -i count=0
while (($count<5))
do
        for host in ${hosts[@]}
        do
                nc -zv $host 80 &>> check_ip.log
        done
        count+=1
done````
```
Получим на выходе следующее:
```shell
vagrant@ubuntu-focal:~$ vi check_ip.sh
vagrant@ubuntu-focal:~$ chmod +x check_ip.sh
vagrant@ubuntu-focal:~$ ./check_ip.sh
vagrant@ubuntu-focal:~$ cat check_ip.log
Connection to 192.168.0.1 80 port [tcp/http] succeeded!
Connection to 173.194.222.113 80 port [tcp/http] succeeded!
Connection to 87.250.250.242 80 port [tcp/http] succeeded!
Connection to 192.168.0.1 80 port [tcp/http] succeeded!
Connection to 173.194.222.113 80 port [tcp/http] succeeded!
Connection to 87.250.250.242 80 port [tcp/http] succeeded!
Connection to 192.168.0.1 80 port [tcp/http] succeeded!
Connection to 173.194.222.113 80 port [tcp/http] succeeded!
Connection to 87.250.250.242 80 port [tcp/http] succeeded!
Connection to 192.168.0.1 80 port [tcp/http] succeeded!
Connection to 173.194.222.113 80 port [tcp/http] succeeded!
Connection to 87.250.250.242 80 port [tcp/http] succeeded!
Connection to 192.168.0.1 80 port [tcp/http] succeeded!
Connection to 173.194.222.113 80 port [tcp/http] succeeded!
Connection to 87.250.250.242 80 port [tcp/http] succeeded!
vagrant@ubuntu-focal:~$
```

## Обязательная задача 3
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

### Ваш скрипт:
```bash
#!/usr/bin/env bash
hosts=("192.168.0.1" "173.194.222.113" "192.168.2.46")
declare -i count=0
while (($count<5))
do
        for host in ${hosts[@]}
        do
                nc -zv $host 80 &>> check_ip.log
                if (($?!=0))
                then
                        echo $host > error
                        count=5
                fi
        done
        count+=1
done
```
Для воспроизведения изменил третий IP на собственный и отключил апач. В итоге получил:
```shell
vagrant@ubuntu-focal:~$ ./check_ip.sh
vagrant@ubuntu-focal:~$ cat check_ip.log
Connection to 192.168.0.1 80 port [tcp/http] succeeded!
Connection to 173.194.222.113 80 port [tcp/http] succeeded!
nc: connect to 192.168.2.46 port 80 (tcp) failed: Connection refused
vagrant@ubuntu-focal:~$ cat error
192.168.2.46
vagrant@ubuntu-focal:~$
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: \[04-script-01-bash\] сломал хук.

### Ваш скрипт:
```bash
???
```
