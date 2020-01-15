---
title: Поддерживаемые гостевые операционные системы для Azure Stack Hub | Документация Майкрософт
description: В Azure Stack Hub можно использовать следующие гостевые операционные системы.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 431af9688eb4f97e3c5400e9fe2b00c0ff18ec11
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727519"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Поддерживаемые гостевые операционные системы для Azure Stack Hub

*Область применения: интегрированные системы Azure Stack Hub и Пакет средств разработки Azure Stack*

## <a name="windows"></a>Windows

Azure Stack Hub поддерживает гостевые операционные системы Windows, перечисленные в таблице ниже.

| Операционная система | Description | Наличие в marketplace |
| --- | --- | --- |
| Windows Server, версия1709 | 64-разрядная | Core с контейнерами |
| Windows Server 2019 | 64-разрядная |  Центр обработки данных, ядро центра обработки данных, центр обработки данных с контейнерами |
| Windows Server 2016 | 64-разрядная |  Центр обработки данных, ядро центра обработки данных, центр обработки данных с контейнерами |
| Windows Server 2012 R2 | 64-разрядная |  Центр обработки данных |
| Windows Server 2012 | 64-разрядная |  Центр обработки данных |
| Windows Server 2008 R2 с пакетом обновления 1 (SP1) | 64-разрядная |  Центр обработки данных |
| Windows Server 2008 с пакетом обновления 2 (SP2) | 64-разрядная |  Использование собственного образа |
| Windows 10 *(см. примечание 1)* | 64-разрядная версия, Pro и Корпоративная | Использование собственного образа |

> [!NOTE]
> Чтобы развернуть клиентские операционные системы Windows 10 в Azure Stack Hub требуются [лицензии Windows для каждого пользователя](https://www.microsoft.com/licensing/product-licensing/windows10.aspx). Лицензии также можно приобрести у уполномоченного поставщика услуг мультитенантного размещения ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Образы из Marketplace доступны для лицензирования по схеме "оплата по мере использования" или BYOL (EA или SPLA). Использование обеих схем в одном экземпляре Azure Stack Hub не поддерживается. Во время развертывания Azure Stack Hub внедряет в образ подходящую версию гостевого агента.

Выпуски Datacenter доступны для скачивания в marketplace. Клиенты могут использовать собственные образы серверов, включая другие выпуски. В Marketplace недоступны клиентские образы версий Windows.

## <a name="linux"></a>Linux

Перечисленные дистрибутивы Linux, доступные в Marketplace, включают в себя необходимый агент Linux для Microsoft Azure (WALA). Если вы используете в Azure Stack Hub собственный образ, следуйте инструкциям по [добавлению образов Linux в Azure Stack Hub](azure-stack-linux.md).

> [!NOTE]
> Пользовательские образы должны содержать последнюю общедоступную версию WALA (на основе сборки Azure Stack Hub 1903 и выше или с исправлениями 1901 и 1902) или версию 2.2.20. Версии до 2.2.20 и версии 2.2.21–2.2.34 (не включительно) могут неправильно работать в Azure Stack Hub. В выпуске Azure Stack Hub 1910 и выше все версии агента Azure WALA работают с Azure Stack.
>
> [cloud-init](https://cloud-init.io/) поддерживается в Azure Stack 1910 и выше.

| Distribution | Description | Издатель | Marketplace |
| --- | --- | --- | --- |
| Версия 6.9 на основе CentOS | 64-разрядная | Rogue Wave | Да |
| Версия 7.5 на основе CentOS | 64-разрядная | Rogue Wave | Да |
| Версия 7.3 на основе CentOS | 64-разрядная | Rogue Wave | Да |
| ClearLinux | 64-разрядная | ClearLinux.org | Да |
| CoreOS Linux (стабильная версия) |  64-разрядная | CoreOS | Да |
| Debian 8 "Jessie" | 64-разрядная | credativ |  Да |
| Debian 9 "Stretch" | 64-разрядная | credativ | Да |
| Oracle Linux | 64-разрядная | Oracle; | Да |
| Red Hat Enterprise Linux 7.1 (и последующих версий) | 64-разрядная | Red Hat | Использование собственного образа |
| SLES 11SP4 | 64-разрядная | SUSE | Да |
| SLES 12SP3 | 64-разрядная | SUSE | Да |
| Ubuntu 14.04-LTS | 64-разрядная | Canonical | Да |
| Ubuntu 16.04-LTS | 64-разрядная | Canonical | Да |
| Ubuntu 18.04-LTS | 64-разрядная | Canonical | Да |

Сведения о поддержке Red Hat Enterprise Linux приведены в статье [Red Hat и Azure Stack: Red Hat и Azure Stack](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Дальнейшие действия

См. сведения об Azure Stack Hub Marketplace в следующих статьях:

- [Скачивание элементов Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)
