# devops-netology

### «3.6. Компьютерные сети, лекция 1»
1.  Работа c HTTP через телнет.
   * Подключитесь утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80
   * отправьте HTTP запрос
```GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
```
   * В ответе укажите полученный HTTP код, что он означает?
---
```buildoutcfg
vagrant@ubuntu-focal:~$ telnet stackoverflow.com 80
Trying 151.101.129.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.1 301 Moved Permanently
cache-control: no-cache, no-store, must-revalidate
location: https://stackoverflow.com/questions
x-request-guid: f9ea2aa0-7212-4c1f-9cc0-8c0aad3b7276
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Accept-Ranges: bytes
Date: Thu, 06 Jan 2022 22:35:20 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-hhn4080-HHN
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1641508521.580849,VS0,VE170
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=bad74251-2482-4998-0d67-fd18ced79725; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly

Connection closed by foreign host.
vagrant@ubuntu-focal:~$
```
В данном случае код редиректа 301 говорит о том, что ресурс перемещен на https://stackoverflow.com/questions

---
---
2. Повторите задание 1 в браузере, используя консоль разработчика F12.
* откройте вкладку Network
* отправьте запрос http://stackoverflow.com
* найдите первый ответ HTTP сервера, откройте вкладку Headers
* укажите в ответе полученный HTTP код.
* проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
* приложите скриншот консоли браузера в ответ.
---
Первый запрос:
```buildoutcfg
Request URL: http://stackoverflow.com/
Request Method: GET
Status Code: 307 Internal Redirect
Referrer Policy: strict-origin-when-cross-origin
Location: https://stackoverflow.com/
Non-Authoritative-Reason: HSTS
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.85 YaBrowser/21.11.4.727 Yowser/2.5 Safari/537.36
```
Страница загрузилась за 637 мс. Дольше всех обрабатывался второй запрос (303 мс), т.е. первый успешный запрос (с кодом 200):

![stackoverflow_network](C:\Dev\devops-netology\img\stackoverflow_page.png "stackoverflow")

---
---
3. Какой IP адрес у вас в интернете?
---
![whoer](C:\Dev\devops-netology\img\whoer.png "whoer")

---
---

4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois
---
```buildoutcfg
vagrant@ubuntu-focal:~$ whois 93.185.192.92 | grep 'netname'
netname:        AVIEL-NET
vagrant@ubuntu-focal:~$ whois 93.185.192.92 | grep 'origin'
origin:         AS35271
vagrant@ubuntu-focal:~$
```

---
---

5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute
---
```buildoutcfg
vagrant@ubuntu-focal:~$ traceroute -IAn 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.0.2.2 [*]  0.205 ms  0.166 ms  0.150 ms
 2  192.168.2.1 [*]  1.296 ms  1.278 ms  1.310 ms
 3  10.110.202.1 [*]  1.491 ms  1.540 ms  1.515 ms
 4  10.110.2.128 [*]  10.991 ms  10.967 ms  10.991 ms
 5  * * *
 6  10.110.2.37 [*]  1.200 ms  1.533 ms  1.754 ms
 7  10.110.2.54 [*]  4.293 ms  1.925 ms  1.901 ms
 8  195.208.208.250 [AS5480]  3.505 ms  3.628 ms  3.782 ms
 9  108.170.250.113 [AS15169]  12.655 ms  12.555 ms  12.681 ms
10  * * *
11  172.253.66.110 [AS15169]  21.254 ms  21.075 ms  20.829 ms
12  209.85.254.135 [AS15169]  18.812 ms  19.621 ms  19.072 ms
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  8.8.8.8 [AS15169]  20.993 ms *  18.831 ms
vagrant@ubuntu-focal:~$
```

---
---

6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?
---
```buildoutcfg
                                   My traceroute  [v0.93]
ubuntu-focal (10.0.2.15)                                           2022-01-06T23:48:12+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                   Packets               Pings
 Host                                            Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. AS???    10.0.2.2                             0.0%    29    0.4   0.4   0.3   0.7   0.1
 2. AS???    192.168.2.1                          0.0%    29    1.3   1.2   1.0   1.4   0.1
 3. AS???    10.110.202.1                         0.0%    29    1.4   2.1   1.2   6.7   1.5
 4. AS???    10.110.2.128                         0.0%    29  545.8  29.1   2.0 545.8 102.3
 5. (waiting for reply)
 6. AS???    10.110.2.37                          0.0%    29    1.8   2.0   1.4   6.4   1.2
 7. AS???    10.110.2.54                          0.0%    29    1.5   1.7   1.5   2.7   0.2
 8. AS???    195.208.208.250                      0.0%    29    3.4   5.1   3.2  33.3   5.5
 9. AS???    108.170.250.113                      0.0%    29    3.4   4.8   3.3  31.8   5.3
10. AS???    216.239.51.32                       75.0%    29   20.6  21.1  20.5  23.2   1.0
11. AS???    172.253.66.110                       0.0%    29   21.2  21.5  20.7  25.8   1.1
12. AS???    209.85.254.135                       0.0%    29   21.7  22.4  21.4  25.5   1.0
13. (waiting for reply)
14. (waiting for reply)
15. (waiting for reply)
16. (waiting for reply)
17. (waiting for reply)
18. (waiting for reply)
19. (waiting for reply)
20. (waiting for reply)
21. (waiting for reply)
22. AS???    8.8.8.8                             21.4%    28   22.5  21.6  17.8  29.2   3.2

```
Есть также вариант со включенным X11-Forwarding'ом:

![mtr](C:\Dev\devops-netology\img\mtr.png "mtr")

В любом случае самые большие задержки получиличь на 4-м хопе (10.110.2.128)

---
---
7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig
---
```buildoutcfg
vagrant@ubuntu-focal:~$ dig NS dns.google

; <<>> DiG 9.16.1-Ubuntu <<>> NS dns.google
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59856
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google.                    IN      NS

;; ANSWER SECTION:
dns.google.             20741   IN      NS      ns2.zdns.google.
dns.google.             20741   IN      NS      ns4.zdns.google.
dns.google.             20741   IN      NS      ns1.zdns.google.
dns.google.             20741   IN      NS      ns3.zdns.google.

;; Query time: 20 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Fri Jan 07 00:27:17 UTC 2022
;; MSG SIZE  rcvd: 116

vagrant@ubuntu-focal:~$ dig dns.google

; <<>> DiG 9.16.1-Ubuntu <<>> dns.google
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33855
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;dns.google.                    IN      A

;; ANSWER SECTION:
dns.google.             840     IN      A       8.8.4.4
dns.google.             840     IN      A       8.8.8.8

;; Query time: 20 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Fri Jan 07 00:27:30 UTC 2022
;; MSG SIZE  rcvd: 71

vagrant@ubuntu-focal:~$

```

---
---
8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig
---
```buildoutcfg
vagrant@ubuntu-focal:~$ dig -x 8.8.4.4

; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 55179
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.   19797   IN      PTR     dns.google.

;; Query time: 20 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Fri Jan 07 00:30:26 UTC 2022
;; MSG SIZE  rcvd: 73

vagrant@ubuntu-focal:~$ dig -x 8.8.8.8

; <<>> DiG 9.16.1-Ubuntu <<>> -x 8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 21916
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;8.8.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.   18665   IN      PTR     dns.google.

;; Query time: 16 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Fri Jan 07 00:30:33 UTC 2022
;; MSG SIZE  rcvd: 73

vagrant@ubuntu-focal:~$

```
К обоим IP привязан домен _dns.google._
