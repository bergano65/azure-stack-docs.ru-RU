---
title: Поддерживаемые операционные системы на виртуальной машине для Azure Stack | Документация Майкрософт
description: В Azure Stack можно использовать эти операционные системы на виртуальной машине.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: fc3eecb8f4e06ce02737e513c48d946f2a667c21
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298769"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Поддерживаемые операционные системы на виртуальной машине для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="windows"></a>Windows

Azure Stack поддерживает гостевые операционные системы Windows, перечисленные в таблице ниже.

| Операционная система | ОПИСАНИЕ | Наличие в marketplace |
| --- | --- | --- |
| Windows Server, версия1709 | 64-разрядная | Core с контейнерами |
| Windows Server 2019 | 64-разрядная |  Центр обработки данных, ядро центра обработки данных, центр обработки данных с контейнерами |
| Windows Server 2016 | 64-разрядная |  Центр обработки данных, ядро центра обработки данных, центр обработки данных с контейнерами |
| Windows Server 2012 R2 | 64-разрядная |  Центр обработки данных |
| Windows Server 2012 | 64-разрядная |  Центр обработки данных |
| Windows Server 2008 R2 с пакетом обновления 1 | 64-разрядная |  Центр обработки данных |
| Windows Server 2008 с пакетом обновления 2 (SP2) | 64-разрядная |  Использование собственного образа |
| Windows 10 *(см. примечание 1)* | 64-разрядная версия, Pro и Корпоративная | Использование собственного образа |

> [!NOTE]
> Чтобы развернуть клиентские операционные системы Windows 10 в Azure Stack требуются [лицензии Windows на каждого пользователя](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx). Лицензии также можно приобрести у уполномоченного поставщика услуг многоклиентного размещения ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Образы из Marketplace доступны для лицензирования по схеме "оплата по мере использования" или BYOL (EA или SPLA). Использование обеих схем в одном экземпляре Azure Stack не поддерживается. Во время развертывания Azure Stack внедряет подходящую версию гостевого агента в образ.

Выпуски Datacenter доступны для скачивания в marketplace. Клиенты могут использовать собственные образы серверов, включая другие выпуски. В Marketplace недоступны клиентские образы версий Windows.

## <a name="linux"></a>Linux

Перечисленные дистрибутивы Linux, доступные в Marketplace, включают в себя необходимый агент Linux для Microsoft Azure (WALA). Если вы используете в Azure Stack собственный образ, следуйте указаниям в разделе [Добавление образов Linux в Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Пользовательские образы должны содержать последнюю общедоступную версию WALA (на основе сборки Azure Stack 1903 и последующих версий или с исправлениями 1901 и 1902) или версию 2.2.20. Версии, предшествующие версии 2.2.20 и версиям с 2.2.21 по 2.2.34 (не включительно) могут неправильно работать в Azure Stack. В выпуске Azure Stack 1910 и более поздних версиях все версии агента Azure WALA будут работать с Azure Stack.
>
> [Cloud-init](https://cloud-init.io/) в настоящее время поддерживается в Azure Stack версии 1910 и более поздних.

| Дистрибутив | ОПИСАНИЕ | ИЗДАТЕЛЬ | Marketplace |
| --- | --- | --- | --- |
| Версия 6.9 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Версия 7.5 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Версия 7.3 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| ClearLinux | 64-разрядная | ClearLinux.org | Yes |
| CoreOS Linux (стабильная версия) |  64-разрядная | CoreOS | Yes |
| Debian 8 "Jessie" | 64-разрядная | credativ |  Yes |
| Debian 9 "Stretch" | 64-разрядная | credativ | Yes |
| Oracle Linux | 64-разрядная | Oracle | Yes |
| Red Hat Enterprise Linux 7.1 (и последующих версий) | 64-разрядная | Red Hat | Использование собственного образа |
| SLES 11SP4 | 64-разрядная | SUSE | Yes |
| SLES 12SP3 | 64-разрядная | SUSE | Yes |
| Ubuntu 14.04-LTS | 64-разрядная | Canonical | Yes |
| Ubuntu 16.04-LTS | 64-разрядная | Canonical | Yes |
| Ubuntu 18.04-LTS | 64-разрядная | Canonical | Yes |

Сведения о поддержке Red Hat Enterprise Linux приведены в статье [Red Hat и Azure Stack: часто задаваемые вопросы](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure Stack Marketplace см. в следующих статьях:

- [Скачивание элементов Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Создание и публикация элемента Marketplace](azure-stack-create-and-publish-marketplace-item.md)
