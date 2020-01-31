---
title: Использование iDNS в Azure Stack Hub
description: Узнайте, как использовать компоненты и возможности iDNS в Azure Stack Hub.
author: Justinha
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3cf7b6cadbefb32359e6104bd7b5a3c7851e73f7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883123"
---
# <a name="use-idns-in-azure-stack-hub"></a>Использование iDNS в Azure Stack Hub 

iDNS — это функция сетевого взаимодействия Azure Stack Hub, которая позволяет сопоставлять внешние имена DNS (например, https:\//www.bing.com.) и регистрировать имена внутренней виртуальной сети. Таким образом можно разрешать виртуальные машины в одной и той же виртуальной сети по имени, а не по IP-адресу. Это избавляет от необходимости предоставлять пользовательские записи DNS-сервера. Общие сведения о DNS см. в [этой статье](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Возможности iDNS

С iDNS в Azure Stack Hub вы получаете следующие возможности без необходимости указывать пользовательские записи DNS-сервера:

- Общие службы разрешения DNS-имен для клиентских рабочих нагрузок.
- Полномочная служба DNS для разрешения имен и регистрации DNS в клиентской виртуальной сети.
- Рекурсивная служба DNS для разрешения имен Интернета из клиентских виртуальных машин. Клиенту больше не требуется указывать пользовательские записи DNS для разрешения имен Интернета (например, www\.bing.com).

Вы по-прежнему можете подключить собственный DNS-сервер и использовать пользовательские DNS-серверы. Однако с помощью iDNS можно разрешать DNS-имена в Интернете и подключаться к другим виртуальным машинам в той же виртуальной сети, не создавая пользовательские записи DNS.

## <a name="what-doesnt-idns-do"></a>Что не делает iDNS?

iDNS не позволяет создать запись DNS для имени, которое можно разрешить за пределами виртуальной сети.

В Azure можно указать метку имени DNS, которая связана с общедоступным IP-адресом. Вы можете выбрать метку (префикс), но Azure выбирает суффикс, зависящий от региона, в котором создается общедоступный IP-адрес.

![Пример метки имени DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

На приведенном выше рисунке показано создание платформой Azure записи "A" в DNS для метки имени DNS, указанной в зоне **westus.cloudapp.azure.com**. Вместе префикс и суффикс образуют [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), которое можно разрешить в любом месте в общедоступном Интернете.

В Azure Stack Hub iDNS поддерживается только для внутренней регистрации имен, поэтому iDNS не позволяет выполнить следующие действия:

- создать запись DNS в существующей размещенной зоне DNS (например, local.azurestack.external);
- создать зону DNS (например, Contoso.com);
- создать запись в пользовательской зоне DNS;
- купить доменное имя.

## <a name="demo-of-how-idns-works"></a>Демонстрация работы iDNS

Все имена узлов для виртуальных машин в виртуальных сетях хранятся в виде записей ресурсов DNS в одной зоне, но в отдельных уникальных секциях с некоторыми идентификаторами GUID, которые соответствуют идентификаторам виртуальных сетей в инфраструктуре SDN, в которых развернуты эти виртуальные машины. Полные доменные имена (FQDN) клиентских виртуальных машин состоят из строковых значений имени компьютера и DNS-суффикса виртуальной сети в формате GUID.

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
Ниже показана простая лаборатория для демонстрации этой технологии. Мы создали 3 виртуальных машины в одной виртуальной сети и еще одну виртуальную машину в отдельной виртуальной сети:

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|ВМ    |Виртуальная сеть    |Частный IP-адрес   |Общедоступный IP-адрес    | DNS-имя                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM-A1 |VNetA   | 10.0.0.5    |172.31.12.68 |VM-A1-Label.lnv1.cloudapp.azscss.external |
|VM-A2 |VNetA   | 10.0.0.6    |172.31.12.76 |VM-A2-Label.lnv1.cloudapp.azscss.external |
|VM-A3 |VNetA   | 10.0.0.7    |172.31.12.49 |VM-A3-Label.lnv1.cloudapp.azscss.external |
|VM-B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-Label.lnv1.cloudapp.azscss.external |
 
 
|Виртуальная сеть  |GUID                                 |DNS suffix string                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|VNetA |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local|
 
 
Вы можете выполнить тесты разрешения имен, чтобы лучше понимать работу iDNS.

<!--- why Linux?--->

На виртуальной машине VM-A1 (Linux) выполните поиск имени VM-A2. Вы увидите, что для VNetA добавляется DNS-суффикс и полученное имя разрешается в частный IP-адрес:
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
Поиск VM-A2-Label без указания полного доменного имени ожидаемо завершается сбоем:

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

Если вы предоставите FQDN для метки DNS, имя будет разрешено в общедоступный IP-адрес:

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
Попытка разрешить VM-B1 (которая находится в другой виртуальной сети) завершается сбоем, так как в этой зоне такой записи не существует.

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

Использование полного доменного имени для VM-B1 не поможет, так как эта запись относится к другой зоне.

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
Если вы предоставите FQDN для метки DNS, имя разрешается успешно:

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
При выполнении поиска на виртуальной машине VM-A3 (Windows) обратите внимание на разницу между заслуживающим и не заслуживающим доверия ответами.

Внутренние записи:

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

Внешние записи:

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
Полученный выше результат можно описать так:
 
*   Каждая виртуальная сеть имеет собственную зону с записями A для всех частных IP-адресов, которые состоят из имени виртуальной машины и DNS-суффикса виртуальной сети (который совпадает с ее GUID).
    *   \<vmname>.\<vnetGUID\>.internal.\<region>.\<stackinternalFQDN>
    *   Все это выполняется автоматически
*   Если вы используете общедоступные IP-адреса, для них можно дополнительно создать метки DNS. Они разрешаются так же, как любые другие внешние адреса.
 
 
- Серверы iDNS считаются полномочными серверами для внутренних зон DNS, а также выполняют роль распознавателя для общедоступных имен, когда клиентские виртуальные машины пытаются подключиться к внешним ресурсам. При получении запроса на адрес внешнего ресурса, серверы iDNS перенаправляют его для разрешения полномочным DNS-серверам.
 
Как показала работа в лаборатории, вы можете контролировать используемый IP-адрес. Если вы используете имя виртуальной машины, то получите частный IP-адрес, а для метки DNS — общедоступный IP-адрес.

## <a name="next-steps"></a>Дальнейшие действия

[Использование DNS в Azure Stack Hub](azure-stack-dns.md)
