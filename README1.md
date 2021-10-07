# devops-netology

### «3.2. Работа в терминале, лекция 2»
1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.
        
    > Это встроенная команда Bash, которая меняет текущую папку только для оболочки, в которой выполняется. Её нет в файловой системе. 
    Я очень слабо могу представить себе её как отдельную от shell программу.                                                                                                  
        
2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.
 
        grep -c <some_string> <some_file>
                                                                                                                                                                                                                   
3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

    > systemd

4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?

        vagrant@ubuntu-focal:~$ tty
        /dev/pts/0
        vagrant@ubuntu-focal:~$ ls 2>/dev/pts/1
        -bash: /dev/pts/1: Permission denied

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.

        vagrant@ubuntu-focal:~$ cat test.txt
        12345
        123
        45
        12
        vagrant@ubuntu-focal:~$ cat test2.txt
        cat: test2.txt: No such file or directory
        agrant@ubuntu-focal:~$ cat < test.txt > test2.txt
        vagrant@ubuntu-focal:~$ cat test2.txt
        12345
        123
        45
        12
        vagrant@ubuntu-focal:~$

6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?

    > У меня получилось перенаправить и наблюдать
    
        vagrant@ubuntu-focal:~$ tty
        /dev/pts/0
        vagrant@ubuntu-focal:~$ echo Something  > /dev/tty1
        vagrant@ubuntu-focal:~$   
        
    > тем временем в другом терминале:
        
       vagrant@ubuntu-focal:~$ tty
       /dev/tty1
       vagrant@ubuntu-focal:~$ Something
       
       vagrant@ubuntu-focal:~$                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         

7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?

    > Командой bash 5>&1 будет создан дескриптор 5, который будет перенаправлен в стандартный вывод
    
    > Командой  echo netology > /proc/$$/fd/5  будет записано netology в 5-й дескриптор, который был перенаправлен в стандартный вывод                                                                                                                                        

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.
    
    > например так:

        vagrant@ubuntu-focal:~$ ll /root
        ls: cannot open directory '/root': Permission denied
        vagrant@ubuntu-focal:~$ ll /root  | grep Permission -c
        ls: cannot open directory '/root': Permission denied
        0
        vagrant@ubuntu-focal:~$ ll /root 3>&2 2>&1 1>&3  | grep Permission -c
        1
        vagrant@ubuntu-focal:~$ 
        
    > Создали дескриптор 3 и перенаправили в stderr
    
    > stderr перенаправили в stdout

    > stdout перенаправили в дескриптор 9

    > grep поймал ошибку                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            
    
9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?

    > Будут выведены переменные окружения. Аналогичные, но отформатированные данные можно получить командой
        
        vagrant@ubuntu-focal:~$ env                                                                                                                                                                                                                  

10. Используя man, опишите что доступно по адресам /proc/\<PID>/cmdline, /proc/\<PID>/exe.

    > /proc/[pid]/cmdline                        Этот файл только для чтения содержит полную командную строку для процесса, если только процесс не является зомби. В последнем случае в
    этом файле ничего нет: то есть чтение этого файла вернет 0 символов. Аргументы командной строки отображаются в
    этот файл представляет собой набор строк, разделенных нулевыми байтами ('\0'), с дополнительным нулевым байтом после последней строки.

    > /proc/[pid]/exe    В Linux 2.2 и более поздних версиях этот файл представляет собой символическую ссылку, содержащую фактический путь к выполняемой команде. Эта символическая ссылка может быть разыменована в обычном режиме; попытка открыть ее приведет к открытию исполняемого файла. Вы даже можете ввести /proc/[pid]/exe
    чтобы запустить другую копию того же исполняемого файла, который запускается процессом [pid]. Если путь был разорван, символическая ссылка будет содержать строку "(deleted)", добавленную к исходному пути. В многопоточном процессе содержимое
    этой символической ссылки недоступны, если основной поток уже завершен (обычно путем вызова pthread_exit(3)).

11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.
    
    > sse4_2
    
12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:

        vagrant@netology1:~$ ssh localhost 'tty'
        not a tty
    
    Почитайте, почему так происходит, и как изменить поведение.

    > По умолчанию при выполнении команды на удаленной машине с использованием ssh для удаленного сеанса не выделяется TTY. 
    
    > тут возможны как минимум два варианта:  а) либо запустить с флагом -t , что вызывает принудительное создание псевдотерминала:
                                                                                                                                                                                                                                                                                                                                                                                                                                                            
        vagrant@ubuntu-focal:~$ ssh -t localhost 'tty'
        /dev/pts/1
        Connection to localhost closed.                                                                                                                                                                                                                                                                                                                          

    > ... б) либо зайти на сервер по ssh без удаленной команды, тогда TTY выделится:

        vagrant@ubuntu-focal:~$ tty
        /dev/pts/0
        vagrant@ubuntu-focal:~$ ssh localhost
        Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-88-generic x86_64)
        
         * Documentation:  https://help.ubuntu.com
         * Management:     https://landscape.canonical.com
         * Support:        https://ubuntu.com/advantage
        
          System information as of Wed Oct  6 22:15:44 UTC 2021
        
          System load:  0.11              Processes:               124
          Usage of /:   8.4% of 38.71GB   Users logged in:         1
          Memory usage: 55%               IPv4 address for enp0s3: 10.0.2.15
          Swap usage:   0%
        
         * Super-optimized for small spaces - read how we shrank the memory
           footprint of MicroK8s to make it the smallest full K8s around.
        
           https://ubuntu.com/blog/microk8s-memory-optimisation
        
        50 updates can be applied immediately.
        1 of these updates is a standard security update.
        To see these additional updates run: apt list --upgradable
        
        
        Last login: Wed Oct  6 22:12:52 2021 from 127.0.0.1
        vagrant@ubuntu-focal:~$ tty
        /dev/pts/1
        vagrant@ubuntu-focal:~$ exit
        logout
        Connection to localhost closed.
        vagrant@ubuntu-focal:~$                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          

13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.

    > Вот в этом было сложно разобраться. Но каким-то чудом я смог перехватить процесс 37548.

        vagrant@ubuntu-focal:~$ ps -ua
        USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         628  0.0  0.2   7352  2180 ttyS0    Ss+  Oct06   0:00 /sbin/agetty -o -p -- \u --keep-baud 115200,38400,960
        root         641  0.0  0.1   5828  1924 tty1     Ss+  Oct06   0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
        root       37548  0.0  0.2   8984  2932 pts/4    Ss+  09:04   0:00 ping ya.ru
        root       37584  0.0  0.3   8960  3904 pts/3    Ss   09:05   0:00 /bin/bash
        root       37600  6.0  0.1   2592  1948 pts/3    S+   09:05   0:06 reptyr 37548
        vagrant    37746  0.3  0.5  10032  5136 pts/2    Ss   09:07   0:00 -bash
        vagrant    37760  0.0  0.3  10812  3648 pts/2    R+   09:07   0:00 ps -ua
        vagrant@ubuntu-focal:~$                                                                                                                                                                                                                                                               

14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.

      > tee читает из stdin и пишет как в stdout, так и в файл, который был указан в качестве аргумента
                                                                                                                                                                                                                                                                                                                                                                                                                   