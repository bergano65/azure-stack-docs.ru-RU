---
title: Архитектура ASDK | Документация Майкрософт
description: Узнайте об архитектуре Пакета средств разработки Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 5a34061b1fa6cd30f3bbf9f9780b13c01f0a4866
ms.sourcegitcommit: 4eb1766c7a9d1ccb1f1362ae1211ec748a7d708c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69579099"
---
# <a name="asdk-architecture"></a>Архитектура ASDK
Пакет средств разработки Azure Stack (ASDK) — это развертывание Azure Stack с использованием одного узла, выполняемое на одном главном компьютере. Компоненты пограничной маршрутизации устанавливаются на главном компьютере для обеспечения возможностей преобразования сетевых адресов (NAT) и VPN в Azure Stack. Инфраструктурные роли Azure Stack выполняются на физическом главном компьютере в слое Hyper-V.


## <a name="virtual-machine-roles"></a>Роли виртуальной машины
Пакет ASDK предлагает службы, которые используют следующие виртуальные машины на компьютере, где размещен пакет средств разработки:

| ИМЯ | ОПИСАНИЕ |
| ----- | ----- |
| **AzS-ACS01** | Службы хранилища Azure Stack.|
| **AzS-ADFS01** | Службы федерации Active Directory (AD FS).  |
| **AzS-CA01** | Службы центра сертификации для служб ролей Azure Stack.|
| **AzS-DC01** | Службы Active Directory, DNS и DHCP для Microsoft Azure Stack.|
| **AzS-ERCS01** | Виртуальная машина консоли аварийного восстановления. |
| **AzS-GWY01** | Службы пограничных шлюзов, включая VPN-подключения типа "сеть — сеть" для сетей клиентов.|
| **AzS-NC01** | Сетевой контроллер, который управляет сетевыми службами Azure Stack.  |
| **AzS-SLB01** | Службы мультиплексора балансировки нагрузки в Azure Stack для клиентов и служб инфраструктуры Azure Stack.  |
| **AzS-SQL01** | Внутреннее хранилище данных для ролей инфраструктуры Azure Stack.  |
| **AzS-WAS01** | Портал администрирования Azure Stack и службы Azure Resource Manager.|
| **AzS-WASP01**| Портал пользователя (клиента) Azure Stack и службы Azure Resource Manager.|
| **AzS-XRP01** | Контроллер управления инфраструктурой для Microsoft Azure Stack, включая поставщиков вычислительных и сетевых ресурсов, а также ресурсов хранения.|
| **AzS-SRNG01** | Поддержка виртуальной машины для вызова, в которой размещена служба сбора журналов для Azure Stack. |

## <a name="next-steps"></a>Дополнительная информация
[Узнайте об основных задачах администрирования ASDK](asdk-admin-basics.md).
