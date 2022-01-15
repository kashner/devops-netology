# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

Ответ:

---

	{ "info" : "Sample JSON output from our service\t", //не понятно, так и должно быть, или вместо "\t" должно быть "   "
		"elements" :[  //тут я бы поставил пробел не до, а после двоеточия
			{ "name" : "first",
            "type" : "server",
            "ip" : 7175 // не совсем понятно, возможно ошибка данных в источнике, т.к. на ip не похоже
            }  // здесь явно не хватает запятой
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43  // ключ всегда строковый, и значение здесь тоже строка, оба в кавычках
            }
		]
	}


Вот такой формат будет вполне валидным:

```json
{
	"info": "Sample JSON output from our service\t",
	"elements": [
		{
			"name": "first",
			"type": "server",
			"ip": 7175
		},
		{
			"name": "second",
			"type": "proxy",
			"ip": "71.78.22.43"
		}
	]
}
```

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket, json, yaml


hosts = {"drive.google.com": "64.233.165.194", 
         "mail.google.com": "192.168.0.1",
         "google.com": "74.125.205.139"}
json_config = "ip_check.json"
yaml_config = "ip_check.yaml"
new_hosts_dict = {}
new_hosts_list = []
for host, ip in hosts.items():
	old_ip = ip
	new_ip = socket.gethostbyname(host)
	if old_ip != new_ip:
		print(f'[ERROR] "{host}" IP mismatch: "{old_ip}" "{new_ip}"')
	print(f'"{host}" - "{new_ip}"')
	new_hosts_dict.update({host : new_ip})
	new_hosts_list.append(f'{host}: {new_ip}')
with open(json_config, "w") as j:
	json.dump(new_hosts_dict, j, indent=2)
with open(yaml_config, "w") as y:
	yaml.dump(new_hosts_list, y, sort_keys=False, default_flow_style=False)

```

### Вывод скрипта при запуске при тестировании:
```
vagrant@ubuntu-focal:~$ ./ip_check_2.py
"drive.google.com" - "64.233.165.194"
[ERROR] "mail.google.com" IP mismatch: "192.168.0.1" "64.233.161.83"
"mail.google.com" - "64.233.161.83"
"google.com" - "74.125.205.139"
vagrant@ubuntu-focal:~$ cat ip_check.json
{
  "drive.google.com": "64.233.165.194",
  "mail.google.com": "64.233.161.83",
  "google.com": "74.125.205.139"
}vagrant@ubuntu-focal:~$
vagrant@ubuntu-focal:~$
vagrant@ubuntu-focal:~$ cat ip_check.yaml
- 'drive.google.com: 64.233.165.194'
- 'mail.google.com: 64.233.161.83'
- 'google.com: 74.125.205.139'
vagrant@ubuntu-focal:~$

```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{
  "drive.google.com": "64.233.165.194",
  "mail.google.com": "64.233.161.83",
  "google.com": "74.125.205.139"
}
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
- 'drive.google.com: 64.233.165.194'
- 'mail.google.com: 64.233.161.83'
- 'google.com: 74.125.205.139'

```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
???
```

### Пример работы скрипта:
???