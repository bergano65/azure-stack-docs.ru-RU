---
title: Применение обновления изготовителя оборудования к Azure Stack | Документация Майкрософт
description: Узнайте, как применить обновление изготовителя оборудования к Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 73671431c70960fa517ee83c68945f14e2621b46
ms.sourcegitcommit: 9cb82df1eccb0486bcabec0bd674162d4820c00c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060190"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>Применение обновлений изготовителя оборудования для Azure Stack

*Область применения: интегрированные системы Azure Stack*

Вы можете применять обновления изготовителя оборудования к аппаратным компонентам Azure Stack, чтобы устанавливать улучшения драйверов и встроенного ПО, а также исправления безопасности, минимально влияя на работу пользователей. В этой статье описываются обновления изготовителя оборудования, приведены контактные данных изготовителей оборудования и рассматривается применение обновлений.

## <a name="overview-of-oem-updates"></a>Обзор обновлений изготовителя оборудования

Помимо обновлений Microsoft Azure Stack, многие изготовители оборудования также выпускают регулярные обновления для оборудования Azure Stack, например обновления драйверов и встроенного ПО. Они называются **обновлениями пакета изготовителя оборудования**. Чтобы узнать, выпускает ли изготовитель оборудования обновления пакета, обратитесь к [документации по Azure Stack от изготовителя оборудования](#oem-contact-information).

Эти обновления пакета передаются в учетную запись хранения **updateadminaccount** и применяются через портал администрирования Azure Stack. Дополнительные сведения см. в разделе [Применение обновлений для изготовителей оборудования](#apply-oem-updates).

Обратитесь к своему изготовителю оборудования, чтобы убедиться, что в рамках используемой им процедуры уведомления ваша организация будет получать уведомления об обновлениях пакета изготовителя оборудования.

Некоторым поставщикам оборудования может потребоваться *виртуальная машина поставщика оборудования*, которая выполняет внутренний процесс обновления встроенного ПО. Дополнительные сведения см. в разделе [Настройка виртуальной машины поставщика оборудования](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>Контактные данные изготовителей оборудования 

В этом разделе содержатся контактные данные изготовителей оборудования и ссылки на справочные материалы по Azure Stack от изготовителя оборудования.

| Партнер по оборудованию | Регион | URL-адрес |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Все | [Поддержка и обновления встроенного ПО для Azure Stack для Cisco — автоматическое уведомление (требуется учетная запись или имя для входа)](https://software.cisco.com/download/redirect?i=!y&mdfid=283862063&softwareid=286320368&release=1.0(0)&os=)<br><br>[Заметки о выпуске интегрированной системы Cisco для Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Все | [Облако для Microsoft Azure Stack 14G (требуется учетная запись и имя для входа)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Облако для Microsoft Azure Stack 13G (требуется учетная запись и имя для входа)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Япония | [Служба технической поддержки Fujitsu (требуется учетная запись и имя для входа)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | Европа, Ближний Восток и Африка | [Fujitsu: поддержка продуктов и систем ИТ](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Сайт MySupport Fujitsu (требуется учетная запись и имя для входа)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Все | [HPE ProLiant для Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Все | [Лучшие рецепты для ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Пакет OEM или встроенное ПО](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[Документация по TERRA для Azure Stack (включая FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Применение обновлений для изготовителей оборудования

Установите пакеты изготовителя оборудования, выполнив следующие действия.

1. Потребуется обратиться к изготовителю оборудования для следующего:
      - определить текущую версию пакета изготовителя оборудования;  
      - найти лучший способ скачивания пакета изготовителя оборудования.  
2. Подготовьте пакет изготовителя оборудования, выполнив действия, описанные в разделе [Загрузка пакетов обновлений для интегрированных систем](azure-stack-servicing-policy.md).
3. Установите обновления, выполнив действия, описанные в разделе [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Настройка виртуальной машины поставщика оборудования

Некоторым поставщикам оборудования может потребоваться виртуальная машина для установки обновлений изготовителя оборудования. Поставщик оборудования будет отвечать за создание этих виртуальных машин и составление документации, если при выполнении командлета **Set-OEMExternalVM** вам нужно указать `ProxyVM` или `HardwareManager` для параметра **-VMType**, а также за указание учетных данных, которые нужно задать для параметра **-Credential**. После создания виртуальных машин настройте их, выполнив командлет **Set-OEMExternalVM** на привилегированной конечной точке.

Дополнительные сведения о привилегированной конечной точке в Azure Stack см. в разделе [Использование привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).

1.  Получите доступ к привилегированной конечной точке.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Настройте виртуальную машину поставщика оборудования с помощью командлета **Set-OEMExternalVM**. Командлет проверяет IP-адрес и учетные данные для виртуальной машины, заданной параметром **-VMType** `ProxyVM`. Входные данные **-VMType** `HardwareManager` командлет не проверяет. Параметр **-Credential** для командлета **Set-OEMExternalVM** должен быть четко указан в документации поставщика оборудования.  Это НЕ учетные данные CloudAdmin, используемые для привилегированной конечной точки, или любые другие существующие учетные данные Azure Stack.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Дополнительная информация

[Обновления Azure Stack](azure-stack-updates.md)
