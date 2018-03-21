# nslookup (практика)
# Белых Владислав кн-202

1. OK
2.
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
3.
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
4.
* server NAME работает так:
Если в качестве NAME указан IP адрес (```> server 194.226.235.1```), команда посылает запрос [1.235.226.194.in-addr.arpa: type PTR, class IN], а затем независимо был ли получен ответ от сервера или нет, фиксирует IP указанного сервера.
Если в качестве NAME указано доменное имя (```> server ns1.urfu.ru```), команда пытается разрешить его в IP адрес и посылает запрос указанному ранее DNS серверу (в данном случае 194.226.235.1). В случае неудачи, сервер NAME не фиксируется в качестве резолвера.
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
