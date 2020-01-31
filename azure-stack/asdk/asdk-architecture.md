---
title: Архитектура ASDK
description: Узнайте об архитектуре Пакета средств разработки Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 842dab690d8239ca0c4db7622fe7a0bb18347a0c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874150"
---
# <a name="asdk-architecture"></a>Архитектура ASDK
Пакет средств разработки Azure Stack (ASDK) — это развертывание Azure Stack с использованием одного узла, выполняемое на одном главном компьютере. Компоненты пограничной маршрутизации устанавливаются на главном компьютере для обеспечения возможностей преобразования сетевых адресов (NAT) и VPN в Azure Stack. Инфраструктурные роли Azure Stack выполняются на физическом главном компьютере в слое Hyper-V.


## <a name="virtual-machine-roles"></a>Роли виртуальной машины
Пакет ASDK предлагает службы, которые используют следующие виртуальные машины на компьютере, где размещен пакет средств разработки:

| Имя | Описание |
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
| **AzS-WAS01** | Службы Azure Resource Manager и портал администратора Azure Stack.|
| **AzS-WASP01**| Портал пользователя (клиента) Azure Stack и службы Azure Resource Manager.|
| **AzS-XRP01** | Контроллер управления инфраструктурой для Microsoft Azure Stack, включая поставщиков вычислительных и сетевых ресурсов, а также ресурсов хранения.|
| **AzS-SRNG01** | Поддержка виртуальной машины для вызова, в которой размещена служба сбора журналов для Azure Stack. |

## <a name="next-steps"></a>Дальнейшие действия
[Узнайте об основных задачах администрирования ASDK](asdk-admin-basics.md).
