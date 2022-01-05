# devops-netology

### «3.4. Операционные системы, лекция 2»
1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
    поместите его в автозагрузку,
    предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
    удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
    
    Установлен.  
    <img src="/img/9100.png" alt="9100" title="9100" />
    
    Создал конфиг:
            
            root@ubuntu-focal:/home/vagrant# cat /etc/systemd/system/node_exporter.service
            [Unit]
            Description=Node Exporter
            
            [Service]
            ExecStart=/home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter $OPTIONS
            EnvironmentFile=-/etc/default/node_exporter
            KillMode=process
            Restart=on-failure
            
            [Install]
            WantedBy=multi-user.target
    
    Сервис успешно стартует.
            
            root@ubuntu-focal:/home/vagrant/node_exporter-1.3.1.linux-amd64# systemctl start node_exporter.service
            root@ubuntu-focal:/home/vagrant/node_exporter-1.3.1.linux-amd64# systemctl status node_exporter
            ● node_exporter.service - Node Exporter
                 Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
                 Active: active (running) since Tue 2022-01-04 18:18:56 UTC; 5s ago
               Main PID: 3443 (node_exporter)
                  Tasks: 4 (limit: 1136)
                 Memory: 13.5M
                 CGroup: /system.slice/node_exporter.service
                         └─3443 /home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter
            root@ubuntu-focal:/home/vagrant/node_exporter-1.3.1.linux-amd64# ps -e |grep node_exporter
            3443 ?        00:00:00 node_exporter
    
    Добавляю сервис в автозагрузу:
            
            sudo systemctl enable node_exporter
    
    Остановка и запуск:
    
            root@ubuntu-focal:/home/vagrant# systemctl stop node_exporter.service
            root@ubuntu-focal:/home/vagrant# systemctl status node_exporter.service
            ● node_exporter.service - Node Exporter
                 Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
                 Active: inactive (dead) since Wed 2022-01-05 07:56:24 UTC; 2s ago
                Process: 77526 ExecStart=/home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter $OPTIONS (code=killed, signal=TERM)
               Main PID: 77526 (code=killed, signal=TERM)
            
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=node_exporter.go:115 level=info collector=udp_queues
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=node_exporter.go:115 level=info collector=uname
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=node_exporter.go:115 level=info collector=vmstat
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=node_exporter.go:115 level=info collector=xfs
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=node_exporter.go:115 level=info collector=zfs
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
            Jan 05 07:53:22 ubuntu-focal node_exporter[77526]: ts=2022-01-05T07:53:22.225Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
            Jan 05 07:56:24 ubuntu-focal systemd[1]: Stopping Node Exporter...
            Jan 05 07:56:24 ubuntu-focal systemd[1]: node_exporter.service: Succeeded.
            Jan 05 07:56:24 ubuntu-focal systemd[1]: Stopped Node Exporter.
            root@ubuntu-focal:/home/vagrant#
            root@ubuntu-focal:/home/vagrant#
            root@ubuntu-focal:/home/vagrant# systemctl start node_exporter.service
            root@ubuntu-focal:/home/vagrant#
            root@ubuntu-focal:/home/vagrant# systemctl status node_exporter.service
            ● node_exporter.service - Node Exporter
                 Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
                 Active: active (running) since Wed 2022-01-05 07:56:50 UTC; 3s ago
               Main PID: 77732 (node_exporter)
                  Tasks: 4 (limit: 1136)
                 Memory: 3.1M
                 CGroup: /system.slice/node_exporter.service
                         └─77732 /home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter
            
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=thermal_zone
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=time
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=timex
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=udp_queues
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=uname
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=vmstat
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=xfs
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:50.999Z caller=node_exporter.go:115 level=info collector=zfs
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:51.000Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
            Jan 05 07:56:51 ubuntu-focal node_exporter[77732]: ts=2022-01-05T07:56:51.001Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
            root@ubuntu-focal:/home/vagrant#
    
    После перезагрузки процесс успешно поднимается автоматически:
    
            77 updates can be applied immediately.
            To see these additional updates run: apt list --upgradable
            
            
            Last login: Tue Jan  4 18:24:45 2022 from 10.0.2.2
            vagrant@ubuntu-focal:~$ sudo su
            root@ubuntu-focal:/home/vagrant#
            root@ubuntu-focal:/home/vagrant#
            root@ubuntu-focal:/home/vagrant#
            root@ubuntu-focal:/home/vagrant# systemctl status node_exporter.service
            ● node_exporter.service - Node Exporter
                 Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
                 Active: active (running) since Wed 2022-01-05 07:59:11 UTC; 36s ago
               Main PID: 637 (node_exporter)
                  Tasks: 4 (limit: 1131)
                 Memory: 3.7M
                 CGroup: /system.slice/node_exporter.service
                         └─637 /home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter
            
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=thermal_zone
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=time
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=timex
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=udp_queues
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=uname
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=vmstat
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=xfs
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:115 level=info collector=zfs
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.768Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
            Jan 05 07:59:11 ubuntu-focal node_exporter[637]: ts=2022-01-05T07:59:11.778Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
            root@ubuntu-focal:/home/vagrant#                                                                                                                    

2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

    CPU:

        node_cpu_seconds_total{cpu="0",mode="idle"} 478.29
        node_cpu_seconds_total{cpu="0",mode="iowait"} 9.85
        node_cpu_seconds_total{cpu="0",mode="irq"} 0
        node_cpu_seconds_total{cpu="0",mode="nice"} 0
        node_cpu_seconds_total{cpu="0",mode="softirq"} 2.44
        node_cpu_seconds_total{cpu="0",mode="steal"} 0
        node_cpu_seconds_total{cpu="0",mode="system"} 7.7
        node_cpu_seconds_total{cpu="0",mode="user"} 10.43
        node_cpu_seconds_total{cpu="1",mode="idle"} 477.52
        node_cpu_seconds_total{cpu="1",mode="iowait"} 10.73

    Память:

        node_memory_Active_anon_bytes 6.1523968e+08
        node_memory_Active_bytes 7.0637568e+08
        node_memory_Active_file_bytes 9.1136e+07
        node_memory_AnonHugePages_bytes 0

    Диск:
    
        node_disk_io_time_seconds_total{device="sda"} 30.164
        node_disk_io_time_seconds_total{device="sdb"} 1.412
        node_disk_io_time_seconds_total{device="sr0"} 0
        node_disk_io_time_weighted_seconds_total{device="sda"} 19.072
        node_disk_io_time_weighted_seconds_total{device="sdb"} 1.724
        node_disk_io_time_weighted_seconds_total{device="sr0"} 0
        
    Сеть:
    
        node_network_address_assign_type{device="enp0s3"} 0
        node_network_address_assign_type{device="lo"} 0
        node_network_carrier{device="enp0s3"} 1
        node_network_carrier{device="lo"} 1r
        node_network_carrier_changes_total{device="enp0s3"} 2
        node_network_carrier_changes_total{device="lo"} 0

3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:
    ###### в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0,
    ###### добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload:
        config.vm.network "forwarded_port", guest: 19999, host: 19999
    После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на localhost:19999. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
    
    Успех:  
    <img src="/img/netdata.png" alt="netdata" title="netdata" />
    
4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
    ###### Судя по ответу команды, ДА:
     
        root@ubuntu-focal:/home/vagrant# dmesg | grep virtual
        [    0.012644] CPU MTRRs all blank - virtualized system.
        [    0.216818] Booting paravirtualized kernel on KVM
        [    5.373059] systemd[1]: Detected virtualization oracle.
        root@ubuntu-focal:/home/vagrant#
        
5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
       
       root@ubuntu-focal:/home/vagrant# sysctl -n fs.nr_open
       1048576
       root@ubuntu-focal:/home/vagrant# cat /proc/sys/fs/nr_open
       1048576

    Это максимальное количество открытых файловых дескрипторов(системное ограничение).
    Есть также пользовательские лимиты мягкий и жесткий(Hard, Soft):
    
        root@ubuntu-focal:/home/vagrant# ulimit -Sn
        1024
        root@ubuntu-focal:/home/vagrant# ulimit -Hn
        1048576

   Мягкий лимит может быть увеличен. Жесткий не может быть увеличен.
   Но ни тот, ни другой лимиты не могут превысить системный fs.nr_open

    
6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.

        root@ubuntu-focal:/home/vagrant# unshare -f --pid --mount-proc sleep 1h
        bg
        ^Z
        [1]+  Stopped                 unshare -f --pid --mount-proc sleep 1h
        root@ubuntu-focal:/home/vagrant# ps -e | grep sleep
           6955 pts/0    00:00:00 sleep
        root@ubuntu-focal:/home/vagrant# nsenter -t 6955 -p -m
        root@ubuntu-focal:/# ps
           PID TTY          TIME CMD
             1 pts/0    00:00:00 sleep
             2 pts/0    00:00:00 bash
            13 pts/0    00:00:00 ps
        root@ubuntu-focal:/#   
   
7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
   
   ###### По всей видимости это fork-бомба — вредоносная или ошибочно написанная программа, бесконечно создающая свои копии (системным вызовом fork()), которые обычно также начинают создавать свои копии и т. д.
   По информации dmesg:
   
       [  226.114088] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope

   Т.е. сработал сgroups - это способ ограничить ресурсы внутри конкретной группы процессов.
   
   Текущие ограничения можно увидеть командой:
    
       root@ubuntu-focal:/home/vagrant# ulimit -u
       15587

   Изменить лимит можно командой:
   ###### ulimit -u 100
   , где 100 - это новое ограничение.