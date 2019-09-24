---
title: Заметки о выпуске обновления 1 для Службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте об улучшениях, исправлениях и известных проблемах в обновлении 1 для Службы приложений в Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 7be74701b3e25658258abc7102668346e584ab39
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974828"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Заметки о выпуске обновления 1 для Службы приложений Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описаны улучшения, исправления и известные проблемы в обновлении 1 для Службы приложений Azure в Azure Stack. Известные проблемы можно разделить на три категории: проблемы, которые непосредственно относятся к процессу развертывания, проблемы с обновлением и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать Службу приложений Azure, примените обновление 1802 к интегрированной системе Azure Stack или разверните Пакет средств разработки Azure Stack последней версии.

## <a name="build-reference"></a>Указание сборки

Номер сборки обновления 1 для Службы приложений Azure в Azure Stack — **69.0.13698.9**.

### <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> Теперь для новых развертываний Службы приложений Azure в Azure Stack нужен [трехсубъектный групповой сертификат](azure-stack-app-service-before-you-get-started.md#get-certificates). Это связано с улучшением обработки единого входа для Kudu в Службе приложений Azure. Новый субъект — **\*.sso.appservice.\<регион\>.\<доменное_имя\>.\<расширение\>**

Ознакомьтесь с [предварительными условиями для развертывания Службы приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md) перед началом развертывания.

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Пакет обновлений 1 службы приложений Azure в Azure Stack включает следующие улучшения и исправления:

- **Высокий уровень доступности Службы приложений Azure**. Рабочие нагрузки с обновлением Azure Stack 1802 поддерживают развертывание в доменах сбоя, что обеспечивает отказоустойчивость инфраструктуры Службы приложений, так как она развертывается в доменах сбоя. По умолчанию эта возможность реализована во всех новых развертываниях Службы приложений Azure. Сведения о развертываниях, выполненных до применения обновления 1802 Azure Stack, см. в [документации по доменам сбоя Службы приложений](azure-stack-app-service-before-you-get-started.md).

- **Развертывание в существующей виртуальной сети**. Теперь клиенты могут развертывать службу приложений в Azure Stack в существующей виртуальной сети. Развертывание в существующей виртуальной сети позволяет клиентам подключаться через частные порты к SQL Server и файловому серверу, которые требуются для работы Службы приложений Azure. Во время развертывания клиенты могут выбрать развертывание в существующей виртуальной сети, но перед этим им [нужно создать подсети для использования Службой приложений](azure-stack-app-service-before-you-get-started.md#virtual-network).

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Они согласованы с версией пакета SDK для портала Azure Stack.

- Обновления **среды выполнения Функций Azure** до версии **v1.0.11388**.

- **Реализованы обновления следующих исполняющих сред и инструментов**:
    - Добавлена поддержка **.NET Core 2.0**.
    - Добавлены такие версии **Node.JS**:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Добавлены такие версии **NPM**:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Добавлены обновления **PHP**:
        - 5.6.32
        - 7.0.26 (x86 и x64)
        - 7.1.12 (x86 и x64)
    - Обновлено **Git для Windows** для версии 2.14.1.
    - Обновлено **Mercurial** для версии 4.5.0.

  - Добавлена поддержка параметра **Только HTTPS** в функции "Личные домены" на портале клиента службы приложений.

  - Добавлена возможность проверки подключения к хранилищу в пользовательском средстве выбора хранилищ для Функций Azure.

#### <a name="fixes"></a>Исправления

- При создании автономного пакета развертывания клиенты больше не будут получать сообщение об ошибке отказа в доступе при открытии папки в установщике Службы приложений.

- Устранены проблемы при работе с функцией личных доменов на портале клиента Службы приложений.

- Теперь пользователи не могут использовать зарезервированные имена администраторов во время установки.

- Включено развертывание Службы приложений с **присоединенным к домену** файловым сервером.

- Оптимизирована процедура получения корневого сертификата Azure Stack в скрипте и добавлена возможность проверки корневого сертификата в установщике Службы приложений.

- Исправлена ошибка, при которой в Azure Resource Manager возвращалось неправильное состояние при удалении подписки, содержащей ресурсы в пространстве имен Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Известные проблемы с процессом развертывания

- Ошибки при проверке сертификата.

    У некоторых клиентов при развертывании интегрированных систем возникали проблемы с предоставлением сертификатов для установщика Службы приложений, связанные с чрезмерно строгими ограничениями проверки в установщике. Теперь выпущена новая версия установщика Службы приложений, и клиентам следует [скачать обновленный установщик](https://aka.ms/appsvconmasinstaller). Если и с обновленным установщиком у вас возникнут проблемы при проверке сертификатов, обратитесь в службу поддержки.

- Ошибка при извлечении корневого сертификата Azure Stack из интегрированной системы

    Ошибка в Get-AzureStackRootCert.ps1 приводила к тому, что клиентам не удавалось извлечь корневой сертификат Azure Stack при выполнении скрипта на компьютере без корневого сертификата. Выпущена новая версия этого скрипта, в которой эта проблема устранена. [Скачайте обновленную версию вспомогательных скриптов](https://aka.ms/appsvconmashelpers). Если и с обновленным скриптом у вас возникнут проблемы при извлечении корневого сертификата, обратитесь в службу поддержки.

### <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления

- Нет известных проблем, возникающих при обновлении пакета обновления 1 службы приложений Azure в Azure Stack.

### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

- Не работает переключение слотов.

В этом выпуске неправильно функционировало переключение слотов сайта. Чтобы восстановить эту функциональность, выполните следующие действия:

1. Измените параметры группы безопасности сети ControllersNSG, чтобы **разрешить** подключения удаленного рабочего стола к экземплярам контроллера службы приложений. Замените имя AppService.local именем группы ресурсов, в которой вы развернули службу приложений.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Найдите **CN0-VM** в группе виртуальных машин на портале администратора Azure Stack и щелкните **Подключиться**, чтобы открыть сеанс удаленного рабочего стола с экземпляром контроллера. Используйте учетные данные, указанные при развертывании службы приложений.
3. Запустите **PowerShell с правами администратора** и выполните следующий скрипт:

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Закройте сеанс удаленного рабочего стола.
5. Верните прежние параметры группы безопасности сети ControllersNSG, которые **запрещают** подключения удаленного рабочего стола к экземплярам контроллера службы приложений. Замените имя AppService.local именем группы ресурсов, в которой вы развернули службу приложений.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Рабочим ролям не удается связаться с файловым сервером, если служба приложений развернута в существующей виртуальной сети и файловый сервер доступен только в частной сети.

Если вы решили выполнить развертывание в существующей виртуальной сети с использованием внутреннего IP-адреса для подключения к файловому серверу, необходимо добавить правило безопасности для исходящего трафика, разрешающее передачу трафика SMB между подсетью рабочей роли и файловым сервером. Для этого перейдите к группе безопасности сети WorkersNsg на портале администрирования и добавьте правило безопасности для исходящего трафика со следующими свойствами.

- Источник: Любой
- Диапазон исходных портов: *.
- Назначение: IP-адреса
- Диапазон конечных IP-адресов: диапазон IP-адресов для файлового сервера
- Диапазон конечных портов: 445
- Протокол: TCP
- Действие: РАЗРЕШИТЬ
- Приоритет: 700
- Имя: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Известные проблемы, с которыми сталкиваются облачные администраторы, работающие со службой приложений Azure в Azure Stack

Обратитесь к документации в разделе [Обновление 1802 Azure Stack](azure-stack-update-1903.md).

## <a name="next-steps"></a>Дополнительная информация

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- См. сведения о том, как [подготовиться к развертыванию Службы приложений Azure в Azure Stack](azure-stack-app-service-before-you-get-started.md).
