# nslookup (практика)
# Белых Владислав кн-202

## 1. OK
## 2.
Примечание: DHCP УрФУ предоставляет несколько ip-шников DNS серверов, первый из которых почему-то не отвечает на запросы, поэтому я использовал второй: 10.98.241.10.
* urfu.ru
```
> nslookup -type=ns urfu.ru. 10.98.241.10
╤хЁтхЁ:  t04-505-pdc-pri.at.urfu.ru
Address:  10.98.241.10

urfu.ru nameserver = ns2.urfu.ru
urfu.ru nameserver = ns3.urfu.ru
urfu.ru nameserver = ns1.urfu.ru
ns2.urfu.ru     internet address = 212.193.82.21
ns3.urfu.ru     internet address = 212.193.72.21
ns1.urfu.ru     internet address = 212.193.66.21
```
* msu.ru
```
> nslookup -type=ns msu.ru. 10.98.241.10
╤хЁтхЁ:  t04-505-pdc-pri.at.urfu.ru
Address:  10.98.241.10

Не заслуживающий доверия ответ:
msu.ru  nameserver = ns.msu.net
msu.ru  nameserver = ns.msu.ru
msu.ru  nameserver = ns1.orc.ru
msu.ru  nameserver = ns3.nic.fr

ns.msu.ru       internet address = 93.180.0.1
ns1.orc.ru      internet address = 212.48.128.152
ns3.nic.fr      internet address = 192.134.0.49
ns3.nic.fr      AAAA IPv6 address = 2001:660:3006:1::1:1
```
IP-адреса хостов для символьных имен:
* urfu.ru
```
> nslookup urfu.ru. 10.98.241.10
╤хЁтхЁ:  t04-505-pdc-pri.at.urfu.ru
Address:  10.98.241.10

╚ь :     urfu.ru
Address:  212.193.82.20
```
* rbc.ru
```
> nslookup rbc.ru. 10.98.241.10
╤хЁтхЁ:  t04-505-pdc-pri.at.urfu.ru
Address:  10.98.241.10

Не заслуживающий доверия ответ:
╚ь :     rbc.ru
Addresses:  185.72.229.9
          80.68.253.9
```
## 3.
Имя и адрес:
```
╤хЁтхЁ:  UnKnown
Address:  10.114.240.10
```
получаем из запроса:
```
> msu.ru.
╤хЁтхЁ:  UnKnown
Address:  10.114.240.10

DNS request timed out.
    timeout was 2 seconds.
DNS request timed out.
    timeout was 2 seconds.
*** Превышено время ожидания запроса UnKnown
```
## 4.
* server NAME работает так:<br/>
Если в качестве NAME указан IP адрес (```> server 194.226.235.1```), команда посылает запрос [1.235.226.194.in-addr.arpa: type PTR, class IN], а затем независимо был ли получен ответ от сервера или нет, фиксирует IP указанного сервера.<br/>
Если в качестве NAME указано доменное имя (```> server ns1.urfu.ru```), команда пытается разрешить его в IP адрес и посылает запрос указанному ранее DNS серверу (в данном случае 194.226.235.1). В случае неудачи, сервер NAME не фиксируется в качестве резолвера.<br/>
Т.к. 194.226.235.1 не существует, то ```server NAME``` не изменит резолвер.
* lserver NAME работает так:
Аналогично ```server NAME```, но запросы отсылаются не серверу, указанному последним, а серверу, указанному в самом начале.

Здесь при переходе к ns1.urfu.ru возникает такая проблема: т.к. изначально от DHCP сервера был получен не функционирующий DNS сервер, то изменить резолвер просто ```lserver NAME``` не получится. Нужно либо указать IP адрес доменного имени, либо изменить сервер на функционирующий командой ```server NAME```. Я выбрал первый вариант:
```
> lserver 212.193.66.21
DNS request timed out.
    timeout was 2 seconds.
╤хЁтхЁ яю єьюыўрэш■:  [212.193.66.21]
Address:  212.193.66.21
```
(ip ns1 получили в первом задании).
* root
Примечание: в задании сказано "Обратить внимание на то, что адрес корневого сервера известен, несмотря на то, что он задан символьным именем.", но в моем случае почему-то это не так и nslookup перед переходом пытается резолвить a.root-servers.net.

Переход на несуществующий сервер не осложнил процесс, т.к. команда root пытается резолвить a.root-servers.net через первоначальный сервер (от DHCP). Однако, как упоминалось ранее, с первоначальным сервером проблема, поэтому перейти на root можно так:
```
> set root=198.41.0.4
> root
DNS request timed out.
    timeout was 2 seconds.
╤хЁтхЁ яю єьюыўрэш■:  [198.41.0.4]
Address:  198.41.0.4

> www.yandex.ru
╤хЁтхЁ:  [198.41.0.4]
Address:  198.41.0.4

╚ь :     www.yandex.ru
Served by:
- a.dns.ripn.net
          193.232.128.6
          2001:678:17:0:193:232:128:6
          ru
- e.dns.ripn.net
          193.232.142.17
          2001:678:15:0:193:232:142:17
          ru
- f.dns.ripn.net
          193.232.156.17
          2001:678:14:0:193:232:156:17
          ru
- d.dns.ripn.net
          194.190.124.17
          2001:678:18:0:194:190:124:17
          ru
- b.dns.ripn.net
          194.85.252.62
          2001:678:16:0:194:85:252:62
          ru
```
Сначала я разрешил имя корневого сервера в IP адрес вне командного режима nslookup (можно было правда и в нем через server 8.8.8.8), затем записал его в root и уже вызвал команду.
## 5.
* com:
```
> set type=ns
> com.
╤хЁтхЁ:  [198.41.0.4]
Address:  198.41.0.4

com     nameserver = e.gtld-servers.net
com     nameserver = b.gtld-servers.net
com     nameserver = j.gtld-servers.net
com     nameserver = m.gtld-servers.net
com     nameserver = i.gtld-servers.net
com     nameserver = f.gtld-servers.net
com     nameserver = a.gtld-servers.net
com     nameserver = g.gtld-servers.net
com     nameserver = h.gtld-servers.net
com     nameserver = l.gtld-servers.net
com     nameserver = k.gtld-servers.net
com     nameserver = c.gtld-servers.net
com     nameserver = d.gtld-servers.net
e.gtld-servers.net      internet address = 192.12.94.30
e.gtld-servers.net      AAAA IPv6 address = 2001:502:1ca1::30
b.gtld-servers.net      internet address = 192.33.14.30
b.gtld-servers.net      AAAA IPv6 address = 2001:503:231d::2:30
j.gtld-servers.net      internet address = 192.48.79.30
j.gtld-servers.net      AAAA IPv6 address = 2001:502:7094::30
m.gtld-servers.net      internet address = 192.55.83.30
m.gtld-servers.net      AAAA IPv6 address = 2001:501:b1f9::30
i.gtld-servers.net      internet address = 192.43.172.30
i.gtld-servers.net      AAAA IPv6 address = 2001:503:39c1::30
f.gtld-servers.net      internet address = 192.35.51.30
f.gtld-servers.net      AAAA IPv6 address = 2001:503:d414::30
```
* org:
```
> org.
╤хЁтхЁ:  [198.41.0.4]
Address:  198.41.0.4

org     nameserver = a0.org.afilias-nst.info
org     nameserver = a2.org.afilias-nst.info
org     nameserver = b0.org.afilias-nst.org
org     nameserver = b2.org.afilias-nst.org
org     nameserver = c0.org.afilias-nst.info
org     nameserver = d0.org.afilias-nst.org
a0.org.afilias-nst.info internet address = 199.19.56.1
a2.org.afilias-nst.info internet address = 199.249.112.1
b0.org.afilias-nst.org  internet address = 199.19.54.1
b2.org.afilias-nst.org  internet address = 199.249.120.1
c0.org.afilias-nst.info internet address = 199.19.53.1
d0.org.afilias-nst.org  internet address = 199.19.57.1
a0.org.afilias-nst.info AAAA IPv6 address = 2001:500:e::1
a2.org.afilias-nst.info AAAA IPv6 address = 2001:500:40::1
b0.org.afilias-nst.org  AAAA IPv6 address = 2001:500:c::1
b2.org.afilias-nst.org  AAAA IPv6 address = 2001:500:48::1
c0.org.afilias-nst.info AAAA IPv6 address = 2001:500:b::1
d0.org.afilias-nst.org  AAAA IPv6 address = 2001:500:f::1
```
* ru:
```
> ru.
╤хЁтхЁ:  [198.41.0.4]
Address:  198.41.0.4

ru      nameserver = a.dns.ripn.net
ru      nameserver = e.dns.ripn.net
ru      nameserver = f.dns.ripn.net
ru      nameserver = d.dns.ripn.net
ru      nameserver = b.dns.ripn.net
a.dns.ripn.net  internet address = 193.232.128.6
a.dns.ripn.net  AAAA IPv6 address = 2001:678:17:0:193:232:128:6
e.dns.ripn.net  internet address = 193.232.142.17
e.dns.ripn.net  AAAA IPv6 address = 2001:678:15:0:193:232:142:17
f.dns.ripn.net  internet address = 193.232.156.17
f.dns.ripn.net  AAAA IPv6 address = 2001:678:14:0:193:232:156:17
d.dns.ripn.net  internet address = 194.190.124.17
d.dns.ripn.net  AAAA IPv6 address = 2001:678:18:0:194:190:124:17
b.dns.ripn.net  internet address = 194.85.252.62
b.dns.ripn.net  AAAA IPv6 address = 2001:678:16:0:194:85:252:62
```
## 6.
Разрешаем cs.usu.edu.ru.
1. Находимся в (ROOT) = a.root-servers.net. = 198.41.0.4, спрашиваем у него, получаем список авторитетных серверов имен и их IP адреса.
2. Переходим в домен (RU) = a.dns.ripn.net = 193.232.128.6, спрашиваем, получаем список nameserver'ов и IP.
3. Переходим в домен (EDU.RU) = ns.informika.RU = 194.226.215.65, получаем только доменные имена, без IP адресов.
4. Чтобы разрешить cs.usu нужно послать запрос на ns.urgu.org (например), но IP его сейчас не известен, поэтому переходим к корневому серверу и пытаемся разрешить ns.urgu.org.
    1. (ROOT) = a.root-servers.net. = 198.41.0.4
    2. (ORG) = a0.org.afilias-nst.info = 199.19.56.1 
    3. OK: разрешили ns.urgu.org в 212.193.68.254
5. Переходим на урфушный nameserver ns.urgu.org, пытаемся разрешить исходное имя - ОК: 212.193.68.254 (то же, что у ns.urgu.org).
6. Делаем проверку, что все сделали правильно: обращаемся к резолверу универа, полученному по DHCP (10.98.241.10). Да, IP совпадают.

Разрешаем www.imm.uran.ru.
1. (ROOT) = a.root-servers.net. = 198.41.0.4
2. (RU) = a.dns.ripn.net = 193.232.128.6
3. set type=ns и получаем ns.uran.ru = 195.19.137.69
4. OK:  195.19.137.125 (разрешили исходное имя)

Разрешаем kma.imkn.urfu.ru.
// Можно быстро разрешить в 212.193.66.79, т.к. сервер имен urGu из первого задания оказался рекурсивным резолвером.
1. 1,2 те же
2. set type=ns, получаем ns1.URFU.RU = 212.193.66.21
3. OK: 212.193.66.79
## 7.
* edu.ru.:
Чтобы получить записи этой зоны, перейдем на ее nameserver: ns2.informika.RU. [edu.md](edu.md)
* urfu.ru. и mail.ru.:
Сервера имен доменов отказывают в передаче зоны. Даже при запросе записей только одного типа, nslookup выполняет AXFR запрос (запрос всей зоны), поэтому -t A и -t MX тоже не проходят.
## 8.
* ya.ru.:
```
> ya.ru.
╤хЁтхЁ:  [213.180.193.1]
Address:  213.180.193.1

ya.ru
        primary name server = ns1.yandex.ru
        responsible mail addr = sysadmin.yandex.ru
        serial  = 2018031600
        refresh = 900 (15 mins)
        retry   = 600 (10 mins)
        expire  = 2592000 (30 days)
        default TTL = 900 (15 mins)
ya.ru   nameserver = ns1.yandex.ru
ya.ru   nameserver = ns2.yandex.ru
ns1.yandex.ru   internet address = 213.180.193.1
ns2.yandex.ru   internet address = 93.158.134.1
ns1.yandex.ru   AAAA IPv6 address = 2a02:6b8::1
ns2.yandex.ru   AAAA IPv6 address = 2a02:6b8:0:1::1
```
* urfu.ru.:
```
> urfu.ru.
╤хЁтхЁ:  family.dns.yandex.ru
Address:  77.88.8.7

Не заслуживающий доверия ответ:
urfu.ru
        primary name server = ns1.urfu.ru
        responsible mail addr = hostmaster.urfu.ru
        serial  = 2012091861
        refresh = 3600 (1 hour)
        retry   = 1800 (30 mins)
        expire  = 2419200 (28 days)
        default TTL = 3600 (1 hour)
```
* mail.ru.:
```
> mail.ru.
╤хЁтхЁ:  family.dns.yandex.ru
Address:  77.88.8.7

Не заслуживающий доверия ответ:
mail.ru
        primary name server = ns1.mail.ru
        responsible mail addr = hostmaster.mail.ru
        serial  = 3312856144
        refresh = 900 (15 mins)
        retry   = 900 (15 mins)
        expire  = 604800 (7 days)
        default TTL = 60 (1 min)
```
## 9
