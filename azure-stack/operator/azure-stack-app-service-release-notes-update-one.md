---
title: Заметки о выпуске обновления 1 для службы приложений в Azure Stack | Документация Майкрософт
description: Узнайте о том, что находится в пакете обновлений 1 службы приложений в Azure Stack, об известных проблемах и где можно загрузить обновление.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digmobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 86eb23e57516bc108557bbdc4f2d131153f5e855
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306488"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Служба приложений в заметках о выпуске обновления 1 Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этих заметках о выпуске описываются улучшения и исправления в пакете обновлений 1 службы приложений Azure в Azure Stack и известные проблемы. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу развертывания, обновления, и проблемы со сборкой (после установки).

> [!IMPORTANT]
> Прежде чем развертывать Cлужбу приложений Azure, примените обновление 1802 к интегрированной системе Azure Stack или разверните последний пакет средств разработки Azure Stack.
>
>

## <a name="build-reference"></a>Указание сборки

Номер сборки пакета обновлений 1 службы приложений в Azure Stack — **69.0.13698.9**.

### <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> Теперь для новых развертываний службы приложений Azure в Azure Stack нужен [трехсубъектный групповой сертификат](azure-stack-app-service-before-you-get-started.md#get-certificates). Это связано с улучшением обработки единого входа для Kudu в службе приложений Azure. Новый субъект — **\*.sso.appservice.\<регион\>.\<доменное_имя\>.\<расширение\>**
>
>

Перед началом развертывания ознакомьтесь со статьей [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Новые функции и исправления

Пакет обновлений 1 службы приложений Azure в Azure Stack включает следующие улучшения и исправления:

- **Высокий уровень доступности службы приложений Azure**. С обновлением 1802 Azure Stack стало возможным развертывать рабочие нагрузки в доменах сбоя. Поэтому инфраструктура службы приложений может быть отказоустойчивой, так как она будет развернута в доменах сбоя. По умолчанию все новые развертывания службы приложений Azure будут иметь эту возможность, однако для развертываний, завершенных до применения обновления 1802 Azure Stack, обратитесь к [документации по доменам сбоя службы приложений](azure-stack-app-service-before-you-get-started.md ).

- **Развертывание в существующей виртуальной сети**. Теперь клиенты могут развертывать службу приложений в Azure Stack в существующей виртуальной сети. Развертывание в существующей виртуальной сети позволяет клиентам подключаться через частные порты к SQL Server и файловому серверу, которые необходимы для службы приложений Azure. Во время развертывания клиенты могут выбрать развертывание в существующей виртуальной сети, но до этого они [должны создать подсети для использования службой приложений](azure-stack-app-service-before-you-get-started.md#virtual-network).

- Обновления для **клиента, администратора службы приложений, портала функций и средств Kudu**. Согласованы с версией пакета SDK для портала Azure Stack.

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

- Во время создания пакета развертывания в автономном режиме клиенты больше не получат сообщение об ошибке отказа в доступе при открытии папки из установщика службы приложений.

- Устранены проблемы при работе с функцией "Личные домены" на портале клиента службы приложений.

- Теперь пользователи не могут использовать зарезервированные имена администраторов во время установки.

- Включено развертывание службы приложений с **присоединенным к домену** файловым сервером.

- Улучшено получение корневого сертификата Azure Stack в скрипте, и теперь корневой сертификат проверяется в установщике службы приложений.

- Исправлена ошибка, при которой в Azure Resource Manager возвращалось неправильное состояние при удалении подписки, содержащей ресурсы в пространстве имен Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Известные проблемы с процессом развертывания

- Ошибки при проверке сертификата

У некоторых клиентов при развертывании интегрированных систем возникали проблемы с предоставлением сертификатов для установщика службы приложений, связанные с чрезмерно строгими ограничениями проверки в установщике. Теперь выпущена новая версия установщика службы приложений, и клиентам следует [скачать обновленный установщик](https://aka.ms/appsvconmasinstaller). Если и с обновленным установщиком у вас возникнут проблемы при проверке сертификатов, обратитесь в службу поддержки.

- Ошибка при извлечении корневого сертификата Azure Stack из интегрированной системы

Ошибка в Get-AzureStackRootCert.ps1 приводила к тому, что клиентам не удавалось извлечь корневой сертификат Azure Stack при выполнении скрипта на компьютере без корневого сертификата. Теперь выпущена новая версия этого скрипта, в которой проблема устранена. Клиентам следует [скачать обновленные вспомогательные скрипты](https://aka.ms/appsvconmashelpers). Если и с обновленным скриптом у вас возникнут проблемы при извлечении корневого сертификата, обратитесь в службу поддержки.

### <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления

- Нет известных проблем, возникающих при обновлении пакета обновления 1 службы приложений Azure в Azure Stack.

### <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

- Не работает переключение слотов

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

2. Найдите **CN0-VM** в группе виртуальных машин на портале администратора Azure Stack и щелкните **Подключиться**, чтобы открыть подключение удаленного рабочего стола к экземпляру контроллера. Используйте учетные данные, указанные при развертывании службы приложений.
3. **Запустите PowerShell с правами администратора** и выполните следующий скрипт:

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Известные проблемы для облачных администраторов, работающих со службой приложений Azure в Azure Stack

Обратитесь к документации в разделе [Обновление 1802 Azure Stack](azure-stack-update-1903.md).

## <a name="next-steps"></a>Дополнительная информация

- Обзор службы приложений Azure в Azure Stack см. в [этой статье](azure-stack-app-service-overview.md).
- Дополнительные сведения о том, как подготовиться к развертыванию службы приложений Azure в Azure Stack, см. в разделе [Подготовка к работе со службой приложений в Azure Stack](azure-stack-app-service-before-you-get-started.md).

<!-- Update_Description: wording update -->